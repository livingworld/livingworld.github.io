---
layout: post
title: "2018年二级建造师考试"
date: 2018-06-03 16:00:00 +0800
categories: 其他
tag: []
---   

## 前言

今天是 2018 年二级建造师考试截止的日期，我在3、4月份的时候就报名了二级建造师的考试，并于5月初开始复习，为期一个月。在月初，我制定了学习方案，并在月中时做了一定的调整。我想借此过程来回顾和反思自己的学习过程。

## 计划制定

月初时，当我将一些重要的事情忙完之后，我才抽身投入到二级建造师的复习之中。我向来对自己的考试能力感到怀疑，于是我先想到的是，我需要找过来人取经。此前我已经反复多次询问，考过的人的经验，只是未能进行有效的记录，大多随着时间流逝而忘却了，我只能在茫茫网海中寻找种种过关 *秘籍*。很显然，我点开了知乎，上面有无数的热心肠的人的回答，不过，以有限的时间精力来看，我需要的是那种不用看书的过关秘籍。于是，我参照某位知乎答友的回复，形成了自己的复习计划，并借助 workflowy 撰写出来：

- 考试时间:6月1日和6月2日
- 考试总计划
    + 法规
      * 复习流程：陈印讲义-->结合讲义看陈印精讲班-->考前看冲刺班视频
      * 具体实施：
        * 1.看陈印大大的基础和精讲班的讲义和视频
        * 2.刷真题
        * 3.复习错题
      * 现有资料
        * 陈印讲义
        * 共有1266张真题卡片
        * 葵花宝典，陈印《二级建造师考试建设工程法规与相关知识》，共125页
    + 实务
      * 流程：讲义-->结合视频看讲义-->考前冲刺班视频
      * 具体实施：
      * 现有材料：
        * 讲义
        * 刘永强、李俊宏视频课
        * 共有781张真题卡片
    + 管理
      * 复习流程：龙炎飞和宿吉南（或朱俊文）的讲义-->选择性看龙炎飞和宿吉南的视频-->考前看蓝宝书和葵花宝典（
      * 具体实施：
        * 1.做一套真题，对出题方法有一定了解；
        * 2.选择两套讲义看一遍，相互对比查看，对知识点有大致了解和掌握；
        * 3.结合讲义看视频（选择性的），划重点。
        * 4.对讲义上重点进行复习，穿插做真题。
        * 5.结合蓝宝书和葵花宝典，回顾知识点考试。
      * 现有材料
        * 13.2018二建管理-ZY精讲班-朱.俊文【重点推荐】
        * 2018二建管理-ZY精讲班-朱.俊文 讲义
        * 11.2018二建管理-ZY面授班-朱.俊文
        * 共有1140张真题卡片
    * 心得：整理属于自己的讲义笔记。

## 实际实施

### 真题题库的制作

其实在5月初之前，我已经想过怎样进行复习了，即先过一遍书，再看视频。后来发现时间来不及了，才产生了上面的计划。然而，我是高估了个人的毅力，以及错估了复习的进度。

