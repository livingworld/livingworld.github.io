---
layout: post
title: "anki 批量制卡"
date: 2018-06-04 22:33:00 +0800
categories: 其他
tag: []
---   

## 前言

在[`2018年二级建造师考试`](https://livingworld.github.io/%E5%85%B6%E4%BB%96/2018/06/03/2018%E5%B9%B4%E4%BA%8C%E7%BA%A7%E5%BB%BA%E9%80%A0%E5%B8%88%E8%80%83%E8%AF%95.html)一文中我提到使用 python 抓取数据批量制卡，数据来源题库是[`圣才题库在线考试`](http://usertk.100xuexi.com/PracticeCenter/Directory/Index?tb_l_PaperQuePlanID=86213&token=22683921373B303F2D3336276525342338692D393430322C23686472393A3727256830702F363033782130252D34342232303A3571303127)。在上文中我已经提到了通过 excel 进行批量制卡的方法，能找到相应的制卡方法链接。这篇文章我将解释如何借助 python 的第三方库 selenum 进行数据抓取。

## 环境配置

### 什么是 selenium？

[Selenium 官方网站](https://www.seleniumhq.org/)的解释如下：
> Selenium 使浏览器自动化。就是这样！你用这种力量做什么完全取决于你。主要是为了测试的目的而自动化 web 应用程序，但当然不限于此。无聊的基于 Web 的管理任务可以（也应该）自动化。

### 安装 selenium

按照 [Selenium with Python ](http://selenium-python.readthedocs.io/) 文档，在 python 环境中配置 selenium：

```
pip install selenium
```

Selenium 需要一个驱动程序来连接所选的浏览器。我由于使用的是 Chrome 浏览器，所以可以在 [ChromeDriver](https://sites.google.com/a/chromium.org/chromedriver/downloads) 中下载与 Chrome 浏览器版本对应的驱动，所以需要查询 Chrome 浏览器的版本号。我采用的系统是 Windows ，所以需要将下载的 ChromeDriver.exe 文件配置到 环境变量的 PATH 路径中，所以，需要现将 ChromeDrover.exe 文件放在合适的位置，比如直接放在与 Chrome.exe 同一目录下。

### 如何使用 selenium？

在官网中，可以演示给出的代码：

```
from selenium import webdriver
from selenium.webdriver.common.keys import Keys

driver = webdriver.Firefox()
driver.get("http://www.python.org")
assert "Python" in driver.title
elem = driver.find_element_by_name("q")
elem.clear()
elem.send_keys("pycon")
elem.send_keys(Keys.RETURN)
assert "No results found." not in driver.page_source
driver.close()
```

在熟悉之后，就要进入我是如何通过代码实现的，以及需要注意什么。

开始启动

```
import os,time
from selenium import webdriver
from bs4 import BeautifulSoup
chromedriver = "C:\\Program Files (x86)\\Google\\Chrome\\Application\\chromedriver.exe"#chrome所在驱动
os.environ["webdriver.chrome.driver"] = chromedriver
driver = webdriver.Chrome(chromedriver)
driver.get('http://user.100xuexi.com/MyDeal/myEBookPurchase.aspx')
time.sleep(2)
```

在启动进入之后，可以手动或者用代码来登录账户，在此处我不详细加解释，感兴趣的可以多看官方文档。

在完成登录操作之后，执行下面的代码，来使获取想要的窗口句柄。

```
#接下来会有新的窗口打开，获取所有窗口句柄
windows = driver.window_handles
driver.switch_to.window(windows[-1]) #-1或-2是根据窗口打开的顺序来确定，最后一个打开则是 -1
```

在加控制的窗口切换至题库的窗口之后，通过手动选择需要抓取的页面。

## 解析成 html

```
html = driver.page_source
soup = BeautifulSoup(html, "html5lib")
```

## html 文件解析
### 解析问题

```
import re

baseurl_item = u'2Z204000　建设工程合同和劳动合同法律制度\\2Z204020　劳动合同及劳动关系制度'
single_baseurl = u'二级建造师anki\\二建2018法规\\'+ baseurl_item + u'\\单选\\'
multiple_baseurl = u'二级建造师anki\\二建2018法规\\'+ baseurl_item + u'\\多选\\'

content_all = soup.find_all("div",attrs={"class":"nodeItem"})

# replace with `soup.findAll` if you are using BeautifulSoup3
for div in soup.find_all("div", {'class':'Item config'}): 
    if len(div.find_all("div", {'class':'ItemBotCompose'}))!=0:
        div.decompose() #将满足调节的 div 项删除

single_content_all = []
multiple_content_all = []

single_content_all = content_all[0].find_all("div",attrs={"class":"question config"})
if len(content_all)>1:
    multiple_content_all = content_all[1].find_all("div",attrs={"class":"question config"})

# 单选
single_question = []

for i in range(len(single_content_all)):
    single_contents = single_content_all[i].dt.contents
    #print(contents_single)
    single_s = ''
    for j in single_contents:#单选
        single_s = single_s+str(j)
    single_question.append(re.sub('[\n]','',single_s))

# 多选

multiple_question = []

for i in range(len(multiple_content_all)):#多选
    multiple_contents = multiple_content_all[i].dt.contents
    #print(contents_single)
    multiple_s = ''
    for j in multiple_contents:
        multiple_s = multiple_s+str(j)
    multiple_question.append(re.sub('[\n]','',multiple_s))

#保存选项

#question_file = open('question_file.txt', 'w')
import codecs

single_folder = os.path.exists(single_baseurl)
multiple_folder = os.path.exists(multiple_baseurl)
if not single_folder:                   #判断是否存在文件夹如果不存在则创建为文件夹
    os.makedirs(single_baseurl)
                
if not multiple_folder:                   #判断是否存在文件夹如果不存在则创建为文件夹
    os.makedirs(multiple_baseurl)
                
single_question_file = codecs.open(single_baseurl + 'single_question.txt', 'w','utf-8')
multiple_question_file = codecs.open(multiple_baseurl + 'multiple_question.txt', 'w','utf-8')

for item in single_question:
    single_question_file.write("%s\r\n" % item)
    
for item in multiple_question:
    multiple_question_file.write("%s\r\n" % item)
    
single_question_file.close()
multiple_question_file.close()

```

### 解析选项

```
option_all = soup.find_all("div",attrs={"class":"nodeItem"})

# 区别单选和多选

single_option_all = option_all[0].find_all("div",attrs={"class":"question config"})
if len(option_all)>1:
    multiple_option_all = option_all[1].find_all("div",attrs={"class":"question config"})

# 单选

single_option_a = []
single_option_b = []
single_option_c = []
single_option_d = []
for option_i in range(len(single_option_all)):
    single_option_a.append(re.sub('[\r|\n|\t|\\s]', '', single_option_all[option_i].table.tbody.find_all("label")[0].get_text()))
    single_option_b.append(re.sub('[\r|\n|\t|\\s]', '', single_option_all[option_i].table.tbody.find_all("label")[1].get_text()))
    single_option_c.append(re.sub('[\r|\n|\t|\\s]', '', single_option_all[option_i].table.tbody.find_all("label")[2].get_text()))
    single_option_d.append(re.sub('[\r|\n|\t|\\s]', '', single_option_all[option_i].table.tbody.find_all("label")[3].get_text()))

# 多选

multiple_option_a = []
multiple_option_b = []
multiple_option_c = []
multiple_option_d = []
multiple_option_e = []
for option_i in range(len(multiple_option_all)):
    multiple_option_a.append(re.sub('[\r|\n|\t|\\s]', '', multiple_option_all[option_i].table.tbody.find_all("label")[0].get_text()))
    multiple_option_b.append(re.sub('[\r|\n|\t|\\s]', '', multiple_option_all[option_i].table.tbody.find_all("label")[1].get_text()))
    multiple_option_c.append(re.sub('[\r|\n|\t|\\s]', '', multiple_option_all[option_i].table.tbody.find_all("label")[2].get_text()))
    multiple_option_d.append(re.sub('[\r|\n|\t|\\s]', '', multiple_option_all[option_i].table.tbody.find_all("label")[3].get_text()))
    multiple_option_e.append(re.sub('[\r|\n|\t|\\s]', '', multiple_option_all[option_i].table.tbody.find_all("label")[4].get_text()))

# 保存单选

import codecs

single_option_a_file = codecs.open(single_baseurl + 'single_option_a.txt', 'w','utf-8')
single_option_b_file = codecs.open(single_baseurl + 'single_option_b.txt', 'w','utf-8')
single_option_c_file = codecs.open(single_baseurl + 'single_option_c.txt', 'w','utf-8')
single_option_d_file = codecs.open(single_baseurl + 'single_option_d.txt', 'w','utf-8')

for item in single_option_a:
    single_option_a_file.write("%s\r\n" % item)
for item in single_option_b:
    single_option_b_file.write("%s\r\n" % item)
    
for item in single_option_c:
    single_option_c_file.write("%s\r\n" % item)
    
for item in single_option_d:
    single_option_d_file.write("%s\r\n" % item)

single_option_a_file.close()
single_option_b_file.close()
single_option_c_file.close()
single_option_d_file.close()

保存多选

import codecs

multiple_option_a_file = codecs.open(multiple_baseurl + 'multiple_option_a.txt', 'w','utf-8')
multiple_option_b_file = codecs.open(multiple_baseurl + 'multiple_option_b.txt', 'w','utf-8')
multiple_option_c_file = codecs.open(multiple_baseurl + 'multiple_option_c.txt', 'w','utf-8')
multiple_option_d_file = codecs.open(multiple_baseurl + 'multiple_option_d.txt', 'w','utf-8')

for item in multiple_option_a:
    multiple_option_a_file.write("%s\r\n" % item)
for item in multiple_option_b:
    multiple_option_b_file.write("%s\r\n" % item)
    
for item in multiple_option_c:
    multiple_option_c_file.write("%s\r\n" % item)
    
for item in multiple_option_d:
    multiple_option_d_file.write("%s\r\n" % item)

multiple_option_a_file.close()
multiple_option_b_file.close()
multiple_option_c_file.close()
multiple_option_d_file.close()
```

### 解析答案

```
anwser_all = soup.find_all("div",attrs={"class":"nodeItem"})

single_anwser_all = anwser_all[0].find_all("div",attrs={"class":"Item config"}) #单选
if len(anwser_all)>1:
    multiple_anwser_all = anwser_all[1].find_all("div",attrs={"class":"Item config"}) #多选

single_answer_right = []
multiple_answer_right = []

for answer_i in range(len(single_anwser_all)):
    #print(anwser_all[25].find("div",attrs={"class":"config answer"}))
    #print(anwser_all[answer_i].find("table",attrs={"class":"HiddenStart"}).find("div",attrs={"class":"anser Con"}))
    #print(anwser_all[answer_i].find("table",attrs={"class":"HiddenStart"}).find("div",attrs={"class":"anser Con"}).get_text())
    single_answer_right.append(single_anwser_all[answer_i].find("div",attrs={"class":"anser Con"}).string)
    
for answer_i in range(len(multiple_anwser_all)):
    #print(anwser_all[25].find("div",attrs={"class":"config answer"}))
    #print(anwser_all[answer_i].find("table",attrs={"class":"HiddenStart"}).find("div",attrs={"class":"anser Con"}))
    #print(anwser_all[answer_i].find("table",attrs={"class":"HiddenStart"}).find("div",attrs={"class":"anser Con"}).get_text())
    multiple_answer_right.append(multiple_anwser_all[answer_i].find("div",attrs={"class":"anser Con"}).string)
    
len(single_answer_right)

import codecs

single_anwser_file = codecs.open(single_baseurl + 'single_anwser.txt', 'w','utf-8')
multiple_anwser_file = codecs.open(multiple_baseurl + 'multiple_anwser.txt', 'w','utf-8')

for item in multiple_answer_right:
    multiple_anwser_file.write("%s\r\n" % item)
    
single_anwser_file.close()
multiple_anwser_file.close()
```

### 解析答案解析

```
analysis_all = soup.find_all("div",attrs={"class":"nodeItem"})

single_analysis_all = analysis_all[0].find_all("div",attrs={"class":"Item config"}) #单选
if len(analysis_all)>1:
    multiple_analysis_all = analysis_all[1].find_all("div",attrs={"class":"Item config"}) #多选
len(single_analysis_all)

single_analysis = []
multiple_analysis = []

for analysis_i in range(len(single_analysis_all)):
    print(single_analysis_all[analysis_i].find("div",attrs={"class":"analysits Con"}))
    print('\n')
    #answer_right.append(anwser_all[answer_i].find("div",attrs={"class":"analysits Con"}).string)
#print(answer_right)
for analysis_i in range(len(multiple_analysis_all)):
    print(multiple_analysis_all[analysis_i].find("div",attrs={"class":"analysits Con"}))
    print('\n')

print(multiple_analysis_all[0].find("div",attrs={"class":"analysits Con"}))

single_analay = []
multiple_analay = []
for i in range(len(single_analysis_all)):
    single_analay_item = single_analysis_all[i].find("div",attrs={"class":"analysits Con"}).contents
    #print(analay_single)
    single_analay.append(single_analay_item)
    #print(analay_single)
    
for i in range(len(multiple_analysis_all)):
    multiple_analay_item = multiple_analysis_all[i].find("div",attrs={"class":"analysits Con"}).contents
    #print(analay_single)
    multiple_analay.append(multiple_analay_item)
    #print(analay_single)

single_analay_final = []
for item in single_analay:
    #analysis_file.write("%s" % item)
    single_s = ''
    for i in item:
        single_s = single_s + str(i)
    single_analay_final.append(single_s)
    #analysis_file.write("%s\n" % s)
#analysis_file.close()

multiple_analay_final = []
for item in multiple_analay:
    #analysis_file.write("%s" % item)
    multiple_s = ''
    for i in item:
        multiple_s = multiple_s + str(i)
    multiple_analay_final.append(multiple_s)
    #analysis_file.write("%s\n" % s)
#analysis_file.close()

import codecs
single_analysis_file = codecs.open(single_baseurl + 'single_analysis_file.txt', 'w','utf-8')

for s in single_analay_final:
    print(s)
    print("\n")
    single_analysis_file.write("%s\r\n" % s)
    
multiple_analysis_file = codecs.open(multiple_baseurl + 'multiple_analysis_file.txt', 'w','utf-8')

for s in multiple_analay_final:
    print(s)
    print("\n")
    multiple_analysis_file.write("%s\r\n" % s)    
    
single_analysis_file.close()
multiple_analysis_file.close()
```

### 选项拼接

单选选项拼接

```
single_data = []
for i in range(len(single_option_a)):
    single_data_string = single_question[i].replace('（　　）','{{c1::（）}}') + "    "+ single_option_a[i].strip().replace('A．','')+ '<br>' + single_option_b[i].strip().replace('B．','')+ '<br>'+ single_option_c[i].strip().replace('C．','')+ '<br>'+ single_option_d[i].replace('D．','')+ "   "+ single_answer_right[i]+ "    "+ single_analay_final[i]
    single_data.append(single_data_string)
data_single = codecs.open(single_baseurl + 'data_single.txt', 'w','utf-8')

for i in range(len(single_data)):
    data_single.write(single_data[i]+"\r\n")
data_single.close()
```

多选选项拼接

```
multiple_data = []
for i in range(len(multiple_option_a)):
    multiple_data_string = multiple_question[i].replace('（　　）','{{c1::（）}}') + "    "+ multiple_option_a[i].strip().replace('A．','')+ '<br>' + multiple_option_b[i].strip().replace('B．','')+ '<br>'+ multiple_option_c[i].strip().replace('C．','')+ '<br>'+ multiple_option_d[i].replace('D．','')+ '<br>'+ multiple_option_e[i].replace('E．','')+ "    "+ multiple_answer_right[i]+ "  "+ multiple_analay_final[i]
    multiple_data.append(multiple_data_string)
data_multiple = codecs.open(multiple_baseurl + 'data_multiple.txt', 'w','utf-8')

for i in range(len(multiple_data)):
    data_multiple.write(multiple_data[i]+"\r\n")
data_multiple.close()
```

## 不足之处改正

上述选项解析部分无法将选项中的图片等 html 原始信息保存下来，经过调整如下：

```
single_option_a = []
single_option_b = []
single_option_c = []
single_option_d = []
for option_i in range(len(single_option_all)):
    
    print(option_i)
    _single_option_a = ''
    _single_option_b = ''
    _single_option_c = ''
    _single_option_d = ''
    
    for i in single_option_all[option_i].table.tbody.find_all("label")[0].children:
        if 'input' in str(i):
            pass
        else:
            if 'img' not in str(i):
                _single_option_a = _single_option_a + re.sub('[\r|\n|\t|\\s]', '',str(i))
            else:
                _single_option_a = _single_option_a + str(i)
    single_option_a.append(_single_option_a)
    
    for i in single_option_all[option_i].table.tbody.find_all("label")[1].children:
        if 'input' in str(i):
            pass
        else:
            if 'img' not in str(i):
                _single_option_b = _single_option_b + re.sub('[\r|\n|\t|\\s]', '',str(i))
            else:
                _single_option_b = _single_option_b + str(i)
    single_option_b.append(_single_option_b)
    
    if len(single_option_all[option_i].table.tbody.find_all("label")) == 2:
        single_option_c.append('C．'+'null')
    else:
        for i in single_option_all[option_i].table.tbody.find_all("label")[2].children:
            if 'input' in str(i):
                pass
            else:
                if 'img' not in str(i):
                    _single_option_c = _single_option_c + re.sub('[\r|\n|\t|\\s]', '',str(i))
                else:
                    _single_option_c = _single_option_c + str(i)
        single_option_c.append(_single_option_c)
    
    if len(single_option_all[option_i].table.tbody.find_all("label"))!=4:
        single_option_d.append('D．'+'null')
    else:
        for i in single_option_all[option_i].table.tbody.find_all("label")[3].children:
            if 'input' in str(i):
                pass
            else:
                if 'img' not in str(i):
                    _single_option_d = _single_option_d + re.sub('[\r|\n|\t|\\s]', '',str(i))
                else:
                    _single_option_d = _single_option_d + str(i)
        single_option_d.append(_single_option_d)
    

```

## 其他

如何批量创建 anki 题录名称，如何借助 anki 批量导入题录名称，如何批量新建文件夹，如何从 txt 文本中获取所有 url 图片链接，如何通过链接批量下载图片等等，请移步 github 仓库[HydroScrapy](https://github.com/livingworld/HydroScrapy)

## CHANGELOG

- 20180609 update
- created at 20180604









