---
author: javen
comments: true
date: 2013-12-27 05:08:02+00:00
excerpt: "\n\t\t\t\t\t\t"
layout: post
link: http://blog.jiguang.cn/push_api_for_ios_env/
slug: push_api_for_ios_env
title: "\n\t\t\t\tJPush API 更新：iOS Push 支持推送时指定部署环境\t\t"
wordpress_id: 468
categories:
- 产品更新
tags:
- APNs
- iOS Push
---


				JPush Push API 更新增加了一个可选参数“apns_production”，用来指定这次推送的 iOS 部署环境：0. 开发环境，1:生产环境。与此同时，JPush Web Portal 上进行推送时，可指定这次推送的环境。

此功能是基于开发者的反馈来做出的：为了方便 iOS 应用上线后，部署环境设置为“生产环境”时，仍然需要进行 iOS 开发环境的推送测试。

对于老版本未指定 "apns_production" 参数的 API 调用，默认行为与之前相同：检查 JPush Web Portal 上当前应用的部署环境设置。如果指定了此参数，则按照此参数指定来进行这次 APNs 推送。

详细请参考推送 API 文档里的具体参数定义：http://docs.jpush.cn/display/dev/Push+API+v2

JPush 官方提供的 API Client Library 将在稍后的版本更新中逐步加上此特性。

		
