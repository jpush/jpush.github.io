---
author: javen
comments: true
date: 2016-03-15 05:48:51+00:00
excerpt: "\n\t\t\t\t\t\t"
layout: post
link: http://blog.jiguang.cn/jmessage-cross-app/
slug: jmessage-cross-app
title: "\n\t\t\t\tJMessage 发布跨应用聊天功能\t\t"
wordpress_id: 727
categories:
- 产品更新
tags:
- JMessage
- 极光IM
- 跨应用
- O2O
---


				近日，极光IM 支持跨应用聊天功能的 Android SDK 上线了，后台支持也同步上线。

此功能很受期待，因为对很多社交类应用非常重要，比如教育类的 App 一般都有教师端、学生端，O2O 类 App 一般都有消费者端、服务提供者端。这种一个业务存在多个角色来参与的，就有需要这些多角色之间能够交互聊天。

极光IM 支持跨应用聊天的功能，对原有体系设计、使用习惯改变不大，只要多个 App 注册在一个极光开发者帐户下，这多个 App 就可以互相找到对方的用户，然后进行聊天。

具体功能上，当前主要在 SDK 侧增加如下的 API：



	
  * 查询用户接口，增加支持 appKey 参数 - 可以查找其他应用的用户名；

	
  * 会话相关接口，增加支持 appKey 参数 - 会话可能会涉及其他应用的用户；

	
  * 发消息接口，增加支持 appKey 参数 - 消息可以发给其他应用的用户；


上述运作不包含 appKey 应用的接口继续存在，默认是本应用范围内。再次强调，上面所说的其他应用 appKey，应该是同一个 JPush 帐号里创建的。

该版本为 JMessage Android SDK v1.2.0，你可以看看[变更记录](http://docs.jpush.io/updates/#jmessage-android-sdk-v120)，或者[下载SDK](https://www.jpush.cn/downloads/sdk/android/)。iOS 支持跨应用聊天的版本稍后也将发布上线。有任何问题，请到[极光社区](http://community.jpush.cn)讨论。

[![jmessage1](http://blog.jiguang.cn/wp-content/uploads/2016/03/jmessage1.jpg)](http://blog.jpush.cn/wp-content/uploads/2016/03/jmessage1.jpg)		
