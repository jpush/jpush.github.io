---
author: hurong
comments: true
date: 2016-12-28 09:12:09+00:00
excerpt: "\n\t\t\t\t\t\t"
layout: post
link: http://blog.jiguang.cn/4-android-push-method/
slug: 4-android-push-method
title: "\n\t\t\t\t做推送，怎么能不了解推送的 4 种消息形式呢？「Android 篇」\t\t"
wordpress_id: 1759
categories:
- 产品更新
---


				




作者：极光社区 - 向红




极光推送是为 App 提供第三方推送服务的平台之一，它提供四种消息形式：通知，自定义消息，富媒体和本地通知。
笔者将基于官方说明与个人理解来谈一下这四种消息。本篇为 Android 篇，[ iOS 篇入口](http://www.jianshu.com/p/061c5f7e86d1)。[![Basic RGB](/images/2016/12/31.jpg)](/images/2016/12/31.jpg)





### 1.通知




<blockquote>

> 
> Push Notification，即指在手机的通知栏（状态栏）上会显示的一条通知信息。
**Android： 依靠 JPush service ，由 JPush SDK 实现在手机通知栏的展示。**
> 
> 
</blockquote>




#### Android 通知消息的流程是这样的：





* * *






	
  1. 我们可以调用极光的 API 发起推送请求，也可以通过极光官网控制台推送。

	
  2. 请求在参数要求上基本正确——请求成功，返回 msgid （这条消息的 ID 标识），返回 200（成功）或由极光判断的推送失败（一般是 [1011 找不到目标的错误](https://community.jiguang.cn/t/jpush/5145/10)）；参数错误，有返回错误码。

	
  3. 极光服务器处理该请求：

	
    * 若该 App [长连接正常](https://docs.jiguang.cn/jpush/client/Android/android_api/#_82)，极光服务器就会把这条消息在线下发给它。

	
    * 如果长连接是断开的，那么就会免费保存 5 条离线消息。

	
    * 如果该 App 在你推送消息时设定的离线保存时间（time_to_live） 时间内，重连上了长连接，该条消息就会下发给 App 。

	
    * 如果离线时间内，推送的消息超过5条，最先保存的消息会被删掉。

	
    * 如果过了time_to_live，该条消息也会被删掉，没有收到的 App 就不会再收到了。

	
    * 这里涉及到长连接断开的问题：

	
    * RTC 会定时发送心跳去触发连接。

	
    * 断网，关机，进程不在（被杀死），主动调用 stoppush 都会导致断开。

	
    * [一些手机系统的限制](https://docs.jiguang.cn/jpush/client/Android/android_faq/#_2)会导致断网和进程不在。

	
    * 解决办法：添加极光提供的拉起服务；文档提示用户对 App 进行保护。
[Android 收不到消息的情况，极光社区也有一个总结说明。](https://community.jiguang.cn/t/jpush/5145/3)




	
  4. App 收到了通知，由 JPush SDK 进行展示，和 iOS 一样，如果通知内容（ alert ）为空，也是默认不展示的， **但能获取到消息中的数据，以作他用**。

	
    * 我们可以简单的[修改这个通知栏的样式](https://docs.jiguang.cn/jpush/client/Android/android_api/#api_6)。

	
    * 在客户端定制了通知栏样式后，需要使用该通知栏时，在推送的时候就 **记得要写对应的通知栏编号**。（API 推送时，V2 接口的字段是n_builder_id，V3 接口的字段是 builder_id 。V2 功能较少，且在2015年已不再维护，建议大家尽快升级 V3 ，可以帮助我们更好的做推送。）

	
    * 这里容易有问题的是图标显示不正常，解决方案：

	
    * 若没有 res/drawable-xxxx/jpush_notification_icon 这个资源，那么通知栏默认使用应用图标作为通知 icon 。

	
    * 在 5.0 以上系统将应用图标作为 statusbar icon 可能显示不正常，我们可以定义 **没有阴影和渐变色**的 icon **同名替换** res/drawable-xxxx/jpush_notification_icon 这个文件。

	
    * 应该将 res 文件夹下的 **所有 icon 文件**都替换成自己的图标文件。

	
    * 还有一个可能是：小米手机的 launcher 缓存可能有bug，按照上述要求配置好后， **重启手机**，再测试。




	
  5. 点击该条通知消息：

	
    * 如果我们在 AndroidManifest.xml 里没有配置 Action - cn.jpush.android.intent.NOTIFICATION_OPENED ，JPush SDK 默认打开 App 首页，相当于点击桌面图标的效果。

	
    * 如果我们需要做点击跳转，那么就得在 AndroidManifest.xml 里配置此 receiver action ，SDK 在点击后不会做动作。我们需要在自己写的 BroadcastReceiver 类里处理，打开某 Activity 。

	
    * 这里可能遇到的问题是：某些手机系统下，App 从后台退出了，但是能收到通知消息，点击却没有反应，无法跳转。

	
    * 可能的原因： Android 应用退出后，可能长连接还存在，所以能收到消息，但是由于手机系统对 App 的限制，导致点击通知栏不能重启 App 成功。

	
    * 解决办法：打开手机系统的限制。

	
    * 具体会出现该情况的手机系统和后台退出后还能收到消息的前提条件（是否需要将 JPush 相关组件设置为一个独立进程）等还没有测试验证过。




	
  6. Android 关闭通知：
可以达到关闭通知效果的有两个办法，我们可以根据自己的需求去选用，如果在 UI 层面加一个关闭的按钮，那么对应的代码层面也是去选用这两种 API 。

	
    * [调用 stoppush](https://docs.jiguang.cn/jpush/client/Android/android_api/#api) ：

	
    * 一种本地的状态操作，相当于手动终止 push 服务的效果，这个会导致同时关掉对所有类型消息的接收。

	
    * 极光推送所有的其他 API 调用都无效。

	
    * 必须调用 resumepush 才能恢复推送服务。

	
    * 恢复后，如果推送的消息还在 time_to_live 内，客户端会收到这条消息。

	
    * 置空别名/标签（极光官网有总结一个 [别名的高级应用场景](https://docs.jiguang.cn/jpush/advanced/scenes/#_6)可以仔细阅读一下）

	
    * 一般需求是：用户退出登录后不要收到通知。
对应的操作是：项目的用户退出登录—置空别名—点击登录—重设别名。

	
    * 如果用户是直接杀死 A 手机的 App ，然后在 B 手机登录。
若你们不允许多设备登录，希望 A 手机不再收到消息，那么应该在自己的后台判断出 A 用户“ 换了设备登录” ，让 A 手机自动做登出操作，在客户端置空别名；
或者在[服务端调用极光的 API ](https://docs.jiguang.cn/jpush/server/push/rest_api_v3_device/)去置空 A 手机设置的别名。
（判断的方法可以是：将用户某唯一信息与获取到的 registrationID 一起上传，registrationID 与之前相比改变了，则判断为更换了设备。）

	
    * **需要注意**：别名置空 至 重设别名 期间，推送的消息， **在重设别名后，设备不会收到。**
若需要这期间的消息也被收到，需要自己服务器对此进行判断、保存、并在客户端重设别名后，重新向极光发起推送请求。
若需要用户的历史推送消息可被查询，需要自己服务器那边去保存。







### 2.自定义消息




<blockquote>

> 
> iOS 需要当 App 处于前台才能收到，Android 不需要处于前台。
> 
> 
</blockquote>




##### 几个特点





* * *






	
  1. **只接收，不展示，需要我们自己做接收处理，并且自己代码实现 消息的展示。**

	
  2. 需要依靠 App 与极光服务器建立的长连接。

	
  3. Android 需要我们在 AndroidManifest.xml 里配置 Action - cn.jpush.android.intent.MESSAGE_RECEIVED ，并且在自己写的 BroadcastReceiver 里接收处理，[详细看官方文档说明](https://docs.jiguang.cn/jpush/client/Android/android_api/#action-cnjpushandroidintentmessage_received)。




##### 什么时候使用它？





* * *




主要用于应用的内部业务逻辑和特殊展示需求，譬如：






	
  1. 需要自定义通知栏时（Android 要播放自定义的声音文件等情况）。

	
  2. 在 App 项目内的消息提醒。

	
  3. 有条件的消息提醒（譬如只让处在某一页面的用户看到该条消息）。




##### 可能会犯的错误





* * *




弄混自定义内容（字段 extras）与自定义消息（Message）






	
  1. Android 点击通知栏无法获取到自定义的内容

	
    * 推送时 extras 是写在 Notification 下还是 Message 下？

	
    * 点击 SDK 的通知栏 用 JPushInterface.EXTRA_EXTRA 获取的是写在 Notification 下的 extras 信息，没法获取写在 Message 下的 extras。

	
    * Message 消息和 Notification 消息 分别有各自的参数，不要混用。







### 3.富媒体




<blockquote>

> 
> 可以推送 Web页面、图片、声音等除普通文本之外更丰富的内容。
> 
> 
</blockquote>





	
  1. 两种：信息流模板推送、URL 富媒体链接推送

	
    * 信息流模板推送

	
    * 推送 Web 页面（富文本）；

	
    * 极光官网控制台上有创建富文本页面的功能模块，提供了5种模板；

	
    * 富文本作为通知推送到客户端；

	
    * 客户端点击通知，自动展示该富文本页面。

	
    * URL 富媒体链接推送

	
    * 推送某个页面的 URL ，点击通知栏消息后跳转到这个 URL 指定的页面。




	
  2. **仅支持 Android ，需要JPush Android SDK 1.8.0 及以上。**

	
  3. 只能通过极光推送的控制台发送。

	
  4. 必须将压缩包 res 中的资源放到我们自己的项目的对应文件夹，并按照官方示例 AndroidManifest 配置 PushActivity 组件。




### 4.本地通知




<blockquote>

> 
> 适用于在特定时间发出的通知，如一些Todo和闹钟类的应用，在每周、每月固定时间提醒用户回到应用查看任务
> 
> 
</blockquote>




##### 几个特点





* * *






	
  1. 不依赖于网络，无网也可以触发。

	
  2. 定时时间是自发送时算起，不受中间关机等操作的影响。

	
  3. 本地通知与远程推送的通知是相互独立的，不受 [保留最近通知条数上限](https://docs.jiguang.cn/jpush/client/Android/android_api/#api_7)（Android）的限制。

	
  4. [ Android ](https://docs.jiguang.cn/jpush/client/Android/android_api/#api_8)依旧需要 App 应用程序在运行。




##### 规则





* * *






	
  1. 本地推送保存在本地的 db 中，时间上的控制也是从 db 里面取。

	
  2. 关机或者杀进程后将收不到本地通知。

	
  3. 进程重启后，会检测 db ，准备设定时间在此之后的本地通知的推送。

	
  4. 本地通知的时间也依赖于每4分50秒的心跳来检测，所以并不能特别精准地刚好到了通知时间你刚好启动了进程，就能收到推送，会有一定偏差。




​




		
