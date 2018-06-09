---
layout: post
title: "leo 书签管理"
date: 2018-06-09 12:00:00 +0800 
categories: leo
tag: [leo]
---   

## 前言

leo 插件的书签管理功能是我在前言中阅读到的，我在[Leo editor: Managing our bookmarks - YouTube](https://www.youtube.com/watch?v=9AvbL_0JEMw)上也看到了别人的演示过程，不过过程简化了很多。其实在官方的 github 仓库中，找到 [mod_http.py](https://github.com/leo-editor/leo-editor/blob/master/leo/plugins/mod_http.py) 插件之后，打开该文件就能找到对应的操作步骤。为了更详细的介绍如何操作，我将通过图片进行直观演示。

### 启用插件

先启用 bookmarks.py 和 mod_http.py：

在 leo 主界面，依次点击 "Settings"--> myLeoSettings.leo，选择 @settings 节点的子节点 @enabled-plugins，在其正文中，添加如下信息进行启用：

```
bookmarks.py
mod_http.py
```

选择提纲：

<img src="http://p32dsli77.bkt.clouddn.com/20180609-myLeoSettings.jpg" width="50%" heigint="50%" alt=""/>

启用插件：

<img src="http://p32dsli77.bkt.clouddn.com/20180609-enabled-plugins.jpg" width="50%" heigint="50%" align="middle" alt=""/>


### 设置

在默认的 workbook.leo 提纲中，打开 Statup 中的 @settings 节点，新建下列节点：

```
``@bool http_active = True``
    required for plug-in to be active
``@string http_ip = 127.0.0.1``
    address to bind to, see notes below
``@int  http_port = 8130``
    port to use (1 3 0 ~= L E O)
``@bool http_allow_remote_exec = False``
    must be changed to True for remote code execution
``@string rst_http_attributename = 'rst_http_attribute'``
    link to obsolete rst3 plugin
``@data user_bookmark_stylesheet``
    Additional .css for bookmarks.
``@data http_stylesheet``
    The default .css for this page.
``@data user_http_stylesheet``
    Additional .css for this page.
``@data mod_http script``
    The body text of this @data setting contains *all* the
    javascript used in the page.
```

<img src="http://p32dsli77.bkt.clouddn.com/20180609-mod_http_settings.jpg" width="50%" heigint="50%" align="middle" alt=""/>

### 浏览 Leo 文件

打开浏览器，输入 URL:http://localhost:8130/，可以看到 leo 提纲中的节点信息：

![20180609-browse_leo](http://p32dsli77.bkt.clouddn.com/20180609-browse_leo.jpg)


### 从浏览器中保存书签至 leo

在此处，选择 Chrome 浏览器演示，在浏览器书签栏中，右键-->添加网页，将下列脚本添加至网址一栏：

```
javascript:w=window; d=w.document; ln=[];if(w.location.href.indexOf('one-tab')>-1){el=d.querySelectorAll('a');for (i in el){ln.push({url:el[i].href,txt:el[i].innerHTML});};};w.open('http://localhost:8130/_/add/bkmk/?&name=' + escape(d.title) + '&selection=' + escape(window.getSelection()) + '&ln=' + escape(JSON.stringify(ln)) + '&url=' + escape(w.location.href),"_blank","toolbar=no, location=no, directories=no, status=no, menubar=no, scrollbars=no, resizable=yes, copyhistory=no, width=800, height=300, status=no");void(0); # NOQA
```

设置名称为`Add to leo`。

添加书签：

<img src="http://p32dsli77.bkt.clouddn.com/20180609-add_a_bookmark.png" width="50%" heigint="50%" align="middle" alt=""/>

继续在 workbook.leo @settings 节点中添加书签保存位置，例如：

```
@string http_bookmark_unl = C:/users/laihetao/Home/.bookmarks.leo#@url bookmarks
```

效果：

<img src="http://p32dsli77.bkt.clouddn.com/20180609-bookmarks_example.jpg" width="50%" heigint="50%" align="middle" alt=""/>

## 批量保存标签

根据[文档](https://github.com/leo-editor/leo-editor/blob/master/leo/plugins/mod_http.py)中的介绍，可以选用 Chrome 插件 OneTab 作为批量导入书签的工具。

按照好 OneTab 插件后，点击插件即可将浏览器中所有的网页收纳为书签链接：

<img src="http://p32dsli77.bkt.clouddn.com/20180609-OneTab-step2.png" width="10%" heigint="10%" align="middle" alt=""/>

<img src="http://p32dsli77.bkt.clouddn.com/20180609-OneTab-step1.png" width="50%" heigint="50%" align="middle" alt=""/>

点击 `分享为网页` ：

<img src="http://p32dsli77.bkt.clouddn.com/20180609-OneTab-step3.png" width="50%" heigint="50%" align="middle" alt=""/>


最后直接点击 `Add to tab` 插件后即可将所有链接保存。

**注意**：偶尔会出现保存不成功的情况，暂未发现是什么原因。

## 打开网页
### 打开单个网页

按 CTRL 键后，点击对应网页链接的的节点或链接即可自动打开链接。

## 批量打开网页

可以设置 button 按钮：

1.新建节点 `@button open-child-urls`
2.在节点内设置相应链接打开方式信息 (待补)。

## 中文乱码问题

>**注意**:browser_encoding常量（在此文件的顶部节点中定义）
必须与浏览器中使用的字符编码相匹配。 如果它不，非ascii
字符看起来很奇怪。

在 mod_http.py 中能设置 browser_encoding，但是针对中文标题的书签乱码问题暂未找到解决方法，以下是探索过程。


- leo书签导入信息出现乱码问题，Plugins — Leo 5.7.1 documentation
    "http://leoeditor.com/plugins.html?highlight=encoding "
    - 查找，在上述文章中，找到了`Note: the browser_encoding constant (defined in the top node of this file) must match the character encoding used in the browser. If it does not, non-ascii characters will look strange.`
    - 打开`mod_http.py¶`文件，在 找到了 browser_encoding = 'utf-8'
    - 打开浏览器，在开发者模式下，打开console，输入document.charset指令，得到 UTF-8，即与上述相同
    - 由此可见，并非是这方面的问题。分析javascript代码，发现js中escpae() 函数用来返回16进制数
      - 在Java中正确解码用Javascript:escape()编码的中文字符 - CSDN博客
        "https://blog.csdn.net/hongweibing1/article/details/76920772 "
      - 仔细阅读mod_http.py代码
      - 尝试切换到linux中，配置了leoSetting文件也没有奏效，不是平台的问题。
      - 将中文转码
        - Unicode编码转换 - 站长工具；
          "http://tool.chinaz.com/Tools/Unicode.aspx "
        - Title框中的 `%u9879%u76EE - Kesci.com` , 只要将 %u 替换成 \u，得到的就是中文转换成的 Unicode 码；
        - Title框中输入中文，在节点中得到的 `&#39033;` ,实际上是按照 ASCII（中文） 转 Unicode 处理的。对比上面，思路不太一样。
          - 即中文按照 ASCII 的逻辑转换为 Unicode
          - 以 `项` 为例
            - 中文 --> Unicode: 项 --> \u9879
            - 中文 --> ASCII: 项 --> \u9879
            - ASCII --> Unicode: 项 --> &#39033;

## 参考

- [leo-editor/mod_http.py at master · leo-editor/leo-editor](https://github.com/leo-editor/leo-editor/blob/master/leo/plugins/mod_http.py)
- [Plugins — Leo 5.7.3 documentation](http://leoeditor.com/plugins.html#mod-http-py)
- [Leo editor: Managing our bookmarks - YouTube](https://www.youtube.com/watch?v=9AvbL_0JEMw)

## CHANGELOG

- 20180609
