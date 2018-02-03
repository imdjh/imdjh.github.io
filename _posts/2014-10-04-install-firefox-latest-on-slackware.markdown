---
published: true
comments: true
layout: post
title: slackware发行版本的30+火狐安装方法
category: sysadmin
tags: [notes]
date: 2014-10-04 22:11 +0800
---

> 本篇博客属于归档性质，将先前记录于其他地方的文档收录回来。本文源自：
> http://my.oschina.net/doomred/blog/324259

## TL;DR: 用slack-on-firefox这个脚本对已编译的ff二进制包重新打包，然后直接用对应的包管理器安装。
<br>

1) 下载slack-on-firefox:
{% highlight bash %}
$ git clone https://github.com/doomred/slack-on-firefox
{% endhighlight %}

2) 下载ff包：（欢迎推荐别的下载源）

[beta]https://www.mozilla.org/en-US/firefox/beta/all/
[stable]https://www.mozilla.org/en-US/firefox/all

3) 控制台：
{% highlight bash %}
$ sh /path/to/slack-on-firefox/sloff.sh path/to/downloaded/tarball/*.tar.bz2
{% endhighlight %}

4) 用相应的包管理器开始安装：（我这边是SalixOS)
{% highlight bash %}
$ sudo spkg -i /tmp/nameofgenerated.tgz
{% endhighlight %}
