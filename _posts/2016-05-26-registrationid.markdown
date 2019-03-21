---
author: javen
comments: true
date: 2016-05-26 09:39:15+00:00
excerpt: "\n\t\t\t\t\t\t"
layout: post
link: http://blog.jiguang.cn/registrationid/
slug: registrationid
title: "\n\t\t\t\t极光推送的设备唯一性标识 RegistrationID\t\t"
wordpress_id: 986
categories:
- 技术文章
tags:
- android
- iOS
---


				对于 App 云平台系统，如何唯一地识别移动设备是非常重要的。否则，每次用户在设备上卸载掉应用再重新安装，后端系统只能把这个用户当作一个全新的用户了。

Android 上识别设备的唯一性，因为这个圈太乱，所以设备本身的任何标识都是无法直接用作设备唯一标识的。iOS 是系统强力限制被唯一识别的，目前唯一可以部分满足条件是 IDFA，但需要你的 App 本身的确嵌入了广告。

很多开发者使用极光推送时，都有这个疑问：极光推送是如何来做设备的唯一性识别的。本文解析极光推送如何尽可能地来唯一识别设备。

极光推送对安装在设备上的 App 使用 RegistrationID 作为标识。极光推送要『尽可能』确保设备的唯一性，就是要使得 RegistrationID 尽可能唯一。


### RegistrationID 的定义


关于 RegistrationID 极光官方文档有如下的定义：


<blockquote>集成了 JPush SDK 的应用程序在第一次 App 启动后，成功注册到 JPush 服务器时，JPush 服务器会给客户端返回唯一的该设备的标识 - RegistrationID。JPush SDK 会以广播的形式发送 RegistrationID 到应用程序。</blockquote>


有了这个标识，App 编程可以把这个 RegistrationID 保存到自己的应用服务器上，然后就可以根据 RegistrationID 来向设备推送消息或者通知。


### RegistrationID 变化可能性


如果 App 不卸载，是直接覆盖安装，Android, iOS 上 RegistrationID 的值都不会变化。

如果 App 是卸载之后再次安装：



	
  * Android 上 RegistrationID 基本不会变；

	
  * iOS 上如果启用了 IDFA 变化可能性不大，如果未启用 IDFA 则每次安装 RegistrationID 都会变；




### RegistrationID 生成规则解析




#### Android 平台


Android 上因为国内存在大量山寨设备的原因，正常的 IMEI, Mac Address, AndroidID 这些可以考虑用作唯一标识的值，都是不可以用的，因为这些值在一批设备中可能都是同一个值。

极光的基本思路是：



	
  1. 生成一个 DeviceID 保存到 Settings, External Storage。依赖本地存储，应用被卸载后重新安装这些存储里的 DeviceID 还在的话，就是同一个设备。这一条理论上解决 90% 的不变性问题。

	
  2. DeviceID 之外增加补充规则：综合根据 IMEI, MAC Address, AndroidID 这几个值来判断，是否可能是老设备。


具体的逻辑细节，也是根据实际运行情况，以及收集到的反馈不断调整的，大多数逻辑可在服务器端调整。


#### iOS平台


鉴于 iOS 系统设计上限制设备唯一标识，所以极光一直使用 Device Token 作为标识，也因为极光推送本身就是需要 Device Token 这个值才可能运作的。

iOS 9 版本之后，每次卸载后重装都会导致 Device Token 变化，所以对于极光后台来说，都只能被识别为新用户。

极光 SDK 新版本增加了 IDFA 选项，在集成初始化 SDK 时可选把 IDFA 这个值设置进来，这样极光后台就优先根据 IDFA 值来识别用户，从有一定的可能性应用被卸载后重装还能识别回老设备。

IDFA 是广告标识符，是 iOS 专门为广告跟踪唯一地识别用户而设计的。在 iOS 设备上，设备 -> 隐私 -> 广告这个页面，有一个设置项：限制广告跟踪。默认是未选中状态的，即是关闭状态，是不限制的。用户可以选中，从而限制广告跟踪。设置项之外还有一个按钮：还原广告标识符...。如果用户点击了这个按钮，则 IDFA 值会变化。


[![2115454-b0c648ae038d8d0a](/images/2016/05/2115454-b0c648ae038d8d0a.png)](/images/2016/05/2115454-b0c648ae038d8d0a.png)






默认的情况下，没有限制广告跟踪，可以取到 IDFA 这个值。并且用户未点击『还原广告标识』时，这个值是不会变的。这样就达到了唯一地标识设备、跟踪到用户的目标。

但是，但是，请一定留意，IDFA 并不是一定可以启用的，是需要你的 App 的确有广告功能才可以用的，否则 Apple 在上架审核时有可能发现从而拒绝上架。

关于苹果 App 上架对 IDFA 的要求，可参考这里的说明：[The Advertising Identifier (IDFA) ](https://developer.apple.com/library/ios/documentation/LanguagesUtilities/Conceptual/iTunesConnect_Guide/Chapters/SubmittingTheApp.html#//apple_ref/doc/uid/TP40011225-CH33-SW8)


### 高级使用建议


因为 RegistrationID 是 JPush SDK 注册完成之后才得到的，所以调用 SDK API 来获取 RegistrationID 的值时需要稍注意，不是总能够立即得到。

比如 iOS 上建议在监听到 kJPFNetworkDidLoginNotification 这个通知后的代码里，来获取 RegistrationID 的值。		
