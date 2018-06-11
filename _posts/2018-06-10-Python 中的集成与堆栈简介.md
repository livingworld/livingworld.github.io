---
layout: post
title: "Python 中的集成与堆栈简介"
date: 2018-06-10 18:00:00 +0800 
categories: DA
tag: [DA]
---   

# Python中的集成与堆栈简介

该博客翻译自[Introduction to Ensembling/Stacking in Python](https://www.kaggle.com/arthurtok/introduction-to-ensembling-stacking-in-python)，目前只翻译了一部分，后面 Ensembling/Stacking 部分实难理解，姑且放一放。

## 介绍

这篇 notebook 是一个非常基础和简单的入门基础教程，介绍了 ensembling(combining) 基础学习模型方法的方法，特别是称为 Stacking的 ensembling 的变体。简而言之，stacking 用作第一级 (基础), 对几个基础分类器的预测，然后在第二级使用另一个模型来预测来自较早的第一级预测的输出。

Titanic 数据集是引入这一概念的主要候选者，因为许多 Kaggle 的新手都是从这里开始。此外，尽管 stacking 是许多团队赢得 Kaggle 比赛的原因，但似乎在这个主题上缺乏内核，所以我希望这个 notebook 可以填补这个空白。

我自己也是 Kaggle 场景的新手，我设法学习和研究的一个合适的 ensembling/stacking 脚本是由伟大的 Faron 在 AllState Serverity Claims 竞赛中编写的。这篇 notebook 的材料很大程度上借鉴了 Faron 的脚本，虽然被移植到分类器的集合中，而他的脚本是回归量的集合 (?)。物理如何，请在这里查看它的脚本：

[Stacking Starter](https://www.kaggle.com/mmueller/stacking-starter): by Faron

现在，在手边的 notebook 上，我希望它能够做到公正，并以直觉和简洁的方式传达 ensembling 的概念。我的另一个独立的 Kaggle [脚本](https://www.kaggle.com/arthurtok/titanic/simple-stacking-with-xgboost-0-808)采用了实现了下面讨论的完全相同的 ensembling 步骤 (虽然参数不同)，公共 LB 分数为 0.808，足以达到前 9%，并且运行时间仅为 4 分钟。因此，我相当确定很多的空间可以改进并添加到该脚本中。无论如何，请随时给我留下任何关于我如何改进的意见。


```python
# Load in our libraries
import pandas as pd
import numpy as np
import re
import sklearn
import xgboost as xgb
import seaborn as sns
import matplotlib.pyplot as plt
%matplotlib inline

import plotly.offline as py
py.init_notebook_mode(connected=True)
import plotly.graph_objs as go
import plotly.tools as tls

import warnings
warnings.filterwarnings('ignore')

# Going to use these 5 base models for the stacking
from sklearn.ensemble import (RandomForestClassifier, AdaBoostClassifier, 
                              GradientBoostingClassifier, ExtraTreesClassifier)
from sklearn.svm import SVC
from sklearn.cross_validation import KFold
```



## 特征探索，工程和清洗

我们将继续研究大多数内核的结构，即首先研究手头的数据，确定可能的特征工程机会，以及对任何分类特征进行数字编码。


```python
# 加载 train 和 test 数据集编码任何分类特征。

train = pd.read_csv("./input/train.csv")
test = pd.read_csv("./input/test.csv")

# 保存我们的乘客 ID 以便于访问

PassengerID = test['PassengerId']

train.head(3)
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>PassengerId</th>
      <th>Survived</th>
      <th>Pclass</th>
      <th>Name</th>
      <th>Sex</th>
      <th>Age</th>
      <th>SibSp</th>
      <th>Parch</th>
      <th>Ticket</th>
      <th>Fare</th>
      <th>Cabin</th>
      <th>Embarked</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1</td>
      <td>0</td>
      <td>3</td>
      <td>Braund, Mr. Owen Harris</td>
      <td>male</td>
      <td>22.0</td>
      <td>1</td>
      <td>0</td>
      <td>A/5 21171</td>
      <td>7.2500</td>
      <td>NaN</td>
      <td>S</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2</td>
      <td>1</td>
      <td>1</td>
      <td>Cumings, Mrs. John Bradley (Florence Briggs Th...</td>
      <td>female</td>
      <td>38.0</td>
      <td>1</td>
      <td>0</td>
      <td>PC 17599</td>
      <td>71.2833</td>
      <td>C85</td>
      <td>C</td>
    </tr>
    <tr>
      <th>2</th>
      <td>3</td>
      <td>1</td>
      <td>3</td>
      <td>Heikkinen, Miss. Laina</td>
      <td>female</td>
      <td>26.0</td>
      <td>0</td>
      <td>0</td>
      <td>STON/O2. 3101282</td>
      <td>7.9250</td>
      <td>NaN</td>
      <td>S</td>
    </tr>
  </tbody>
</table>
</div>



毫无疑问, 我们的任务就是以某种方式从分类变量中抽取信息。

### 特征工程

在这里，归功于 Sina 的关于特征工程思想非常全面和深思熟虑的笔记本，请查看他的工作。

[Titanic Best Working Classfier](https://www.kaggle.com/sinakhorami/titanic-best-working-classifier) : by Sina




```python
full_data = [train, test]

# 我自己添加的一些特征
# 给出名字的长度
train['Name_length'] = train['Name'].apply(len)
test['Name_length'] = test['Name'].apply(len)

# 特征告诉了乘客在 Titanic 上是否有客舱
train['Has_Cabin'] = train['Cabin'].apply(lambda x:0 if type(x) == float else 1)
test['Has_Cabin'] = test['Cabin'].apply(lambda x:0 if type(x) == float else 1)

# 来自 Sina 的特征工程步骤
# 创建新的特征 FamilySize 作为 SibSp 和 Parch 的结合
for dataset in full_data:
    dataset['FamilySize'] = dataset['SibSp'] + dataset['Parch'] + 1

# 从 FamilySize 中创建新特征 IsAlone
for dataset in full_data:
    dataset['IsAlone'] = 0
    dataset.loc[dataset['FamilySize'] == 1,'IsAlone'] = 1

# 删除 Embarked 列中所有的 NULLS
for dataset in full_data:
    dataset['Embarked'] = dataset['Embarked'].fillna('S')

# 删除 Fare 列中所有的 NULLS，并创建一个新特征 CategoricalFare
for dataset in full_data:
    dataset['Fare'] = dataset['Fare'].fillna(train['Fare'].median())
train['CategoricalFare'] = pd.qcut(train['Fare'], 4)

# 创建一个新特征 CategoricalAge
for dataset in full_data:
    age_avg = dataset['Age'].mean()
    age_std = dataset['Age'].std()
    age_null_count = dataset['Age'].isnull().sum()
    age_null_random_list = np.random.randint(age_avg - age_std,age_avg + age_std,size=age_null_count)
    dataset['Age'][np.isnan(dataset['Age'])] = age_null_random_list
    dataset['Age'] = dataset['Age'].astype(int)
train['CategoricalAge'] = pd.cut(train['Age'],5)

# 定义函数来从乘客名称中抽取头衔
def get_title(name):
    title_search = re.search(' ([A-Za-z]+)\.',name)
    # 如果头衔存在，抽取并返回。
    if title_search:
        return title_search.group(1)
    return ""
# 创建一个新特征 Title，其中包含乘客名称的头衔
for dataset in full_data:
    dataset['Title'] = dataset['Name'].apply(get_title)
# 将所有非常见头衔分组到一个单独的组 "Rare"
for dataset in full_data:
    dataset['Title'] = dataset['Title'].replace(['Lady', 'Countess','Capt', 'Col','Don', 'Dr', 'Major', 'Rev', 'Sir', 'Jonkheer', 'Dona'], 'Rare')

    dataset['Title'] = dataset['Title'].replace('Mlle', 'Miss')
    dataset['Title'] = dataset['Title'].replace('Ms', 'Miss')
    dataset['Title'] = dataset['Title'].replace('Mme', 'Mrs')
    
for dataset in full_data:
    # 映射 Sex
    dataset['Sex'] = dataset['Sex'].map( {'female': 0, 'male': 1} ).astype(int)
    
    # 映射 titles
    title_mapping = {"Mr": 1, "Miss": 2, "Mrs": 3, "Master": 4, "Rare": 5}
    dataset['Title'] = dataset['Title'].map(title_mapping)
    dataset['Title'] = dataset['Title'].fillna(0)
    
    # 映射 Embarked
    dataset['Embarked'] = dataset['Embarked'].map( {'S': 0, 'C': 1, 'Q': 2} ).astype(int)
    
    # 映射 Fare
    dataset.loc[ dataset['Fare'] <= 7.91, 'Fare'] 						        = 0
    dataset.loc[(dataset['Fare'] > 7.91) & (dataset['Fare'] <= 14.454), 'Fare'] = 1
    dataset.loc[(dataset['Fare'] > 14.454) & (dataset['Fare'] <= 31), 'Fare']   = 2
    dataset.loc[ dataset['Fare'] > 31, 'Fare'] 							        = 3
    dataset['Fare'] = dataset['Fare'].astype(int)
    
    # 映射 Age
    dataset.loc[ dataset['Age'] <= 16, 'Age'] 					       = 0
    dataset.loc[(dataset['Age'] > 16) & (dataset['Age'] <= 32), 'Age'] = 1
    dataset.loc[(dataset['Age'] > 32) & (dataset['Age'] <= 48), 'Age'] = 2
    dataset.loc[(dataset['Age'] > 48) & (dataset['Age'] <= 64), 'Age'] = 3
    dataset.loc[ dataset['Age'] > 64, 'Age'] = 4 ;
```


```python
# 特征选择
drop_elements = ['PassengerId', 'Name', 'Ticket', 'Cabin', 'SibSp']
train = train.drop(drop_elements, axis = 1)
train = train.drop(['CategoricalAge', 'CategoricalFare'], axis = 1) #为什么要分成两步？实际效果都一样。
test  = test.drop(drop_elements, axis = 1)
```

好了，现在已经清洗了这些特征并提取了相关信息和删除了 categorical 列，我们的特征现在应该都是数值，这是一种适合我们的机器学习模型的格式。然而，在我们继续之前，让我们先生成一些简单的相关性和分布图来观察转化后的数据集的。

## 可视化


```python
train.head(3)
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Survived</th>
      <th>Pclass</th>
      <th>Sex</th>
      <th>Age</th>
      <th>Parch</th>
      <th>Fare</th>
      <th>Embarked</th>
      <th>Name_length</th>
      <th>Has_Cabin</th>
      <th>FamilySize</th>
      <th>IsAlone</th>
      <th>Title</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>0</td>
      <td>3</td>
      <td>1</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>23</td>
      <td>0</td>
      <td>2</td>
      <td>0</td>
      <td>1</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1</td>
      <td>1</td>
      <td>0</td>
      <td>2</td>
      <td>0</td>
      <td>3</td>
      <td>1</td>
      <td>51</td>
      <td>1</td>
      <td>2</td>
      <td>0</td>
      <td>3</td>
    </tr>
    <tr>
      <th>2</th>
      <td>1</td>
      <td>3</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
      <td>22</td>
      <td>0</td>
      <td>1</td>
      <td>1</td>
      <td>2</td>
    </tr>
  </tbody>
</table>
</div>



### 皮尔逊相关热图

让我们生成一些特征的关联图，以查看一个特征与下一个特征之间的相关性。为此，我们将利用 Seaborn 绘图包，这使我们可以非常方便的绘制热图，如下所示


```python
colormap = plt.cm.RdBu
plt.figure(figsize=(14,12))
plt.title('Pearson Correlation of Features',y=1.05,size=15)
sns.heatmap(train.astype(float).corr(),linewidths=0.1,vmax=1.0,
           square=True,cmap=colormap,linecolor='white',annot=True)
```




    <matplotlib.axes._subplots.AxesSubplot at 0x2aa44c2d550>




![20180610-pearson-correlation-of-features](http://p32dsli77.bkt.clouddn.com/20180610-pearson-correlation-of-features.png)


### 从图中能得到

皮尔逊相关图可以告诉我们的一件事是，没有太多的特征与另一个特征强相关。从将这些特征添加到您的学习模型中的角度来看，这是很好的，因为这意味着我们的训练集中没有太多冗余或多余的数据，我们很高兴每个功能都带有一些独特的信息。这里有两个最相关的特征 Family size 和 Parch (家长和孩子)。为了本练习的目的，我仍然保留这两个特征。

### 多变量图

最后让我们生成一些多变量图来馆擦从一个到另一个特征的数据分布。我们再次使用 Seaborn 来帮助我们。


```python
g = sns.pairplot(train[[u'Survived',u'Pclass',u'Age',u'Parch',u'Fare',u'Embarked',u'FamilySize',u'Title']],hue='Survived',palette = 'seismic',size=1.2,diag_kind = 'kde',diag_kws=dict(shade=True),plot_kws=dict(s=10))
g.set(xticklabels=[])
```

    <seaborn.axisgrid.PairGrid at 0x2aa456c99e8>


![20180610-pearson-correlation-of-features](http://p32dsli77.bkt.clouddn.com/20180610-pairplot.png)


## Ensembling & Stacking 模型

最后，在完成了关于特征工程和格式的简短旋风式绕道后，我们终于来到了这篇 notebook 的精髓和要点。

创建一个 Stacking ensemble！


### Helpers 通过 Python 类

在这里我们调用了 Python 的类来帮助我们更方便。对于编程新手来讲，人们通常来说，人们通常会听到类与面向对象编程 (oop) 结合使用。简言之，类有助于拓展一些用于创建对象的代码/程序 (为老派的 peeps 提供变量)，以及实现特定于该类的函数和方法。

在下面的代码部分，我们实际上编写了一个 SklearnHelper 类，它允许扩展所有 Sklearn 分类中常见的内置方法 (例如 train,predict 和 fit)。因此，如果我们想要调用 5 个不同的分类器，这就减少了冗余，因为不需要编写编写 5 次不同的分类器。


```python
# 一些将会在后面用到的有用参数

ntrain = train.shape[0]
ntest =test.shape[0]
SEED = 0 # 可重复性
NFOLDS = 5 # 设置折叠创建折外预测
kf = KFold(ntrain,n_folds = NFOLDS, random_state=SEED)

# 用于扩展 Sklearn 分类器的类
class SklearnHelper(object):
    def __init__(self, clf, seed=0, params=None):
        params['random_state'] = seed
        self.clf = clf(**params)

    def train(self, x_train, y_train):
        self.clf.fit(x_train, y_train)

    def predict(self, x):
        return self.clf.predict(x)
    
    def fit(self,x,y):
        return self.clf.fit(x,y)
    
    def feature_importances(self,x,y):
        print(self.clf.fit(x,y).feature_importances_)
        
# 用于扩展 XGboost 分类器的类
```

对于哪些已经知道这一点的人，请接受我的建议，但是对于那些以前没有在 Python 中创建类或对象的人，让我解释上面给出的代码的作用。在创建我们的基础分类器时，我的只使用 Sklearn 库中已存在的模型，因此只扩展该类。

**def init**: 用于调用该类的默认构造器的 Python 标准库。这意味着当你创建一个对象 (分类器)，你必须给它 clf (你想要什么 sklearn 分类器), seed (随机种子) 和 params (分类器的参数) 的参数。

剩下的代码只是类的方法，它们只调用已 sklearn 分类器中已经存在的方法。本质上，我们创建了一个包装类来扩展各种的 Sklearn 分类器，这样当我们将多个学习器实现到我们的堆栈时，就可以帮助我们减少必须反复编写相同的代码。

### 折外预测

现在正如前面部分提到的那样，stacking 使用基础分类器预测作为对第二级模型的训练的输入。然而，不能简单的在完整训练集上训练基础模型，在完整数测试集上产生预测，然后输出这些用于第二级训练。这回带来基础模型预测已经 "看到" 测试集并因此在提供这些预测时可能过度拟合。


```python
def get_oof(clf,x_train,y_train,x_test):
    oof_train = np.zeros((ntrain,))
    oof_test = np.zeros((ntest,))
    oof_test_skf = np.empty((NFOLDS,ntest))
    
    for i ,(train_index,test_index) in enumerate(kf):
        x_tr = x_train[train_index]
        y_tr = y_train[train_index]
        x_te = x_train[test_index]
        
        clf.train(x_tr,y_tr)
        
        oof_train[test_index] = clf.predict(x_te)
        oof_test_skf[i, :] = clf.predict(x_test)

    oof_test[:] = oof_test_skf.mean(axis=0)
    return oof_train.reshape(-1, 1), oof_test.reshape(-1, 1)
```

## 生成我们的基础第一级模型

所以现在让我们准备 5 个学习模型作为我们的第一级分类。这些模型都可以安装常规通过 Sklearn 库来调用，列表如下：

- 1.Random Forest classifier
- 2.Extra Trees classifier
- 3.AdaBoost classifer
- 4.Gradient Boosting classifer
- 5.Support Vector Machine
