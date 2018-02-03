---
published: true
comments: true
layout: post
title: 火狐修改输入绑定
category: toolchain
tags: [firefox, keybinding]
date: 2014-09-29 22:18 +0800
---

> 本篇博客属于归档性质，将先前记录于其他地方的文档收录回来。本文源自：
> http://my.oschina.net/doomred/blog/323349

<br>

## 你曾想过在firefox的地址栏里使用emacs的c-a,c-e的keybinding吗？没关系，使用这个patch就可以：
{% highlight bash %}
$ echo gtk-key-theme-name = "Emacs" >> ~/.gtkrc-2.0 
{% endhighlight %}
