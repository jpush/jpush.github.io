---
author: javen
comments: true
date: 2012-11-01 07:25:35+00:00
excerpt: "\n\t\t\t\t\t\t"
layout: post
link: http://blog.jiguang.cn/androidpn_android_push_problem/
slug: androidpn_android_push_problem
title: "\n\t\t\t\tandroidpn 作为Android推送方案存在的问题\t\t"
wordpress_id: 173
categories:
- 技术文章
tags:
- 推送
- androidpn
- android push
---


				如果百度或者Google搜索 “android 推送” 关键字，相当一部分文章都在说到 androidpn。也可以看到有人说用起来了，有人在吐槽说不稳定、功能缺失，维护工作量大。本文尝试对 androidpn 的前世今生做个汇总分析。

访问 [androidpn官方网站](http://sourceforge.net/projects/androidpn/)，我们可以了解到如下的基本信息：



	
  1. androidpn 全称是 Android Push Notification。

	
  2. 这是韩国人开源放在 [sourceforge.net](http://sourceforge.net/) 上的一个开源项目，文档是韩文的。

	
  3. 最近的版本更新时间是 2010-11-05，也就是约二年之前。

	
  4. 来自中国的访问量，占其总访问量的 81%。请[点击本链接再看当前的统计](http://sourceforge.net/projects/androidpn/files/stats/timeline)。


以上的基本信息表明，这不是一个很成熟的项目（貌似个人维护的），但是确有大量的中国人有兴趣。

相信有不少同学知道为什么国人对 androidpn 这么感兴趣，这是国情啊：因为Google官方的 GCM（之前叫C2DM）在国内使用不了。另外，国内之前又没有可用好用的第三方推送服务。所以，大家不得已而为之：自己搞。从头开始？工作量太大，太不划算了。所以从开源的开始。而也恰好，开源项目里，明确地为 Android Push来生的，也就 androidpn了。mqtt 里没有整合这么好可以快速跑起来的。

但 androidpn 搭建起来后，情况如何呢？[CSDN上一个美女程序员](http://blog.csdn.net/nairuohe)的文章 [androidpn推送初探](http://blog.csdn.net/nairuohe/article/details/6924301) 比较热。这篇文章里作者提出来几个问题：



	
  * androidpn服务器收到消息后如何知道要发给哪个用户？

	
  * 一旦服务器重启了，客户端似乎不会自动重连，需要用户自己中断后台Service再重启应用。

	
  * androidpn服务器不保存消息。就是说它一有消息就会发出去，即使客户端根本不在线，它也不会重发。


作者赞在于，不只是满足于把环境搭建起来，而且针对业务需求做了思考。解析下她提出的问题：

第一个问题相对简单，要去定制下用户体系，业务部分的用户体系需要与 androidpn 对应起来。

第二个问题，是个小细节，androidpn 客户端没有去做这些细节。

第三个问题，是最重要的。androidpn 背后的 Openfire，是 XMPP IM服务器，消息内容是不会落地的，即只在内存里保存一下离线消息。如果要生产用，需要考虑改造这里。

以下从 androidpn 的技术基础来个深入的剖析。

androidpn 是一个整合方案，它是基于 XMPP 开源组件的 。即服务器端基于 Openfire，客户端基于 Smack ，这二个是 XMPP 开源组件里最常见的两个。androidpn使用Spring框架做了个Web层，把XMPP IM组件集成起来，以实现Android Push功能。因此，androidpn的可用性来自于如下几个方面：

	
  1. 其依赖的XMPP IM协议与通讯机制，是否适合用于Android Push场景。

	
  2. 其是否为Android Push需求做了必要的定制。


第一个方面，XMPP协议与开源组件。XMPP是个成熟的IM（即时通讯）协议，基于XML文本方式实现，灵活强大。国外大多数聊天服务都是基于XMPP的，比如：Gtalk，Facebook Chat，iMessage等。正因为如此，所以XMPP的一些开源组件可用性还不错，国内很多新兴的聊天工具刚开始完全基于XMPP开源组件来开发，比如米聊。

但是，以笔者曾经实现过移动聊天App的类似经历来说，XMPP开源组件有其问题，需要大范围改造：

	
  1. XMPP协议复杂冗余，客户端费流量、费电；

	
  2. 开源的XMPP服务器（androidpn选择的是Openfire）单点容量有限，集群方案复杂、不成熟。


基于这些原因，并考虑到大范围改造技术上更不可控，所以笔者所在的团队刚开始用XMPP开源方案，后来完全切到自己实现的技术方案上了。

第二个方面，Android Push的需求场景，除了消息能够及时地推送到客户端，还有什么其他的基本需求呢？这里试举两个例子：其一，确保消息能够到达。即是否能有机制确保消息不会丢失，不管什么原因。其二，向指定的一群人推送消息。anroidpn的基础XMPP IM组件是没有考虑这些的，需要做大量的定制改造。

总结起来，使用androidpn可以简单地做到：把消息推送到客户端。但是，要使其适合开发者需要，并在生产环境上运行，则可能需要做很多定制开发工作。从笔者与多个开发者交流得到的反馈来看，在生产环境里运行起来问题很多。

上面说到，国内之前没有什么可用好用的第三方消息推送服务，所以大家不得不自己去考虑搭建。最近国内有个第三方消息推送服务开放了，它是[极光推送](http://www.jpush.cn)，普通开发者可以随时注册帐号、[下载SDK](http://www.jpush.cn/home/download-android.jsp)做集成，整个过程 3 分钟就可以完成。关键在于，这个服务是免费的。		