我在三四月份就已经购买了某网站的二级建造师的真题以及分章节的题库，在四月份的时候，抽空编写了一个 python 脚本，借助 selenium 将该网站的题库基本都下载下来了，我为什么要多此一举，放着好好的多平台题库不用呢？因为我想试着使用 anki 来刷真题。我从[二级建造师【anki记忆库】](https://zhuanlan.zhihu.com/p/33448401)那里受启发，也想制作水利专业的真题 anki 题库。于是，按照 [如何导入建造师题库到anki中](https://zhuanlan.zhihu.com/p/32757049) 的方法，先在 excel 中操作了一遍批量将题库导入 anki 的方法。后面，我才借助于 python 编程自动化了该过程，只不过是从题库网站抓取数据之后，以 anki 接受的文件格式保存的。关于如何抓取题库数据，我会在今后的博客中详细介绍。

题库数据保存在本地之后，我分别导入到 anki 中，并借助[【Anki模板】Monokai风格之随机选项多功能模板1.0（单选、多选二合一）](https://zhuanlan.zhihu.com/p/33337789)模板，该模板能够实现单选、多选二合一，非常不错。不过唯一遗憾的是该模板在安卓端不太好用，替代的方式是在手机 web 端打开，实现随身学习。改日有空我研究研究如何解决在安卓端无法正常传导的问题。

### 进度安排

由于题库的题目是付费才能查看的，所以我也是付费才得以将数据抓取下来的。我起初不太注意，以为题库中按章节的所有题目都是真题，直到我采用 anki 学习，随着时间的推移，我已无法每日投入越来越多的时间来学时，我才回头查看题库真题有多少。我惊讶的发现，其实真题只占有一部分，于是我讲所有非真题都剔除了，于是任务量降低了近 2/3。然而，题目量的降低，其实也让我挺松懈的，因为时间有了其他安排，但却不知如何安排。后来，我借助 PotPlayer 视频播放器，试图以两倍的倍数将法规、管理和实务听过去，后来越觉得不对味，只有实务从头到尾完整听下来了。

我是非常讨厌通看视频的方式来学习的，因为这种方式很容易让我的大脑停止运转。不过，从我后来的经验来看，视频还是要看的，不在于听视频的过程中听到了多少知识点，而是在于视频的老师划了哪些重点，比如实务老师说了哪些是地方今年可能会考案例题，哪些地方会考客观题等等。在视频听过之后，根据视频对应的讲义或者自己在书中划的重点，再整理成自己的知识库，比如可以使用类似于 anki 这样子的记忆题库。

话虽如此，我的实务也就是快速听过，快速将重点标记好的。不过可惜的是，我一直将实务的时间投入在了客观真题上，却并未背多少客观题的内容。在这次考试中吃了大亏，因为今年的客观题题型很偏，跟往年真题不带重的，但是实务的大题却有一些与往年真题题库知识点相同，只是，我没有将什么精力投入到真题的大题上，完全背道而驰的复习计划...

我是在考前的前几日才开始背实务大题的，其实二级建造师这种考试并不难，完全可以采用以前大学那种考前突击的方式考过的，我一位同学就是这样考过的，而且考的还是全国最高分的广东。我自然也是突击，但是时间太过有限，许多题来不及好好的再背几遍，以至于考试中，发现有不少只是眼熟。

下面两张图是我整个的复习时间统计，从图中可以看出，我的复习挺不稳定的，每日投入的时间也是上下波动起伏。其实考试就该是日日不断之功，但我好像也断过几次。

![20180603-bar](http://p32dsli77.bkt.clouddn.com/20180604-Bar.png)
![20180603-line](http://p32dsli77.bkt.clouddn.com/20180603-Line.png)

## 体会

- 复习当有日日不断之功。anki 能够有效的帮助做到这点，假如没有做到，下一日就会收到相应的惩罚，即复习的卡片复习不过来。
- 不过于高估自己的自制力，比如，中途我就开始将周末复习的阵地从住处转移至区图书馆，没想到学习效果出乎意料的好。
- 复习计划越早制定越好，后期就是不断的修改完善。
- 不过过度迷恋工具，比如对于 anki 的使用，在本次考试中，我就觉得没有好好抓住精髓，以为每日刷刷真题就 ok 了，结果真正踏足考场，我才发现没有用好 anki 。因为，如果说这门考试可以通过考前一周或更长时间突击的方式来通过的话，那么 anki 这种复习方式就显得成本太高了。那么，究竟该如何使用 anki 呢？视频+讲义+书本+自制卡片。真正的做成填空题或简答题的方式，而不是简简单单的选择题的方式来应对二级建造师考试。因为选择题的形式不容易让人记住知识点，题目做对很多时候是因为题目的其他信息左右的，所以要多做卡片。
- 计划是美好的，现实是骨感的。制定好的复习计划需要深入的考量，但是，对计划的执行确实需要情感的支撑，需要的是内在的动力。对于二级建造师这样如此枯燥乏味的考试，也只能从内心出发，找到驱动自己前进的动力。我在此过程中一直不太坚定，就像当初考研，后来的注册土木工程师基础考试一样，都是三心二意，老想着做其他的事情。但是，现实是紧迫的，我必须通过考证来使自己在职业上有更多的自信，并以期为自己带来一定的收入。

## CHANGELOG

- created at 20180603, 2.5h