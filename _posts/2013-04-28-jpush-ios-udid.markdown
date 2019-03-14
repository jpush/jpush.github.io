---
author: javen
comments: true
date: 2013-04-28 07:56:27+00:00
excerpt: "\n\t\t\t\t\t\t"
layout: post
link: http://blog.jiguang.cn/jpush-ios-udid/
slug: jpush-ios-udid
title: "\n\t\t\t\t关于 iOS 应用里使用 UDID\t\t"
wordpress_id: 310
categories:
- 产品更新
tags:
- OpenUdid
- UDID
---


				


相信不少开发者已经有了解到，苹果发布了公告，从 2013.05.01 开始，对调用 API 获取 UDID 的应用不允许更新上架（老的应用不受影响）。具体公告信息可以参考：[官方公告](https://developer.apple.com/news/index.php?id=3212013a)，[英文媒体报道](http://9to5mac.com/2013/03/21/apple-tells-developers-it-will-stop-accepting-apps-that-access-udids-on-may-1/)，[中文媒体报道](http://www.36kr.com/p/202078.html)。


对此 JPush iOS SDK 早于 2013-3-27 发布的 r1.3.0 版本开始，便不再提供 UDID 版本下载，只提供 OpenUDID  版本。OpenUDID 是通过手机的非私人信息生成的识别手机的标识，完全符合 Apple App Store 协议，可以放心集成。




极光提醒在App里仍然使用 JPush iOS SDK UDID 版本的开发者，在五月一号之后发布的版本里，一定要更新 JPush SDK 到最新的OpenUDID 版本。这个升级更新不需要特殊的集成步骤，只需正常的 SDK 升级操作就可以。详情请参考 [JPush 最新更新](http://docs.jpush.cn/pages/viewpage.action?pageId=3309737)。





		
