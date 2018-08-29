---
layout: post
title: "泰坦尼克号最佳工作分类器"
date: 2018-06-10 12:00:00 +0800 
categories: DA
tag: [DA]
---   


## 前言

这篇博客翻译自[Titanic best working Classifier](https://www.kaggle.com/sinakhorami/titanic-best-working-classifier)，由于对数据分析诸多概念和工具的使用不明，故而选择有代表性博客，边翻译边运行和理解代码来帮助自己学习。

- 作者进行的特征工程处理帮助我了解特征工程的概念，其实就是对各个可能与生存率相关联的特征进行处理，得到特征本身在不同的范围或者不同的值，比如不同年龄区间，不同性别等对生存几率的影响；
- 作者在对年龄、票价等特征进行处理的方法是划定若干个范围进行分类；
- 数据清洗的目的就是数值化所有特征值，即便是对于像年龄这样带有范围的值，也需要用特定的值来代表某个范围；
- 在这篇 notebook 中，作者在进行拟合与预测时，使用的是 scikit-learn 库，在该库中的 sklearn 模块包含了许多的算法，这样算法都可以进行预测，只是我们需要筛选出预测精度高的算法；

## 介绍

这是我第一个关于机器学习的作品。该笔记本是用 python 编写的，并且受到了 ["Exploring Survival on Titanic" by Megan Risdal, a Kernel in R on Kaggle](https://www.kaggle.com/mrisdal/exploring-survival-on-the-titanic) 的启发。


```python
%matplotlib inline
import numpy as np
import pandas as pd
import re as re

train = pd.read_csv('./input/train.csv',header = 0, dtype={'Age':np.float64})
test = pd.read_csv('./input/test.csv',header = 0,dtype={'Age':np.float64})
full_data = [train,test]

print (train.info())
```

    <class 'pandas.core.frame.DataFrame'>
    RangeIndex: 891 entries, 0 to 890
    Data columns (total 12 columns):
    PassengerId    891 non-null int64
    Survived       891 non-null int64
    Pclass         891 non-null int64
    Name           891 non-null object
    Sex            891 non-null object
    Age            714 non-null float64
    SibSp          891 non-null int64
    Parch          891 non-null int64
    Ticket         891 non-null object
    Fare           891 non-null float64
    Cabin          204 non-null object
    Embarked       889 non-null object
    dtypes: float64(2), int64(5), object(5)
    memory usage: 83.6+ KB
    None
    

## 特征工程

### 1.Pclass

此特征没有缺失值，并且已经是数值。所以让我们来检查它对我们训练集的影响。


```python
print (train[['Pclass','Survived']].groupby(['Pclass'],as_index=False).mean())
```

       Pclass  Survived
    0       1  0.629630
    1       2  0.472826
    2       3  0.242363
    

可参考 pandas.Series.groupby 的用法。

### 2.Sex


```python
print(train[["Sex","Survived"]].groupby(["Sex"],as_index=False).mean())
```

          Sex  Survived
    0  female  0.742038
    1    male  0.188908
    

### 3.SibSp 和 Parch

通过 siblings/spouse 和 children/parents 的数量，我们可以创建一个名为 Family Size 的新特征。


```python
for dataset in full_data:
    dataset['FamilySize'] = dataset['SibSp'] + dataset['Parch'] + 1
print(train[['FamilySize','Survived']].groupby(['FamilySize'],as_index=False).mean())
```

       FamilySize  Survived
    0           1  0.303538
    1           2  0.552795
    2           3  0.578431
    3           4  0.724138
    4           5  0.200000
    5           6  0.136364
    6           7  0.333333
    7           8  0.000000
    8          11  0.000000
    

这似乎对我们的预测有很好的影响，但是让我们更进一步对人们进行分类，看看他们是否独自在这艘船上。


```python
for dataset in full_data:
    dataset['IsAlone'] = 0
    dataset.loc[dataset['FamilySize'] == 1,'IsAlone'] = 1
print(train[['IsAlone','Survived']].groupby(['IsAlone'],as_index=False).mean())
```

       IsAlone  Survived
    0        0  0.505650
    1        1  0.303538
    

很好！影响是相当大的。

### 4.Embarked

Embarked 特征有一些缺失值。我们试着用最多的值 ('S') 来填充它们。


```python
for dataset in full_data:
    dataset['Embarked'] = dataset['Embarked'].fillna('S')
print (train[['Embarked','Survived']].groupby(['Embarked'],as_index=False).mean())
```

      Embarked  Survived
    0        C  0.553571
    1        Q  0.389610
    2        S  0.339009
    

### 5.Fare

Fare 也有一些缺失值，我们将用中位数来代替它。然后，我们将其分成 4 个范围。


```python
for dataset in full_data:
    dataset['Fare'] = dataset['Fare'].fillna(train['Fare'].mean())
train['CategoricalFare'] = pd.qcut(train['Fare'],4)
print(train[['CategoricalFare','Survived']].groupby(['CategoricalFare'],as_index=False).mean())
```

       CategoricalFare  Survived
    0   (-0.001, 7.91]  0.197309
    1   (7.91, 14.454]  0.303571
    2   (14.454, 31.0]  0.454955
    3  (31.0, 512.329]  0.581081
    

### 6.Age

我们在这个特征中有很多缺失值。#生成 (mean - std) 和 (mean + std) 之间的随机数。然后我们将年龄分为 5 个范围。


```python
for dataset in full_data:
    age_avg        = dataset['Age'].mean()
    age_std        = dataset['Age'].std()
    age_null_count = dataset['Age'].isnull().sum()
    
    age_null_random_list = np.random.randint(age_avg - age_std,age_avg + age_std,size=age_null_count)
    dataset['Age'][np.isnan(dataset['Age'])] = age_null_random_list
    dataset['Age'] = dataset['Age'].astype(int)

train['CategoricalAge'] = pd.cut(train['Age'],5)

print (train[['CategoricalAge','Survived']].groupby(['CategoricalAge'],as_index=False).mean())
```

      CategoricalAge  Survived
    0  (-0.08, 16.0]  0.547170
    1   (16.0, 32.0]  0.351474
    2   (32.0, 48.0]  0.371212
    3   (48.0, 64.0]  0.434783
    4   (64.0, 80.0]  0.090909
    

### 7.Name

在这个特征里面我们可以发现人们的头衔。


```python
def get_title(name):
    title_search = re.search(' ([A-Za-z]+)\.',name)
    # 如果标题存在，抽取并返回它
    if title_search:
        return title_search.group(1)
    return ""

for dataset in full_data:
    dataset['Title'] = dataset['Name'].apply(get_title)
    
print(pd.crosstab(train['Title'],train['Sex']))
```

    Sex       female  male
    Title                 
    Capt           0     1
    Col            0     2
    Countess       1     0
    Don            0     1
    Dr             1     6
    Jonkheer       0     1
    Lady           1     0
    Major          0     2
    Master         0    40
    Miss         182     0
    Mlle           2     0
    Mme            1     0
    Mr             0   517
    Mrs          125     0
    Ms             1     0
    Rev            0     6
    Sir            0     1
    

所以我们有了头衔。让我们对它进行分类并检查头衔对生存率的影响。


```python
for dataset in full_data:
    dataset['Title'] = dataset['Title'].replace(['Lady', 'Countess','Capt', 'Col',\
 'Don', 'Dr', 'Major', 'Rev', 'Sir', 'Jonkheer', 'Dona'], 'Rare') #稀少？
    dataset['Title'] = dataset['Title'].replace('Mille','Miss')
    dataset['Title'] = dataset['Title'].replace('Ms','Miss')
    dataset['Title'] = dataset['Title'].replace('Mme','Mrs')

print (train[['Title','Survived']].groupby(['Title'],as_index=False).mean())
```

        Title  Survived
    0  Master  0.575000
    1    Miss  0.699454
    2    Mlle  1.000000
    3      Mr  0.156673
    4     Mrs  0.793651
    5    Rare  0.347826
    

## 数据清洗

太棒了！现在让我们清洗数据并将特征映射为数值。


```python
for dataset in full_data:
    # 映射 Sex
    dataset['Sex'] = dataset['Sex'].map( {'female':0,'male':1 }).astype(int)

    # 映射 Titles
    title_mapping = dataset['Title'].map( {"Mr":1,"Miss":2,"Mrs":3,"Master":4, "Rare":5})
    dataset['Title'] = dataset['Title'].map(title_mapping)
    dataset['Title'] = dataset['Title'].fillna(0)
    
    # 映射 Embarked
    dataset['Embarked'] = dataset['Embarked'].map( {'S':0,'C':1,'Q':2} ).astype(int)
    
    # 映射 Fare
    dataset.loc[ dataset['Fare'] <= 7.91,'Fare'] = 0
    dataset.loc[ (dataset['Fare'] >7.91) & (dataset['Fare'] <=14.454),'Fare' ] = 1
    dataset.loc[ (dataset['Fare'] >14.454) & (dataset['Fare'] <=31),'Fare' ] = 2
    dataset.loc[ (dataset['Fare'] >31),'Fare' ] = 3
    
    # 映射 Age
    dataset.loc[ dataset['Age'] <=16, 'Age' ] = 0
    dataset.loc[ (dataset['Age']>16) & (dataset['Age']<=32),'Age'] = 1
    dataset.loc[ (dataset['Age']>32) & (dataset['Age']<=48),'Age'] = 2 
    dataset.loc[ (dataset['Age']>48) & (dataset['Age']<=64),'Age'] = 3
    dataset.loc[ dataset['Age']>64,'Age']                          = 4

# 特征选择
drop_elements =  ['PassengerId', 'Name', 'Ticket', 'Cabin', 'SibSp',\
                 'Parch', 'FamilySize']
train = train.drop(drop_elements,axis = 1)
train = train.drop(['CategoricalAge', 'CategoricalFare'], axis = 1)

test  = test.drop(drop_elements, axis = 1)

print (train.head(10))

train = train.values
test  = test.values
```

       Survived  Pclass  Sex  Age  Fare  Embarked  IsAlone  Title
    0         0       3    1    1   0.0         0        0    0.0
    1         1       1    0    2   3.0         1        0    0.0
    2         1       3    0    1   1.0         0        1    0.0
    3         1       1    0    2   3.0         0        0    0.0
    4         0       3    1    2   1.0         0        1    0.0
    5         0       3    1    1   1.0         2        1    0.0
    6         0       1    1    3   3.0         0        1    0.0
    7         0       3    1    0   2.0         0        0    0.0
    8         1       3    0    1   1.0         0        0    0.0
    9         1       2    0    0   2.0         1        0    0.0
    

很好！现在我们有了一个干净的数据集，可以进行进行预测了。让我们找出哪个分类器在这个数据集上工作得更好。

## 分类器比较


```python
import matplotlib.pyplot as plt
import seaborn as sns

from sklearn.model_selection import StratifiedShuffleSplit
from sklearn.metrics import accuracy_score, log_loss
from sklearn.neighbors import KNeighborsClassifier
from sklearn.svm import SVC
from sklearn.tree import DecisionTreeClassifier
from sklearn.ensemble import RandomForestClassifier, AdaBoostClassifier, GradientBoostingClassifier
from sklearn.naive_bayes import GaussianNB
from sklearn.discriminant_analysis import LinearDiscriminantAnalysis, QuadraticDiscriminantAnalysis
from sklearn.linear_model import LogisticRegression

classifiers = [
    KNeighborsClassifier(3),
    SVC(probability=True),
    DecisionTreeClassifier(),
    RandomForestClassifier(),
	AdaBoostClassifier(),
    GradientBoostingClassifier(),
    GaussianNB(),
    LinearDiscriminantAnalysis(),
    QuadraticDiscriminantAnalysis(),
    LogisticRegression()]

log_cols = ["Classifier", "Accuracy"]
log 	 = pd.DataFrame(columns=log_cols)

sss = StratifiedShuffleSplit(n_splits=10, test_size=0.1, random_state=0)

X = train[0::, 1::]
y = train[0::, 0]

acc_dict = {}

for train_index, test_index in sss.split(X, y):
	X_train, X_test = X[train_index], X[test_index]
	y_train, y_test = y[train_index], y[test_index]
	
	for clf in classifiers:
		name = clf.__class__.__name__
		clf.fit(X_train, y_train)
		train_predictions = clf.predict(X_test)
		acc = accuracy_score(y_test, train_predictions)
		if name in acc_dict:
			acc_dict[name] += acc
		else:
			acc_dict[name] = acc

for clf in acc_dict:
	acc_dict[clf] = acc_dict[clf] / 10.0
	log_entry = pd.DataFrame([[clf, acc_dict[clf]]], columns=log_cols)
	log = log.append(log_entry)

plt.xlabel('Accuracy')
plt.title('Classifier Accuracy')

sns.set_color_codes("muted")
sns.barplot(x='Accuracy', y='Classifier', data=log, color="b")
```

    <matplotlib.axes._subplots.AxesSubplot at 0x215a5588390>




![20180610-Titanic](http://p32dsli77.bkt.clouddn.com/20180610-Titanic.png)


## 预测

现在我们可以使用 SVC 分类器来预测我们的数据。


```python
candidate_classifier = SVC()
candidate_classifier.fit(train[0::, 1::], train[0::, 0])
result = candidate_classifier.predict(test)
```
