---
layout: post
title: "zotero 使用入门"
date: 2018-06-09 18:00:00 +0800 
categories: zotero
tag: [zotero]
---   

## 前言

接触 zotero 源自于17年上半年，那时报名参加了开智的通识部落，阳志平老师时常在群里面灌水，在有关知识管理方面，他多次推荐了这款工具。其实，在阳老的博客上，关于该工具的使用入门指南已经陈列了多年，只是自己视而不见。此后我先后在 Mac、windows 安装和配置过相关插件，在此处主要记录几处坑点。

## 下载和安装

在官方的网站中提供了zotero4和最新的zotero5版本，我入门时先安装了zotero5版本，但却发现有一些好用的插件并未更新，以至于无法使用，于是我就换成了旧版本zotero4。在换成旧版本时可能会报错，原因很可能是之前版本的 zotero 存储数据库没有删除，根据提示:[kb:newer db version [Zotero Documentation]](https://www.zotero.org/support/kb/newer_db_version)，直接把上一个版本的 zotero.sqlite 数据库和 storage 文件夹等内容删除，重新同步。

## dropbox 配置
### dropbox 映射

由于zotero官方赠送的 storage 存储空间并不多，但是 zotero 作为云同步的资料管理工具，极易耗尽存储空间。因此，我们需要借助第三方的存储工具自动同步和保存附件，而 zotero 只保存题录。我选择 dropbox 作为第三方同步的云服务软件，选择它的主要原因是容量空间可以通过邀请注册的方式来增加容量......

Mac 中的映射方式：
```
ln -s Zotero目录/storage Dropbox目录/zotero/storage
```

或者在Windows下，打开命令行:

```
mklink /D Zotero目录/storage Dropbox目录/zotero/storage
```

### dropbox 无法登录问题

- 现象描述
    + 在国内，dropbox 服务时常受限，所以不得不借助代理来科学上网。我在使用 dropbox 时，莫名其妙的发现无法正常登录，奇怪的是 dropbox 账号密码以及 代理服务都正确无误。

- 解决方法
    + 在 dropbox 配置中，直接手动设置代理地址
        * 选择ss5代理
        * 设置地址及端口:127.0.0.1:1080
        * 完成后，向电脑发送ss的局域网

## 参考

- [Zotero入门六篇文章 - 阳志平的网志](https://www.yangzhiping.com/tech/)
- [大家都叫我老杨](http://blog.yesmryang.net/tags/Zotero/)

## CHANGELOG

- 20180609