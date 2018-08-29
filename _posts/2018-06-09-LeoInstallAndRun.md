---
layout: post
title: "leo 安装与运行"
date: 2018-06-09 12:00:00 +0800 
categories: leo
tag: [leo]
---   

## 前言

此前[大妈](https://github.com/ZoomQuiet)在怼圈时已经多次强调了 leo 的妙用了，我也直接参与到了由[OMlalala](https://github.com/OMlalala)组织的 leo editor 的官方文档的翻译当中。在官方文档的[序言](http://leoeditor.com/preliminaries.html)中，有不同的使用者对 leo 给予了很高的赞誉，如果您感兴趣，可以直接查看官方文档的介绍，或许人们的用后感想会激发你对它的兴趣。我在看完 leo 爱好者的评价后，意识到 leo 仿佛有无所不能的用处，例如写作、编程、信息管理、密码的管理、菜谱管理、书签的管理等方面都有极强的适用性。

不过话又说回来，此前由于我将精力主要放在了文档的翻译上，以至于疏忽了对工具的掌握。毕竟这是一项工具，是工具就应当在实际的写作、编程等方面加以使用，不过遗憾的是，我近几个月都不怎么行文或写代码。当然，虽然如此，耳濡目染良久，我还是掌握了不少基本的用法，其实上手该工具，最快捷的方式是根据官网给出的 [Leo Basics Step By Step](http://leoeditor.com/slides/leo-basics-step-by-step/leo_toc.html) 教程来简单入门，对界面的熟悉，对常用快捷键的熟悉。在此之后，就可以随心所欲的使用它了。

## 什么是 leo?

leo 其实是一种提纲 (outline) 形式的文本编辑器，而文本编辑器中的提纲则是由一个又一个的节点 (node) 节点组成。每个节点的标题 (headline) 都包含了正文(body text)，而且每个节点之间可以随意的变动位置。当然了， leo 也有克隆的功能，不过我用的不多，还不清楚在哪里可以运用。其实 leo 的入门门槛挺高，在编程和写作中运用起初并不容易。

## 安装与运行
### 如何安装 leo？

官方给出的文档[Downloading, Installing & Running Leo — Leo 5.7.3 documentation](http://leoeditor.com/getting-started.html)已经详细介绍了如何进行安装。

根据官方文档指引，leo 的最小依赖项环境为：

```
Python 2.6 或更高的版本, 包括所有的 Python 3.x 版本.
PyQt4 或 PyQt5.
```

为了更便捷的进行安装，我们直接在命令行中通过[pip](https://pypi.org/project/pip/)进行安装，如果您的 pip 已经配置完毕，可以根据 python 版本的不同，按照如下方式安装：

- python3 版本
```
pip install leo
```

python3 版本的安装会自动将所有依赖项安装完成。

- Python 2 版本使用 Anaconda 进行安装:

```
conda install pyqt
pip install leo
```

- Python 2 版本不使用 Anaconda  进行安装:
  + 先安装 [PyQt](https://riverbankcomputing.com/software/pyqt/intro)
  + 再安装 Leo:
```
pip install leo
```

详情可以安装官方文档[Installing Leo with pip](http://leoeditor.com/installing.html#installing-leo-with-pip)来执行。

### 如何运行 leo

在安装完毕 leo 后，只需要在命令行中输入 leo 进行安装。

假如遇到在 python3 版本中安装无误后，出现打开 leo 无响应的情况时，请进行如下操作：

先打命令行，进入 python 编辑：

```
import leo
leo.run() # runs Leo, opening a new outline or,
leo.run(fileName=aFileName) # runs Leo, opening the given file name.
```

可能会提示如下错误：

```
AttributeError: 'NoneType' object has no attribute 'QObject'
```

比如，我使用的是 python3.5 ，根据[Leo does not work with PyQt5 version 5.5.0 · Issue #201 · leo-editor/leo-editor](https://github.com/leo-editor/leo-editor/issues/201)提示，在[PyQt - Browse /PyQt5 at SourceForge.net](https://sourceforge.net/projects/pyqt/files/PyQt5/)中找到对应的 PyQt5 的版本[PyQt - Browse /PyQt5/PyQt-5.6 at SourceForge.net](https://sourceforge.net/projects/pyqt/files/PyQt5/PyQt-5.6/)，下载32位或64位版本，运行`PyQt5-5.6-gpl-Py3.5-Qt5.6.0-x64-2.exe` ,并将其安装至 对应的 python 版本的安装目录下，即与 python.exe 在同一目录，安装完成后，就可以顺利运行了。

**注意**：上述这个坑可能会以不同形式出现，如果无法解决，请及时 google，或者求助 [leo-editor - Google 网上论坛](https://groups.google.com/forum/#!forum/leo-editor).

## CHANGELOG

- 20180609