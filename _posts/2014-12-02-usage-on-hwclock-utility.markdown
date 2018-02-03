---
published: true
comments: true
layout: post
title: 如何理解hwclock的输出与设置？
category: toolchain
tags: [usage, sysadmin]
date: 2014-12-02 08:11 +0800
---

> 本篇博客属于归档性质，将先前记录于其他地方的文档收录回来。本文源自：
> http://my.oschina.net/doomred/blog/351273

####  别被那群不懂时间的M$开发人员给拖低了智商, 改变从将硬件时间设为UTC时区开始！
<br>
<br>

那么你会说，怎么检查我的硬件时间是不是utc时间呢？？

{% highlight bash %}
$ hwclock --localtime
{% endhighlight %}

如果上面这条命令的输出和UTC(GMT)时间同样的话，那就对啦，否则，还是看看下文吧。

有些操作系统会很傻X的不分 __hwclock__ 与 __localtime__ （是M$, 如果你没听出来我指的是谁的话），当然了，我们的先进多用户操作系统是不会这么干的，通常来说一台POSIX机器总是会把机器内部时钟调整到UTC（也就是格林威治(GMT)时间），然后再通过hwclock这个工具在启动时，由内核读取转换为sysclock，通常来说x86平台下，sysclock要比hwclock更精准。

# 背景知识说了那么多，到底怎么将hwclock设置为UTC时间呢？

<br>

## 一、离线手动调

1) 将UTC时间设为本地时间，以便设置hwclock
{% highlight bash %}
$ sudo cp /usr/share/zoneinfo/UTC /etc/localtime
{% endhighlight %}


2) 时间需要是格林治时间（或者称为UTC时间）

{% highlight bash %}
$ sudo date MMDDhhmm[YEAR][.SS]
{% endhighlight %}


3) 将sysclock同步到硬件时间（就是BIOS里面的时间），同时指定硬件时间已经是UTC时区

{% highlight bash %}
$ sudo hwclock --systohc --utc
{% endhighlight %}

4) 将本地时间设为北京时间（没错，没有Beijing这个zoneinfo），不信自己 __tzselect__

{% highlight bash %}
$ sudo cp /usr/share/zoneinfo/Asia/Shanghai /etc/localtime
{% endhighlight %}

5) 将检查本地时间，是不是改回CST时区(+0800)来了。

{% highlight bash %}
$ date
{% endhighlight %}

****
<br>
很让人无语的是，man手册也里关于 __--localtime__ 的说明，实际上仅仅是和 __--set__ __--systohw__ __--ajust__ 关同时用的。让我们用debug信息说出事实（也是唯一的不看source得知的唯一途径）:

{% highlight bash %}
$ sudo hwclock --localtime --debug  #假定硬件时间是本地zoneinfo的时区
hwclock from util-linux 2.27
Using the /dev interface to the clock.
Assuming hardware clock is kept in local time.
Waiting for clock tick...
...got clock tick
Time read from Hardware Clock: 2015/10/07 13:50:21
Hw clock time : 2015/10/07 13:50:21 = 1444197021 seconds since 1969
Time since last adjustment is 1444197021 seconds
Calculated Hardware Clock drift is 0.000000 seconds
Wed 07 Oct 2015 01:50:20 PM CST  .858632 seconds

$ sudo hwclock --utc --debug        #假定硬件时间是UTC时区
hwclock from util-linux 2.27
Using the /dev interface to the clock.
Assuming hardware clock is kept in UTC time.
Waiting for clock tick...
...got clock tick
Time read from Hardware Clock: 2015/10/07 13:50:54
Hw clock time : 2015/10/07 13:50:54 = 1444225854 seconds since 1969
Time since last adjustment is 1444225854 seconds
Calculated Hardware Clock drift is 0.000000 seconds
Wed 07 Oct 2015 09:50:53 PM CST  .921260 seconds
{% endhighlight %}

<br>

## 二、systemd MAGIC
{% highlight bash %}
$ sudo timedatectl set-time "yyyy-MM-dd hh:mm:ss"
{% endhighlight %}
#### 就这么搞定了，systemd woo-yeahh!!

<br>

## 参考资料：

[http://www.linuxfromscratch.org/hints/downloads/files/time.txt](http://www.linuxfromscratch.org/hints/downloads/files/time.txt)    
[how to set hwclock to utc via:
stackoverflow](http://serverfault.com/questions/496076/how-to-set-hwclock-to-utc)



