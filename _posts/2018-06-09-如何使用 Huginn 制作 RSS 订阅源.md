---
layout: post
title: "如何使用 Huginn 制作 RSS 订阅源"
date: 2018-06-09 19:00:00 +0800 
categories: rss
tag: [rss]
---   

## 前言

通过 RSS 方式订阅关注的文章和博客大约是 2012年的时候，那时我刚上大二。记得我最先使用的是 innoreader 和 feedly 两款软件。那时我并不是知道，通过这两款软件可以订阅自己想要关注的博客，因为我订阅的内容大多是国外的新闻，只需要在 app 中检索即可。这几年 innoreader 和 feedly 一直安装在我的手机中，但我却很少打开。近三年来，我愈发的对信息的管理感到焦虑，尤其是对碎片化的信息管理感到头疼。我生活中碎片信息源，很大一部分被微信公众号占据了。在前些年里，在我不怎么使用微信时，我是常常关注新闻的，然而近些年来，我发现自己对新闻的关注度下降了，虽然以前我也经常关注国外媒体的报道，比如经济学人、纽约时报等，但近些年却不太关注了。我很清楚，对于国内新闻的不关心是自己刻意为之的，但也有很大的被动因素，即我的注意力很大部分被微信占据了。我也曾尝试过借助爬虫将某人的微信公众号文章全部整理成 pdf，以便自己阅读，但是除去有一定的技术要求外，这也不是长久之计。

