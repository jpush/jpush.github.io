---
author: javen
comments: true
date: 2012-10-23 22:27:03+00:00
excerpt: "\n\t\t\t\t\t\t"
layout: post
link: http://blog.jiguang.cn/jpush_android_push_androidpn_xmpp_mqtt_user_activation/
slug: jpush_android_push_androidpn_xmpp_mqtt_user_activation
title: "\n\t\t\t\t通过 Push来提高 Android应用的活跃度\t\t"
wordpress_id: 94
categories:
- 技术文章
tags:
- 推送
- xmpp
- push
- open source
- androidpn
- android push
---


				开发移动App 的童鞋，都会有这样一个问题：应用发出去了，就不可控了。用户手机上一堆的应用，有可能用户根本就忘记了我这个应用的存在，不怎么去使用，我有没有什么办法？我发布了新版本相信可以更吸引用户，但他也不知道啊；我想做个活动来激活用户更多地使用我的应用，怎么做到呢？

提高活跃度的根本当然还在于产品本身，这一点是毋庸置疑的。正如有牛人总结说：[互联网企业产品是唯一的核心](http://www.huxiu.com/article/4123/1.html)。

想必很多人了解，产品之外，运营也是非常重要的部分，甚至是更重要的部分。最近著名的移动应用统计供应商 [Flurry](http://www.flurry.com/index.html) 发表了一篇文章：[移动应用经济的数学模型 － 盈利模式矩阵](http://www.huxiu.com/article/5073/1.html)。这篇文明开篇就谈到：在新的移动应用经济中，不管一个公司最初有多成功，**最后的胜者取决于用户参与度和维系用户的能力。获得用户之后，真正的战斗才刚打响，那就是留住用户，并通过他们最终获利。**在“移动优先”的全新世界里，参与度就是新战场。

而在移动App里集成 Push能力，则赋予了开发者对移动App的运营能力，让开发者有更多的机会来与用户交互，让用户更多地参与进来，留住用户，并最终提高获利。


### Push 怎么样提高应用的活跃度


国外为很多大 IT 公司包括 Oracle, Intel, Saleforce, USA Today 在内都使用的第三方推送服务 [Urban Airship](http://urbanairship.com/) ，针对大量应用做了一个统计，如下图：


[![Push for apps](http://blog.jiguang.cn/wp-content/uploads/2012/10/Push通知提高留存率.png)](http://blog.jpush.cn/wp-content/uploads/2012/10/Push通知提高留存率.png)


上图表明，平均一个应用一般 6 个月留存率只有 15%，但做了 Push 后留存率提高一倍。

即当一个应用，适时地运营向用户 Push 一些通知、消息时，能够有效地激活用户更多地使用 Apps，更少地卸载应用。


### 所有移动 App都有必要具备 Push 能力


上述谈到的是，Push 能够有效地提高用户活跃度与用户留存率。从这个角度，所有的移动  App都有必要加上 Push 功能。

其实，移动App 具备Push 能力，还有更多的必要性，以下再具体地列举几个：



	
  1. 用户需要App提供及时的信息，比如News类应用、聊天类、位置服务类等。

	
  2. 用户需要及时地同步信息到移动端，比如设备同步类、用户主动发起的同步需求等。

	
  3. 用户发起订阅某部分内容，应用内容有更新时主动推送给用户。

	
  4. 用户想要发起与别的用户一起玩游戏，别的用户需要及时得到邀请。




### 怎么样为移动应用加上 Push 能力


一般来说，每个手机平台都有自家提供 Push 功能，让应用开发者能够很方便地把 Push 能力集成到应用中。Android 上有 [GCM](http://developer.android.com/guide/google/gcm/index.html) (Google Cloud Messaging，原来叫 [C2DM](https://developers.google.com/android/c2dm/)），iOS 上有 [APNs](http://developer.apple.com/library/ios/#documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/CommunicatingWIthAPS/CommunicatingWIthAPS.html)（Apple Push Notification service），Windows Phone 上有 MPNs（Microsoft Push Notification service）。

但悲剧在于，[GCM](http://developer.android.com/guide/google/gcm/index.html) 在国内基本上是不可用的。有如下两个原因：

其一、国内大部分 Android 手机都是不带 Google 服务的，也就用不了 GCM 这个 Google服务。这是主要的问题。

其二、在国内，Google的服务，大多是不稳定的。原因你懂的！

[GCM](http://developer.android.com/guide/google/gcm/index.html) 的不可用，给国内 Android开发者带来很大的困扰，迫使 Android 开发者来思考：怎么样在 Android 上实现  Push？

我们有两个基本的选择：






	
  1. 自己开发 Push

	
  2. 使用第三方提供的 Push服务




#### **一、自己开发Push**




先说第一个方案，自己开发Push。自己实现又有如下几个可能的作法：








	
  1. 基于 androidpn （基于 XMPP IM）开源方案，或者其他 xmpp, mqtt 开源系统

	
  2. 自己开发伪 Push

	
  3. 完全自己开发长连接 Push 服务


类似于 androidpn 的开源方案存在一定的问题，请参考另外的文章：[androidpn作为Android推送方案存在的问题](http://blog.jpush.cn/index.php/androidpn_android_push_problem/)。这篇文章根据我们团队的聊天应用实践经验说明，想要直接基于开源系统来实现 Push服务会有其问题：系统容量是有限的，不适合移动对流量、电量消耗要求比较高的场景，要高可用性还是需要去基于开源的源代码去调整、改进，维护成本高。

第二个自己实现的方式，是伪 Push，即所谓轮询（polling ），实际上是定时去 http 拉取，检查服务器端是否有信息需要下发。我所了解到当前相当一部分自己实现了 Push方案的移动应用，都是采用这种方案来实现的。这种方式的好处在于：实现简单，容量大点http服务器端也相对容易搞定。

但是，伪 Push方案其问题也是很明显的，即其时效性是达不到要求的，推送消息无法及时到达客户端。如果轮询间隔很短，则电量、流量消耗都会比较大。另外，http每个请求包也相对较大。

第三个自己实现的方式，则就完全是自主开发了。这个路子来说，还是相对更难走一点的，开发团队需要有比较强的网络服务开发经验，最好有 IM开发经验。这里不详述技术上的细节与挑战。









#### ** 二、使用第三方提供的 Push服务**


Push 是个基础服务，不管是以开源方案为基础搭建，还是完全自主研发，投入都是比较大的，需要有网络服务方面的专业团队。投入很大的成本开发后，随着客户端用户量不断增大，其后期扩容、维护的工作量，也都是不断上升的，难度也越来越大。

所以，大多数应用开发者，不是去自己搭建 Push 这个基础服务，而是考虑去使用第三方提供的专业的服务。

国外有不少专业的第三方 Push 服务，比如 [Urban Airship](http://urbanairship.com/)，[Push IO](http://push.io/)，[Pubnub](http://www.pubnub.com/)，[Pusher](http://pusher.com/)。这些公司的服务一般是要收费的。当然在国内，使用国外的服务还是会有网络延时方面的考虑因素（你懂的）。

国内来说，之前没有给普遍开发者开放使用的第三方 Push服务提供商。最近，有公司来提供这个服务了，那就是：[极光推送](http://jpush.cn)。


### 极光推送：免费快捷的解决方案


极光推送，是专门针对移动 Push场景来设计的、开放的大容量 Push 服务。其技术团队由 Oracle、腾讯、华为前员工组成，并做过移动聊天App，对大容量并发网络连接服务具有丰富的经验。

极光推送的目标是在以下几个方面做到极致：



	
  1. 服务器端承载：单机接入并发 200万以上，服务器整体可在线平行扩展

	
  2. 消息稳定发送：借鉴聊天场景的设计，不允许丢失消息

	
  3. 客户端流量与电量消耗：每次心跳不到 100 个字节（如果XMPP或者HTTP，需要 1k 以上）


极光推送也力求开发者尽可能简单的集成SDK，为应用提供 Push能力。在 android 应用客户端，最少可只需要一行代码，即集成极光推送SDK：

`       JPushInterface.init(``this``);`

之外根据要求复制 SDK jar 包到应用里指定的目录，以及根据要求配置 AndroidManifest.xml 文件。具体了解请参考[极光推送推送Android SDK集成指南](http://docs.jpush.cn/pages/viewpage.action?pageId=557214)。

在服务器端，我们提供[开发者Portal](http://jpush.cn/login.jsp)，[注册开发者帐号](http://jpush.cn/signup.jsp)后，登录即可在开发者Portal里向应用推送消息，简单到只需要填写推送内容，然后点击发送按钮。另外，也提供[远程 API](http://docs.jpush.cn/pages/viewpage.action?pageId=557084) 方式供推送消息。完整地了解，请从这里开始：[极光推送快速入门](http://jpush.cn/home/started-android.jsp#article1)。

一般来说，同一个移动App，都会有多个平台的版本，一般至少有 Android 与 iOS 二个平台的。极光推送目前支持 Android 与 iOS 两个平台，服务器端推送时，可以同时兼容这二个平台的推送。这一点，可以大大地减轻运营的工作量。

如果您有移动App 集成 Push 能力的需求，或者对相关技术有兴趣，请访问[极光推送官方网站](http://www.jpush.cn)了解进一步的信息，或者关注[@极光推送](http://e.weibo.com/jpush)新浪微博。

		
