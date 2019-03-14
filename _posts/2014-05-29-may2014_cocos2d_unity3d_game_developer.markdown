---
author: javen
comments: true
date: 2014-05-29 12:51:00+00:00
excerpt: "\n\t\t\t\t\t\t"
layout: post
link: http://blog.jiguang.cn/may2014_cocos2d_unity3d_game_developer/
slug: may2014_cocos2d_unity3d_game_developer
title: "\n\t\t\t\tMay.2014 近期产品更新：为游戏开发者\t\t"
wordpress_id: 516
categories:
- 产品更新
tags:
- 游戏开发
- Cocos2d-x
- Unity3d
---


				JPush 继本月初支持 Windows Phone 推送上线后，又为游戏开发者推了做特别支持的功能：



	
  * Cocos2d-x JPush SDK 插件

	
  * Unity3d JPush SDK 插件

	
  * Push API v3 支持应用内消息


上述 2 个游戏开发引擎的 JPush SDK 插件，都同时支持 Android 与 iOS 版本。详情请参考文档：

	
  * [https://github.com/jpush/jpush-cocos2d-x-plugin](https://github.com/jpush/jpush-cocos2d-x-plugin)

	
  * [https://github.com/jpush/jpush-unity3d-plugin](https://github.com/jpush/jpush-unity3d-plugin)


由于开源在 github 上，欢迎程序猿们提交 Pull Request 来一起改进。

相信有了这两个插件，大部分基于这两个引擎做游戏开发的程序猿，不必去折腾 Android, iOS 的集成了。

[Push API v3](http://docs.jpush.cn/display/dev/Push-API-v3) 是 JPush 全新重构的 API 版本，积极参与 github, Twitter, Google 等大互联网公司提供的 REST API 标准，内容包全 JSON 化，可以命令行快速调试。

并且 Push API v3 也增加了若干功能，其中一个功能点是很多游戏开发者期待已久的：iOS 支持应用内消息通过 API 推送，这样 Android 与 iOS 方便一起做 IM 类沟通。

Push API v3 的其他功能点还有：



	
  * 多推送条件的灵活组合。比如，要给广州、深圳的“女”性“会员”推送。

	
  * 同时支持通知与消息的推送。

	
  * 支持“部分推送”：即灵活地组合用户属性，比如手机型号、分发渠道、App版本号，以及用户活跃情况、用户所在城市等众多条件来进行推送。（稍后开放，敬请期待）




 

		
