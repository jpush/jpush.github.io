---
author: percy
comments: true
date: 2016-08-07 11:54:37+00:00
excerpt: "\n\t\t\t\t\t\t"
layout: post
link: http://blog.jiguang.cn/newfeture_notification_ios10/
slug: newfeture_notification_ios10
title: "\n\t\t\t\t产品运营利器再升级之［通知］－ iOS10通知更新详解\t\t"
wordpress_id: 1123
categories:
- 产品更新
---


				

# [![000](http://blog.jiguang.cn/wp-content/uploads/2016/08/000.png)](http://blog.jiguang.cn/wp-content/uploads/2016/08/000.png)





* * *





#  **_前言_**




## _为什么是［通知］而不是［推送］_


_先来看一下iOS10通知相关的第一个更新点就是新加了一个框架User Notification Framework，从字面翻译来看应该翻译成“用户通知框架”，而通常大家所了解的“推送”翻译成英文应该是“Push”，“Push”其实只是［通知］触发的一种方式，而［通知］其实是操作系统层面的一种UI展示。_

_在苹果的官方文档中Notification分为两类：_



	
  * _Remote（远程，也就是之前所说的Push的方式）_

	
  * _Local（本地，通知由本地的事件触发，iOS10中有三种不同的触发‘Trigger’方式，稍后会进行详细说明）_


_所以，［推送］只是［通知］的一种触发方式，而从iOS迭代更新的历史特征中看，［通知］应该是是被苹果作为一个重点内容来延展的。（从最初的单纯展示和简单回调，到Backgroud的支持，再后来整体的Payload的长度由256字节扩展到2K再到4K，再看这次的独立框架还有丰富的特性更新）_



* * *





# 




# 更新点（概览）




# 由User Notification Framework 整合通知相关方法看特性变化


通知相关的方法由之前一直存在在UIKit Framework中到独立出来，官方确实做了很多，但是也尽量做到让开发者可以平滑的过度。

原文：











<blockquote>

> 
> 

> 
> 
	
>   1. Familiar API with feature parity
> 
	
>   2. Expanded content
> 
	
>   3. Same code path for local and remote notification handling
> 
	
>   4. Simplified delegate methods
> 
	
>   5. Better notification management
> 
	
>   6. In-app presentation option
> 
	
>   7. Schedule and handle notifications in extensions
> 
	
>   8. Notification Extensions
> 


> 
> </blockquote>






释义：



	
  1. 相同的特性使用类似的API（之前的功能API使用方法类似但是还是稍有改变）

	
  2. 内容扩展（支持附件和展示更多内容）

	
  3. 本地通知和远程通知操作代码在相同调用路径（合并代理方法）

	
  4. 简化代理方法

	
  5. 更好的通知管理（支持通知查、改、删；增强本地通知管理，增加日历与地理位置事件的触发）

	
  6. 应用内通知展示（之前App在前台的情况下收到通知不会UI展示）

	
  7. 在Extensions中规划和操作通知（使更新通知内容和删除误发或过期的通知内容成为可能，另一个重要场景为端到端加密）

	
  8. 引入通知Extensions












* * *



_以下由我个人按照产品或运营可能感兴趣的新增特性总结归类_





















# 从产品&运营的角度来看更新点




## 增加Subtitle


Subtitle样式和展示位置如下图所示，Subtitle的加入给内容类App带来了福音，交给优秀编辑和策划去使用应该是一项利器。

