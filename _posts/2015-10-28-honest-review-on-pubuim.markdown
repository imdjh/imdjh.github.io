---
published: true
comments: true
layout: post
title: 团队电子邮件的终结者，瀑布IM
category: toolchain
tags: [team, discuss, hackable]
date: 2015-10-28 15:49:56 +0800
---


你说em@il都是什么年代的事情了，却仍是大数公司的*首要*交流方式。自web2.0开始就有很多业界大佬尝试开发替代传统电子邮件。如提出云计算的谷大哥用[gmail重新发命了email][itgonglun gmail]，quip用编辑时聊天，解决协同编辑时无法[快速沟通意见][quip quick review]想法的痛点。


今天我要介绍的是有更接地气的方案：瀑布IM。相对于瀑布IM的宣传定位，我更喜欢自己的考虑 - 可拓展在线办公平台。   
![pubuim logo](http://tp2.sinaimg.cn/5241138681/180/5734483693/0)

<br />
<br />

首先，无聊天不办公，我认为这是哪儿都是事实的*事实*。
试想你平日的工作环境，如果说一句和办公无关的闲话就会被发现扣奖金，那和牢笼有什么区别。这也就是团队协作服务（如tower，asana），所不足的地方。
瀑布IM可以干名字中**IM**能干的所有事情：发代码（有高亮，有缩进），发群聊@，发图片，发链接，发文件，还是跨平台无关硬件的（你相信么，瀑布IM能在xbox上用手柄操控）
还有喜闻乐见的点赞功能，成员置顶消息（或文件）。右边栏快速定位拓展服务的预览。（又叫DP，深度预览）
作为团队的leader，将不同的任务、团队划分为一个个的频道，可以让web端的不用听app组比比，中午是去星巴克还是来福士。

我说的**拓展**性，是指瀑布IM的拓展服务，我认为那是瀑布IM区别于*无处不约*马叔叔的**杀手锏**。

所谓*服务拓展*，简单来说就是触发第三方服务的API调用。很难懂对不对，实际上确实是这样。但瀑布IM是有内置的第三方服务**快速接入**，只需要频道管理员点几下鼠标就能完成，说真的，这些事项目经理都能干。git代码库更新接入，RSS订阅消息，Sentry错误反馈，tower任务跟新报告。有了这些，不愁你的聊天室缺少办公上实用和聊天时的欢乐。   
![tower服务展示][showbox tower]

为啥会**欢乐**呢？工作本就该*无奈无聊*不是么？因为瀑布IM开放出一些基础服务拓展，让这一切都是**hackable**的。我说的就是Outgoing/Commands, Incoming。   
![我自己用的频道中开启的服务][my plugins]

比如通过Outgoing，我们可以通过团队聊天中的关键词触发一些服务。我自己写了个[真随机数聊天机器人][trbot-pubuim repo]，通过roll关键词激发，让团队内部福利发放更加公平，更有游戏性。

而Incoming能让你制作满足自己实际情况的预警类服务。
> Logic will get you from A to B. Imagination will take you everywhere.
- Albert Einstein

说了不少好话，是不是有点[蠢蠢欲动][signup pubu.im]，瀑布IM真的有那么好（*理想*）么？其实就像所有处于beta的服务/软件，瀑布IM不是*完美*的，我在使用过程中遇到的不爽就挺多：

- 新成员加入后不刷新页面无法**群聊@**提及
- 浏览器**支持范围小**，团队中小盆友用的3xx浏览器就不行
- API**文档**可能会不同步（开发者知道到底API最新版本是咋用的，而文档没更新）
- **隐藏**的特性（*Bug as feature*?你知道吗，聊天框实际上是支持markdown的）


万幸的是，瀑布IM团队的服务支持非常棒！（我收到了CEO的邮件支持反馈和问候）使用中遇到的问题都可以通过：[support@pubu.im][support mail]和[hello@pubu.im][hello mail]得到及时的咨询和建议。
最后感谢Tang在开发随机机器人的时候给予的协同纠错，没错，我们用的**瀑布IM**一起解决的问题 ;-)


> Written with [StackEdit](https://stackedit.io/).

[support mail]: mailto://support@pubu.im
[hello mail]: mailto://hello@pubu.im
[signup pubu.im]: https://beta.pubu.im/signup
[itgonglun gmail]: http://ipn.li/itgonglun/123/
[quip quick review]: https://quip.com/about/tour
[trbot-pubuim repo]: http://v2ex.com/t/230888
[my plugins]: /img/2015/10/pubuim-myservices.png
[showbox tower]: /img/2015/10/services-tower.png

