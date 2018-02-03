---
published: true
comments: true
layout: post
title: how to use nm-connection-editor on a minial install CentOS 7 server
category: sysadmin
tags: [notes]
date: 2014-11-11 21:33 +0800
---

> 本篇博客属于归档性质，将先前记录于其他地方的文档收录回来。本文源自：
> http://my.oschina.net/doomred/blog/343213

<br>

#### With minial installation on CentOS 7, you can't run X client, you must install "X Window System" and "Fonts" group package.

{% highlight bash %}
$ sudo yum -y group install "X Window System"
$ sudo yum -y group install "Fonts"
$ sudo yum -y install nm-connection-editor
{% endhighlight %}

After installation you can run nm-connection-editor with __ssh -X -Y__

<br>

#### Search group package
{% highlight bash %}
$ yum group list hidden | grep X
 
$ yum group list hidden | grep Font
 
$ yum group list env | grep Minimal
{% endhighlight %}
