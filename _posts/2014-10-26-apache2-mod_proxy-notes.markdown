---
published: true
comments: true
layout: post
title: apache2 mod_proxy 注意事项
category: sysadmin
tags: [notes]
date: 2014-09-29 22:18 +0800
---

> 本篇博客属于归档性质，将先前记录于其他地方的文档收录回来。本文源自：
> http://my.oschina.net/doomred/blog/337350

1. 当重定向到本地(localhost)的某端口服务时，如 ProxyPass /redmine
http://localhost:3000
一定要注意
1. 那个软件系统的文件结构是不是相对路径，如果是，那么只能在apache的根网
页目录下使用相应的软连接
2. 如上事例所示，返回的url很可能没有redmine，所以最好还是不要这么干，直
接上子域名
我是指访问myname.com/redmine然后里面得链接全部都是
myname.com/project/...，如果上子域名就没这个问题
3. passenger(mod_rails)真TM不好用
