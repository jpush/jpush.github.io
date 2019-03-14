---
author: javen
comments: true
date: 2014-02-18 01:00:21+00:00
excerpt: "\n\t\t\t\t\t\t"
layout: post
link: http://blog.jiguang.cn/registrationid_pusn_launch/
slug: registrationid_pusn_launch
title: "\n\t\t\t\tRegistrationID 精确对点推送\t\t"
wordpress_id: 476
categories:
- 产品更新
tags:
- jpush
- android push
- Android SDK
---


				随着开发者对 JPush 的使用越来越深入，不少开发者越来越想要非常精确地定位到每一个设备安装（应用）。为此，JPush 开放了基于 RegistrationID 推送的功能。

JPush Android SDK r1.6.0 更新，主要增加了对 RegistrationID 的支持；并且服务器端 Push API 也新增此支持。另外，也改进了统计分析相关功能，以便 JPush 可以基于更完善的统计来改进推送效果。

JPush RegistrationID 是这个版本 SDK 引入的一个新概念，它唯一地标识当前这个应用安装在这个设备上。或者说，相当于是 JPush SDK 内部的用户ID。

开发者可通过 App 新安装后 JPush SDK 内部注册成功时发出的 Broadcast 来得到到这个 ID，也可以通过调用 JPushInterface.getRegistrationID() 方法来得到这个 ID。

使用上，开发者取得 RegistrationID 后，上传到自己的服务器端。之后就可以在自己的服务器调基于这个 RegistrationID 来进行[消息推送](http://www.jpush.cn)。

JPush 之前一直支持基于 Alias （别名）的点对点推送方式，那为什么还要开放 RegistrationID 的点对点推送方式呢？与 Alias 相比，RegistrationID 的有什么不同，或者说优势、劣势呢？

本质上 RegistrationID 与 Alias 类似，其目的都是唯一地定位到应用安装在一个特定的设备上。而本质的不同在于，Alias 是把映射关系保存到 JPush 的服务器上，而 RegistrationID 需要把映射关系保存到开发者业务服务器上。这个本质的不同，决定了其特点：



	
  1. Alias 有个 SDK 端设置绑定关系的问题。这个设置，有可能会失败，App 需要有处理这个暂时失败的逻辑。另外，Alias 是不限制绑定到多个设备的，这样要精确地定位到一个设备就不好控制。总而言之，这个绑定关系是没有那么精确一点的。

	
  2. RegistrationID 一旦生成，意味着这个设备的安装一定存在。App 把这个 RegistrationID 保存到自己的业务服务器上，可以非常精确地定位到这台设备（安装这个应用）。这样开发者的内部业务可以做比较复杂的用户关联关系。


或者说，使用 Alias 开发者更简单、方便。但使用 RegistrationID 更方便于精确地定位用户。

JPush iOS SDK 将于稍后发布的 SDK 版本支持 RegistrationID 推送。

Android SDK 具体的更新说明，请参考[最新更新页面](http://docs.jpush.cn/pages/viewpage.action?pageId=3309737)。RegistrationID 相关的文档有：[获取 RegistrationID API](http://docs.jpush.cn/pages/viewpage.action?pageId=8814639)；[接收推送消息 Receiver](http://docs.jpush.cn/pages/viewpage.action?pageId=1343602) 里关于从广播接受 RegistrationID 部分；[Push API v2](http://docs.jpush.cn/display/dev/Push+API+v2) 里关于 RegistrationID 推送参数的部分。统计分析功能相关的文档有：[统计分析 API](http://docs.jpush.cn/pages/viewpage.action?pageId=8323147) （Android SDK）。

		