这两年，我也开始偶尔在自己的博客上写文章，开始关注别人搭建的博客上的文章，于是，很自然我就想到了 RSS 订阅器。在尝试了多种工具之后，我还是觉得 innoreader 更合自己胃口。就这样，innoreader 也开始成为我时常关注的重要渠道了。不过，这两个月，我发现自己越来越的难以忍受多平台的关注信息了，我想要的是将博客或公众号文章之类的信息整合到统一的平台当中，于是，我开始思考，如何才能将微信公众号的博客推送到 innoreader 上，而且能够实时更新。由于微信封闭的系统环境，只能借助于第三方的服务平台来构建 RSS 订阅的渠道。目前，网上常见微信公众号文章平台有[搜狗微信搜索](http://weixin.sogou.com/)、[狗耳朵](http://www.dogear.cn/weixin)、[传送门 -- 微信公众账号和文章的导航及推荐](http://chuansong.me/)等。我开始逐一关注这几个平台，发现搜狗微信搜索反爬虫机制时常更新，网上关于搜狗微信搜索的 RSS 订阅机制时常失效，所以必须自己构建服务器来实时抓取，且要很好的避开爬虫。狗耳朵则只有文字没有图片，且是收费平台，不太喜欢。剩下就是传送门了，传送门虽然不那么稳定，但能够方便的抓取文章。

我在 github 和 知乎上查找别人尝试的各种实现微信订阅的方式中明白到，必须构建自己的服务器，否则很容易被封禁。许多人都提到了 Huginn，我开始对它产生了兴趣。拖延再三，一日，我找到网上的相关帖子，终于实现了单个微信公众号的订阅。不过，离我预想的还是差距挺远，因为并不是很方便。本文主要简要的介绍了搭建 huginn 服务的方法。
  
## 什么是 Huginn?

### Huginn 一词的来源

>在北欧神话中，爱瑟神族(Aesir)的主神奥丁(Odin)拥有两只乌鸦。奥丁是胜利、诗歌、智慧和死亡之神。他双肩上栖息着两只乌鸦，一只叫尤金(Huginn)，另一只则为曼尼(Muninn)，分别代表着“思维”和“记忆”， 它们是奥丁的眼线，会将每日所见向主人报告。当别的神在饮宴时，奥丁便思索“思维”和“记忆”告诉他的话。

### Huginn 工具简介

>Huginn 是一个用于构建在线执行自动化任务的系统，它可以监控网页变化、聚合第三方应用并且根据设定的触发条件执行自动化操作，你可以将它看作是一个可以运行在自己服务器上的 Yahoo!Pipes + IFTTT。该工具的作者为Andrew Cantino，Github 上的项目地址为 https://github.com/cantino/huginn/， 已经有上万的 star，而且该项目活跃度非常高，一直都有人在更新代码。

其实说到底，Huginn 提供的就是一个能够实现烧制 RSS 的在线爬虫架构。

## Huginn 的配置

根据官方博客http://www.huginn.cn/blog/huginn/初学者向导 上给出的安装方式，在windows下需要通过虚拟机在 linux环境下配置。不过，为了免于在本地配置 huginn 安装环境的种种麻烦，我们可以借助第三方的服务器，在线搭建 huginn 服务，而无需在本地构建。详情，可根据官方教程[利用 Huginn 打造一站式信息阅读平台 | huginn](http://www.huginn.cn/blog/huginn/%E5%88%A9%E7%94%A8-huginn-%E6%89%93%E9%80%A0%E4%B8%80%E7%AB%99%E5%BC%8F%E4%BF%A1%E6%81%AF%E9%98%85%E8%AF%BB%E5%B9%B3%E5%8F%B0)。在这里，我将服务器部署到了 heroku ，每个月有四百多小时的免费使用时间。其它的，比如使用 vps 作为服务器就更佳了，不过我并未购买 vps 服务器。 

在这里，主要借助以下平台部署 Huginn:

- [Codeanywhere](https://codeanywhere.com/) 账号注册（用于 Heroku 环境配置）
- [Heroku](http://herokuapp.com/) 账号注册

具体的配置，请阅读[利用 Huginn 打造一站式信息阅读平台 | huginn](http://www.huginn.cn/blog/huginn/%E5%88%A9%E7%94%A8-huginn-%E6%89%93%E9%80%A0%E4%B8%80%E7%AB%99%E5%BC%8F%E4%BF%A1%E6%81%AF%E9%98%85%E8%AF%BB%E5%B9%B3%E5%8F%B0)

## Huginn 的使用

刚配置好 Huginn 环境后，如果不清楚如何使用，则可以下载官方提供的 scenarios 脚本库[http://huginnio.herokuapp.com/scenarios](http://huginnio.herokuapp.com/scenarios)下载使用。

在这里，我借助官方提供的微信公众号脚本库[http://huginnio.herokuapp.com/scenarios/17](http://huginnio.herokuapp.com/scenarios/17)，下载后，导入到自己搭建的服务器 Scenarios 中。

<img src="http://p32dsli77.bkt.clouddn.com/20180609-import-public-Scenarios.png" width="50%" heigint="50%" alt=""/>

由于该模板是以搜狗微信搜索为抓取对象，在我实际使用时，发现无法正常使用，搜狗微信搜索已经做了反爬虫处理，所以我调整为从传送门爬取，下面我以 `开智学堂公众号` 为 RSS 制作对象，调整如下：

1. 微信公众号 #1 搜索账号 (WebsiteAgent)
Schedule every_1d 
Keep events for 86400 
Propagate immediately false 
Disabled false 
```
{
  "expected_update_period_in_days": "4",
  "_comment": [
    "开智学堂"
  ],
  "url": [
    "http://chuansong.me/search?q=开智学堂"
  ],
  "type": "html",
  "mode": "on_change",
  "extract": {
    "url": {
      "xpath": "//*[@id=\"ld_pb7M6s_32122\"]/div[1]/div[1]/div/div/div[2]/div[1]/a",
      "value": "@href"
    }
  }
}
```

2.微信公众号 #2 获取文章列表 (WebsiteAgent)
Schedule Every 1d 
Keep events for 7776000 
Propagate immediately true 
Disabled false 

```
{
  "expected_update_period_in_days": "20",
  "url": [
    "http://chuansong.me/account/openmindclub"
  ],
  "type": "html",
  "mode": "on_change",
  "extract": {
    "url": {
      "css": "/html/body/div[2]/div[2]/div/div/div/div/div[3]/div[1]/div/div/div[2]/div/div[1]/div/div/div/div/div/div/h2/span/a",
      "value": "@href"
    },
    "title": {
      "css": "/html/body/div[2]/div[2]/div/div/div/div/div[3]/div[1]/div/div/div[2]/div/div[1]/div/div/div/div/div/div/h2/span/a",
      "value": "normalize-space(.)"
    }
  },
  "events_order": [
    [
      "{{_index_}}",
      "number",
      "true"
    ]
  ],
  "template": {
    "url": "{{ url | to_uri: \"http://chuansong.me\" }}"
  }
}
```

3.微信公众号 #3 去除重复 (DeDuplicationAgent)
Keep events for 15552000 
Propagate immediately true 
Disabled false 

```
{
  expected_update_period_in_days"20"
  lookback"0"
  property"{{title}}"
}
```

4.微信公众号 #4 获取单篇文章全文 (WebsiteAgent)
Schedule Every 1d
Keep events for 604800 
Propagate immediately true 
Disabled false 

```
{
  "expected_update_period_in_days": "20",
  "url_from_event": "{{url}}",
  "type": "html",
  "mode": "merge",
  "extract": {
    "desciption": {
      "css": "#js_content",
      "value": "./node()"
    },
    "title": {
      "css": "#activity-name",
      "value": "string(.)"
    }
  }
}
```

5.微信公众号 #5 输出RSS(DataOutputAgent)
Propagate immediately true 
Disabled false 

```
{
  "secrets": [
    "kaizhi_public"
  ],
  "expected_receive_period_in_days": "20",
  "template": {
    "title": "开智学堂",
    "description": "开智学堂",
    "item": {
      "title": "{{title}}",
      "description": "{{ desciption | replace: 'data-src', 'src' }}",
      "link": "{{url}}"
    },
    "link": "http://chuansong.me",
    "icon": "https://res.wx.qq.com/mmbizwap/zh_CN/htmledition/images/icon/common/favicon22c41b.ico"
  },
  "ns_media": "true",
  "events_to_show": "300"
}
```


![20180609-weixin-result](http://p32dsli77.bkt.clouddn.com/20180609-weixin-result.png)

## 使用体验

Huginn 的使用需要一定的技术门槛，不过上述介绍的方法难度并不高。在接触 Huginn 的一段时间里，还是能够看到许多不足的地方，尤其是在所有的配置正确的情况下，需要不断重启刷新才能够启动 Agent ，也许是 heroku 不够稳定的原因。

此外Heroku部署Huginn 会出现 Heroku 无法打开，且免费服务还不能运行24小时的情况，使用 Heroku 部署 Huginn 并非长久之计，所以，有必要的话，可以购买[VPS](https://wzfou.com/vps/)主机部署，感兴趣的话，可以参考[VPS主机排行榜单](https://wzfou.com/vps-bangdan/)

## Huginn 的其他玩法

### Huginn + IFTTT

- [使用Huginn打造自动化云端信息助手 - 简书](https://www.jianshu.com/p/a030246c330a)
- [huginn | huginn，抓取网页全文，抓取微信公众号，自动触发任务，定时监控网页变化，价格变化提醒，比IFTTT更强大的互联网自动化任务平台！](http://www.huginn.cn/)
- [使用Huginn+RSS构建个人信息中心 | 产品汪文森特](https://www.pmvince.win/2017/02/12/%E4%BD%BF%E7%94%A8Huginn+RSS%E6%9E%84%E5%BB%BA%E4%B8%AA%E4%BA%BA%E4%BF%A1%E6%81%AF%E4%B8%AD%E5%BF%83/)

## 参考

- [利用 Huginn 打造一站式信息阅读平台 | huginn](http://www.huginn.cn/blog/huginn/%E5%88%A9%E7%94%A8-huginn-%E6%89%93%E9%80%A0%E4%B8%80%E7%AB%99%E5%BC%8F%E4%BF%A1%E6%81%AF%E9%98%85%E8%AF%BB%E5%B9%B3%E5%8F%B0)
- [Codeanywhere](https://codeanywhere.com/)
- [Heroku](http://herokuapp.com/)
- [Huginn.io](http://huginnio.herokuapp.com/scenarios/30)
- [VPS主机 - 挖站否-挖掘建站的乐趣](https://wzfou.com/vps/)
- [VPS主机 - 挖站否-挖掘建站的乐趣](https://wzfou.com/vps/)
- [搜狗微信搜索](http://weixin.sogou.com/)、[狗耳朵](http://www.dogear.cn/weixin)、[传送门 -- 微信公众账号和文章的导航及推荐](http://chuansong.me/)

## CHANGELOG

- 20180609