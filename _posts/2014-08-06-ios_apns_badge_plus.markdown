---
author: javen
comments: true
date: 2014-08-06 14:58:01+00:00
excerpt: "\n\t\t\t\t\t\t"
layout: post
link: http://blog.jiguang.cn/ios_apns_badge_plus/
slug: ios_apns_badge_plus
title: "\n\t\t\t\tJPush iOS APNs 通知推送支持 badge +1\t\t"
wordpress_id: 552
categories:
- 产品更新
- 技术文章
tags:
- APNs
- JPush iOS
- APNs badge
- JPush iOS SDK
---


				JPush APNs Notification badge 支持 +1 的功能上线了，这对 iOS [消息推送](http://www.jpush.cn)通知非常有意义。

大量的 iOS 应用使用 JPush iOS 来推送通知，并且经常有群发的动作，即广播或者Tag推送。

在不支持 badge +1 时，群发的 APNs 通知其 badge 值只能大家都一样，默认是 1。

这不符合实际情况。

实际的情况是，App 在每个用户手机上，其 badge 值的定义不一样，但都有其业务含义。根据用户使用情况，badge 值每个客户端不同。

为了解决此问题，JPush 为每个客户端保存其特定的 badge 值。客户端有变更时，把 badge 值更新到 JPush 服务器。有新的推送时，把这个值 +1 推送下来（默认是 +1）。这样就符合实际的使用场景了。如下图所示：


![jpush_apns_badge_plus](http://blog.jiguang.cn/wp-content/uploads/2014/08/Snip20140806_72.png) 


为实现此功能，JPush 服务器端需要承受比较大的性能压力、并发压力。但我们认为，这个功能对 iOS 通知推送是非常有意义的，所以我们做了。

JPush iOS SDK v1.7.4 版本支持 setBadge 方法以更新 badge 值，请访问这里下载：[JPush iOS SDK](http://docs.jpush.cn/display/dev/iOS)。

JPush Push API v3 支持 badge +1 的语义。服务器端 SDK 部分，当前 [Java SDK v3.1.3](https://github.com/jpush/jpush-api-java-client/releases/tag/v3.1.3) 提供了此功能；其他语言的 SDK 会陆续提供。

 		
