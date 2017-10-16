---
layout: post
title: "ajax&jQuery基本概念"
date: 2017-04-25 12:00:00 +0800
categories: 其他
tag: [ajax,jQuery]
---   

## 术语分解
## 什么是ajax？
### 检索关键词：ajax
- 术语：AJAX(Asynchronous Javascript And XML)
- 印象：
    - 异步JavaScript和XML，是指一种创建交互式网页应用的网页开发技术。
    - AJAX = 异步JavaScript和XML（标准通用标记语言的子集）。
    - 通过在后台与服务器进行少量数据交换，AJAX 可以使网页实现异步更新。这意味着可以在不重新加载整个网页的情况下，对网页的某部分进行更新。
    - 传统的网页（不使用 AJAX）如果需要更新内容，必须重载整个网页页面。
    - 核心对象XMLHTTPRequest。
- 例子：
- 出处：[ajax（AJAX开发）](http://baike.baidu.com/link?url=btFL5eNSz9qkKaCA61EbJb0O5JcRriZHmWxP-L9KUiC85VWzb0xomQcO4SeRb2caJ6Esn989sIkDbZI9t2ANpK)

- 创建 XMLHttpRequest 对象的语法：variable=new XMLHttpRequest();

为了应对所有的现代浏览器，包括 IE5 和 IE6，请检查浏览器是否支持 XMLHttpRequest 对象。如果支持，则创建 XMLHttpRequest 对象。如果不支持，则创建 ActiveXObject ：

```
var xmlhttp;
if (window.XMLHttpRequest)
  {// code for IE7+, Firefox, Chrome, Opera, Safari
  xmlhttp=new XMLHttpRequest();
  }
else
  {// code for IE6, IE5
  xmlhttp=new ActiveXObject("Microsoft.XMLHTTP");
  }
```

## 什么是jQuery？

- 术语：jQuery
- 印象：jQuery是一个快速、简洁的JavaScript框架，是继Prototype之后又一个优秀的JavaScript代码库（或JavaScript框架）。jQuery设计的宗旨是“write Less，Do More”，即倡导写更少的代码，做更多的事情。它封装JavaScript常用的功能代码，提供一种简便的JavaScript设计模式，优化HTML文档操作、事件处理、动画设计和Ajax交互。
- 例子：
- 出处：[百度百科：jQuery](http://baike.baidu.com/link?url=UZyoJXkbl2l9gQZ1KihfD4xlPpYPEYD-HrcKpaOhooPO0qCvZekeuQdsLHKLeZjBzmATrX3M21M5S2pNEDu3N_)
- 参考教程：[jQuery 安装](http://www.w3school.com.cn/jquery/jquery_install.asp)

### 检索关键词：$.getJSON()

jQuery ajax中的getJSON() 方法

使用方法如下：

```
    $.getJSON( url [, data ] [, success(data, textStatus, jqXHR) ] )
```

url是必选参数，表示json数据的地址；

data是可选参数，用于请求数据时发送数据参数；

success是可参数，这是一个回调函数，用于处理请求到的数据。

获取json数据举例：

```
$.getJSON('test.json', function(data){
   for (var i = 0; i < data.rows.length; i++) {
      $('#test').append('<p>' + data.rows[i].realName + '</p>');
   }
});
```

- [AJAX 数据库实例](http://www.w3school.com.cn/ajax/ajax_database.asp)
- [$.getJSON( )的使用方法简介](http://www.cnblogs.com/leejersey/p/3750232.html)
- [jQuery ajax - getJSON() 方法](http://www.w3school.com.cn/jquery/ajax_getjson.asp)