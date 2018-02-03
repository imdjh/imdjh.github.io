---
published: true
comments: true
layout: post
title: "A guide to fetch USB info under Linux"
category: toolchain
tags: [CN, usb]
date: 2015-04-24 20:16:14 +0800
---
## 事情是这样的：    ##
我的一台电脑被我格式化了，浏览器书签, XMind记录，配置文件啥的我都已经保留好了，但是，但是我居然忘记了保留PGP私钥。
当时想着身边没有什么能久放的存储介质也就没有去做备份。可是现在连Revoke Key也都消失了，还好我用那个key时候也没什么建树，也不知是喜是悲。    
      
         
# lsusb -vvv #      
结果中，你可以看到VID和PID，别听网上瞎bb（这两个值都是可以量产改的），VID是厂商名（通常是主控厂商），PID是产品的唯一ID （相对于VID的唯一，通常是指哪个主控产品，只要是正规的厂家，PID都是可以在usb.org上查到的，我下文会说）
    
%%MORE%%   
# 查USB厂商的datasheet #    
当然，有些太老的产品可以用Wayback Machine

# 从 usb.org 上查找信息 #
里面会有这个产品的所属USB类别（高速，低速一类），用途类别等信息
[usb.org - Product Search](http://www.usb.org/kcompliance/view)

# Extra, 如何在Linux中找到一个U盘的真实大小 #    
/sys/block/sd?/size -> sector数    
/sys/block/sd?/queue -> 一个sector的大