[![111](http://blog.jiguang.cn/wp-content/uploads/2016/08/111.png)](http://blog.jiguang.cn/wp-content/uploads/2016/08/111.png)


## 




## 增加Attachments


通过类似之前的content_available参数的mutable-content参数来控制是否增加Attachments，需要开发者实现NotificationServiceExtension来展示带有Attachments的通知，需要注意的一点是，本地通知的话只能使用本地的资源，远程通知需要服务端发送URL给NotificationServiceExtension去预先执行下载操作，当然如果在网络不太通畅的情况下苹果也提供了超时时间和超时之后的后续操作让开发者在这种情况下也能适当的展示通知，从而提高通知交互体验。

Attachments的加入也可以让你更好的对发给用户的通知进行分类。

[![222](http://blog.jiguang.cn/wp-content/uploads/2016/08/222.png)](http://blog.jiguang.cn/wp-content/uploads/2016/08/222.png)


## 




## 新增本地通知Triggers


在本地通知新增了两种新的Triggers，就是日历和地理位置。日历的话就是让开发者可以根据指定的日期和时间来展示本地通知，并且支持循环条件，比如“每周二上午十一点”这种条件。地理位置的话就是在进入或者离开指定区域来触发这条本地通知，该特性让iOS通知的地理围栏触发有了实现的可能，比如“某品牌App在你进入该品牌线下店铺的范围内即展示最新优惠信息”等。

典型场景：



	
  * 循环提醒

	
  * 地理围栏


[![333](http://blog.jiguang.cn/wp-content/uploads/2016/08/333.png)](http://blog.jiguang.cn/wp-content/uploads/2016/08/333.png)


## 




## 内容扩展显示


如果设备支持3DTouch的话用力按压通知即可进入内容扩展页面，此页面会可以由开发者自定义展示内容，可以是之前Attachments的内容比如图片视频，也可以是开发者自己定义的布局内容，同时也支持在内容扩展页面增加更多的自定义ActionButton。但是，个人认为有一些遗憾的是扩展内容几乎不支持交互，交互就只能放到ActionButton里面了。

[![contentextension01](http://blog.jiguang.cn/wp-content/uploads/2016/08/contentextension01.png)](http://blog.jiguang.cn/wp-content/uploads/2016/08/contentextension01.png)












## [![contentextension02](http://blog.jiguang.cn/wp-content/uploads/2016/08/contentextension02.png)](http://blog.jiguang.cn/wp-content/uploads/2016/08/contentextension02.png)







## 




## 通知查、改、删


实现该功能需要有一个必要参数就是构建通知的identifer，后续的查改删操作都是根据此参数去执行的。

典型的应用场景：



	
  * 赛事比分变更

	
  * 通知撤回


[![modify01](http://blog.jiguang.cn/wp-content/uploads/2016/08/modify01.png)](http://blog.jiguang.cn/wp-content/uploads/2016/08/modify02.png)

[![modify02](http://blog.jiguang.cn/wp-content/uploads/2016/08/modify02.png)](http://blog.jiguang.cn/wp-content/uploads/2016/08/modify02.png)


## 




## WatchOS支持本地通知


此特性让AppleWatch可以在脱离iPhone的情况下更加的独立使用。

典型使用场景：



	
  * 运动目标达成

	
  * 计时器




## 增加Service Extension


让App开发者可以在展示通知之前增加一层处理逻辑，从而使端到端加密成为可能，也就意味着经由苹果的服务器的通知内容可以是完全的密文，在这之前iOS上实现通知内容加密是没有任何可能的。

典型应用场景：



	
  * 端到端加密

	
  * 添加Attachments


[![serviceextension01](http://blog.jiguang.cn/wp-content/uploads/2016/08/serviceextension01.png)](http://blog.jiguang.cn/wp-content/uploads/2016/08/serviceextension01.png)


## 获取通知相关设置信息API


可以通过API获取到用户设置该App相关于推送通知的设置的详细列表，该信息的统计可以让App的开发者更好的根据用户的通知使用习惯来改进通知的策略。

[![notificationsetting01](http://blog.jiguang.cn/wp-content/uploads/2016/08/notificationsetting01.png)](http://blog.jiguang.cn/wp-content/uploads/2016/08/notificationsetting01.png)


## 增加应用内通知展示API


提供官方的应用内收到APNs通知并做UI展示的API，在此之前如果想做此类功能需要开发者自己开发功能，此API的优势在于让开发者更简单的实现应用内展示通知的功能并且统一点击通知之后的事件。

[![inapppresent01](http://blog.jiguang.cn/wp-content/uploads/2016/08/inapppresent01.png)](http://blog.jiguang.cn/wp-content/uploads/2016/08/inapppresent01.png)












# 总结


以上更新点是由产品和运营的角度去看待这次iOS10对通知模块的重点更新，也希望广大开发者能借由新特性的更新做出更佳优秀的App，同时极光的开发同学也在日以继夜的做JPush iOS Client SDK和服务端对应iOS10的新特性适配与Demo，从而让极光的用户能更好的使用新特性去服务用户。


<blockquote>开发同学可以直接点击参考链接查看官方示例代码。</blockquote>





希望读者留下宝贵意见



* * *



苹果官方参考链接

Introduction to Notifications：[https://developer.apple.com/videos/play/wwdc2016/707/](https://developer.apple.com/videos/play/wwdc2016/707/)

Advanced Notifications： [https://developer.apple.com/videos/play/wwdc2016/708/](https://developer.apple.com/videos/play/wwdc2016/708/)


What's New in the Apple Push Notification Service：[https://developer.apple.com/videos/play/wwdc2016/724/](https://developer.apple.com/videos/play/wwdc2016/724/)





		
