---
author: javen
comments: true
date: 2016-01-28 07:28:01+00:00
excerpt: "\n\t\t\t\t\t\t"
layout: post
link: http://blog.jiguang.cn/jpush_long_uid/
slug: jpush_long_uid
title: "\n\t\t\t\t极光推送系统升级支持 42 亿以上用户\t\t"
wordpress_id: 621
categories:
- 产品更新
tags:
- 大容量
- registrationID
- 系统升级
---


				前段时间，极光推送的开发同事在忙于一个"大工程“：系统整体升级支持 64位 uid（内部用户标识）。

坦白地说，极光[推送](http://www.jpush.cn)刚开始设计时，没有想到，至少没有想到这么快，用户量会超过 42 亿，即 32bit 数字的最大限度，所以 uid 只用了 Integer 数字类型。

但是，随着极光推送被越来越多的 App 作为必备插件，终端用户注册量也是越来越大，已经超过 30 亿。从而 SDK 到后台系统升级支持 42 亿以上的 uid 有些迫在眉睫了。

为了进行这个升级，后台系统一年前就开始做准备，服务节点逐步升级支持。SDK 则在早前一些的版本开始提供支持。Android Push SDK 在 1.8.2 版本开始支持 64bit，iOS Push SDK 在最近刚发布的 2.1.0 版本支持。

最近，服务器端正式对新安装的支持 64bit 的 SDK 使用放号超过 42亿的 uid。（老版本未支持 64bit 的 SDK 当然继续使用 32bit uid。）

这些动作是背后的，开发者集成 App 方面，几乎不需要做任何处理，只是希望开发者尽可能地升级到新版本。

上面说极光推送内部 uid 升级，对开发者几乎无影响。但是，有可能存在一个潜在的影响：如果您的推送要使用 registrationID，则建议关注下。[关于 registrationID 推送，请参考之前的博文](http://blog.jpush.cn/registrationid_pusn_launch/)。

如果您的 App 要基于极光推送提供的 registrationID 进行推送，则您在 App 上需要从 JPush SDK 获取到 registrationID，并且上报保存到 App Server 上。

这里的变化在于，之前 32bit uid 时 registrationID 的长度是 11 个字节，而现在 64bit  后 registrationID 长度增加至 19 个字节。如果 App Server 上保存 registrationID 时放到数据库（或者其他存储方式）严格限制了长度，则会导致保存失败。

建议使用 registrationID 做推送的 App 做一个检查。虽然我们认为，大多数时候，保存一个字符串不太会去设置严格的长度限制。

		
