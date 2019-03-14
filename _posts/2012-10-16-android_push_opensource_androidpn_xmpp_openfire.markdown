---
author: javen
comments: true
date: 2012-10-16 15:28:06+00:00
excerpt: "\n\t\t\t\t\t\t"
layout: post
link: http://blog.jiguang.cn/android_push_opensource_androidpn_xmpp_openfire/
slug: android_push_opensource_androidpn_xmpp_openfire
title: "\n\t\t\t\tAndroid Push 开源方案解析\t\t"
wordpress_id: 96
categories:
- 技术文章
tags:
- 推送
- xmpp
- push
- open source
- androidpn
- 极光
- openfire
- android push
---


				在 [Android](http://developer.android.com/index.html) 上，因为 Google 自己实现的 Android 标配的 [GCM](http://developer.android.com/guide/google/gcm/index.html) (Google Cloud Messaging，原来叫 [C2DM](https://developers.google.com/android/c2dm/)) 在国内基本不可用，所以，对于开发者来说，如果需要 Push功能，怎么样选择成为了一个问题。

到目前为止，国内尚没有完全向开发者免费、开放的 Push 服务可用。国外有几家第三方推送服务，但一般都要收费。所以一般来说，国内的开发者不得不考虑自己来搭建 Push服务。

自己构建 Push服务时，一个比较自然的选择就是，基于开源的现在方案来做。

使用 Google或者百度搜索 "Android Push 推送"等关键词，表明已经有不少人研究过。排在前边的是这样几篇文章：



	
  * [Android实现推送方式解决方案](http://www.cnblogs.com/hanyonglu/archive/2012/03/04/2378971.html)

	
  * [用androidpn来实现推送](http://www.iteye.com/topic/1117043)

	
  * [Android上实现Push](http://www.cnblogs.com/xirihanlin/archive/2011/12/06/2277807.html)

	
  * [Android Push Notification实现信息推送使用](http://www.189works.com/article-54321-1.html)


上面文章提及的方案里，基本上都提及了一个开源的 [Android](http://developer.android.com/index.html) Push实现: [androidpn](http://sourceforge.net/projects/androidpn/)。

[androidpn](http://sourceforge.net/projects/androidpn/) 它本质上服务器端基于 [Openfire](http://www.igniterealtime.org/projects/openfire/)，客户端基于 [asmack](http://code.google.com/p/asmack/)，这二者都最 [XMPP  IM](http://xmpp.org/) 开源实现里的二个基本组件，应该说 [androidpn](http://sourceforge.net/projects/androidpn/) 只是把二者更多地结合起来用于做 Push的场景。

笔者做过聊天App，愿意在这里，把基于 XMPP开源系统做 IM 的实践经验分享给大家。

我们做聊天类App，比较自然地，刚开始时也是从研究开源的 [XMPP](http://xmpp.org/) IM 系统入手。

先说服务器端选择。[Openfire](http://www.igniterealtime.org/projects/openfire/) 是一个 [XMPP](http://xmpp.org/)  最古老的开源 IM Server，几乎所有做 IM 的都应该有研究过。但是，它也是最不合适运用到生产的 IM Server，因为：单机并发很有限，集群方案不成熟，代码古老而缺乏及时更新。举个具体的例子：[Openfire](http://www.igniterealtime.org/projects/openfire/) 的集群组件叫[ Connection Manager](http://www.igniterealtime.org/projects/openfire/connection_manager.jsp)，但是，你在[ Openfire官方网站](http://www.igniterealtime.org/projects/openfire/index.jsp)可以看到，最近一个版本是 2009 年 2 月份发布的。可见，基于 [Openfire](http://www.igniterealtime.org/projects/openfire/) 实现的 [androidpn](http://sourceforge.net/projects/androidpn/) 的根基是不够稳的。

更新：与一个基于 Openfire 做聊天App的朋友交流，他们的用户量比较大，有多个 Openfire 节点做集群。他们对 Openfire 做了很多改造，比如 XMPP 协议交互复杂，要简化；XMPP 协议文本臃肿，则转换为二进制。集群方面，则完全是自己重新开发的。他们最多单点负载 30 万用户。

还有另外二个其实相对好一点的选择： [ejabberd](http://www.ejabberd.im/), [tigase](http://www.tigase.org/)。[ejabberd](http://www.ejabberd.im/) 是用 [Erlang](http://www.erlang.org/)语言实现的，懂 [Erlang](http://www.erlang.org/) 的用户很少，所以一般不会选。我们当时初步的聊天服务器端选择是 [tigase](http://www.tigase.org/) (Java实现的)。

[tigase](http://www.tigase.org/) 作者维护很活跃，集群测试结果能够支撑比较大的容量，这是吸引我们的地方。但经过实际生产运营情况来看，由于其集群方案实现的复杂性，以及单节点容量的有限，我们对支撑到 50 万用户在集群节点上没有信心，所以在到达 50 万用户之前，赶快自己开发了替代方案。

再来说 XMPP 协议与客户端的问题：对于移动客户端来说，原始的 XMPP 有些复杂而且流量消耗大。XMPP 本质上协议体都在字符串的 xml 结构上，每个协议都量一堆的字符串，xml里还有很多无意义的结构。另外，XMPP为了其灵活性，就登录这个事情都需要有 N 个来回。对于手机客户端很在乎流量与电量来说，XMPP 比较笨重。

我们的作法是：协议格式上改为二进制，协议内容上简化交互，但保留对原始  XMPP的兼容。

[androidpn](http://sourceforge.net/projects/androidpn/) 是开源的 Push 实现，是基于 XMPP 开源组件集成的，它没有为手机应用场景做必要的优化。另外，XMPP  本质上双向 IM 协议，而直接基于 XMPP 来实现 Push 功能，也是没有特别地为  Push 的特点优化的，比如客户端网络连接的策略等。

总结一下以 [androidpn](http://sourceforge.net/projects/androidpn/) 为典型的开源 Android Push 方案会存在的问题：

１）容量大了开源服务器实现顶不住，还是需要自己去改进开源实现，或者完全重新用新方案，开发投入与高成本是不可避免的。

２）协议与实现上如流量消耗、网络连接策略等，不是专门为移动 Push 优化过的，是不经济的。

基于我们团队基于 XMPP开源系统实现聊天App的实践经验，我们得出的结论是，在移动端的 IM场景里，开源方案不是个可用好用的方案。后来我们自己完全重新架构了整套系统。之后，正是基于这套全新架构的 IM 系统，演变出来了[极光推送](http://jpush.cn)。

[极光推送](http://jpush.cn)专门为移动场景下的实时 Push 来研发，我们想要去解决国内 Android 开发者没有可用、好用的 Push方案的问题，是免费的，完全向普通开发者开放。如果你也有这个 Android Push 的需求，不妨到[极光推送官方网站](http://jpush.cn)进一步地了解。

		
