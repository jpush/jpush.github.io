---
author: javen
comments: true
date: 2016-01-13 01:36:21+00:00
excerpt: "\n\t\t\t\t\t\t"
layout: post
link: http://blog.jiguang.cn/jmessage-uikit/
slug: jmessage-uikit
title: "\n\t\t\t\tJMessage UIKit 预览发布\t\t"
wordpress_id: 611
categories:
- 产品更新
tags:
- JMessage
- 极光IM
- UIKit
---


				JMessage 作为 JPush 的兄弟云服务，自上线后，受到了广大开发者的热烈欢迎！

JMessage 尤其受到原有 JPush 开发者的欢迎，因为它完善地兼容 JPush，并且与 JPush 共享通道。连接又共享，开发又共享，是当然的选择！

完全基于 JMessage，我们官方提供 JChat app，一方面用于 Demo JMessage 的主要使用场景 - IM 聊天，另一方面也可直接拿来使用。JChat 开放源代码在 github 上，可以通过修改直接拿来源代码放到自己的 App 里。

但是，JChat 是完整的 App，逻辑不可避免地稍复杂。而有些 App 集成 IM SDK，只需要相对简单的某部分功能，比如聊天界面。参考重用 JChat 就看起来相对复杂。

基于开发者的这个需求，我们把 JChat 里相对复杂的功能模块抽取出来，作为相对独立的 UIKit （UI组件）项目。比如聊天界面，可以只看 Chatting 这一个组件，或者复杂这部分代码到自己的项目里。

当次我们整理了 4 个 UI 组件：



	
  * Chatting：聊天界面。支持单聊与群聊，支持文本、语音、图片等。

	
  * GroupChatDetail：群聊详情界面相对复杂，需要列表群聊所有成员。

	
  * MultiSelectPhotos：从相册选取图片发送时，要支持多选，而默认的系统相册不支持多选，所以我们自己开发了这个组件。Android, iOS 都有。

	
  * VoiceRecorder：语音录制组件。


以上每个组件的项目首页的 README 都有说明，如何使用这些组件。

使用方式上，你可以有如下几个姿势：

	
  * 把组件相关的源代码 Copy 到你的项目里，参考配套的 Demo 来在自己 App 里调用。如无需要，可以不必改动组件里一行代码。也可以修改相关资源文件或者代码，来做 UI 效果方面的适配。

	
  * 把组件的编译结果直接打包放到项目里，Android/iOS 都支持这种使用方式。这样组件代码都不改动。

	
  * 参考组件代码，来了解使用 JMessage SDK 的姿势。当你只关注 JMessage 用在某个部分时，参考这些组件的代码，相对简单。


JMessage UIKit 按照惯例，开源放在 github 上。

	
  * Android UIKit 项目地址：[https://github.com/jpush/jmessage-android-uikit](https://github.com/jpush/jmessage-ios-uikit)

	
  * iOS UIKIt 项目地址：[https://github.com/jpush/jmessage-ios-uikit](https://github.com/jpush/jmessage-ios-uikit)


我们还在不断更新优化中，欢迎您向我们反馈意见与建议。可以 QQ 群里沟通，也可以在 github 上直接创建 issue 或者提交 pull request。您的反馈非常宝贵，能够让我们持续地改进！		
