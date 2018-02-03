---
published: true
comments: true
layout: post
title: 如何使用docker让站点瞬间拥有只读网盘功能
category: toolchain
tags: [Nginx, docker, storage]
date: 2015-10-29 22:25:38 +0800
---

## 准备工作
> docker套件   
某个版本的nginx套件   
一个包括所有想要分享的文件夹

## 初始化配置文件
{% highlight bash %}
$ sudo docker run --name=bad-nginx -d nginx:speci
$ # 启动一个容器，我们需要从中提取最新的配置文件

$ sudo docker cp bad-nginx:/etc/nginx/conf.d/default.conf my.conf
$ # 将运行中容器的配置文件复制到物理机

$ sudo sed -i 's/^[[:space:]]*index.*;$/autoindex on;/' my.conf
$ sudo sed -i 's/^[[:space:]]*#[[:space:]]*charset.*$/charset utf-8;/' my.conf
$ # 打开nginx的自动索引，将网页设为utf-8编码，因为Linux Rocks！！


$ mkdir this
$ # 创建一个文件夹，用以包含配置文件，挂载到docker容器内

$ sudo mv my.conf this/default.conf
$ # 将配置文件复制进去，其实不是default.conf这个文件名也行

$ sudo docker rm -f bad-nginx
$ # 删掉临时容器，完成配置初始化
{% endhighlight %}

## 启动服务吧！

{% highlight bash %}

$ sudo docker run --name=tmp-disk-nginx --rm -ti -p 0.0.0.0:80:80 \
-v /path/to/folder:/usr/share/nginx/html -v /path/to/this/:/etc/nginx/conf.d \
nginx:speci
$ # 挂载想要共享的文件夹/path/to/folder， 将patch过得nginx配置文件扔到容器里。
{% endhighlight %}
