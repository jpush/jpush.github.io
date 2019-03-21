---
author: Sunsg
comments: true
date: 2016-10-26 02:01:09+00:00
excerpt: "\n\t\t\t\t\t\t"
layout: post
link: http://blog.jiguang.cn/ios10_sixchanges/
slug: ios10_sixchanges
title: "\n\t\t\t\t适配 iOS 10，极光推送用户要做这 6 处更改\t\t"
wordpress_id: 1307
categories:
- 技术文章
---


				




<blockquote>

> 
> #### 本文作者：极光 - [pikacode ](http://www.jianshu.com/users/f8650cb2b4eb)
> 
> 
</blockquote>




#### Change 1：升级至 Xcode 8




建议尽快升级。使用 iOS 10 SDK 需要 Xcode 8 的支持。iOS 10 推出两周内，安装率就已经达到 48.16%，不升级 Xcode 8 并适配 iOS 10 意味着你现在可能已经损失了 50% 的高端客户，而且在未来的几个月内或许会陆续损失 90% 以上的客户。





#### Change 2：Xcode 8 推送基本配置





	
  1. 首先跟以前版本的 Xcode 没什么区别。下载自己在 Apple Developer 官网申请好的证书、描述文件（[iOS 证书 设置指南 ](https://docs.jiguang.cn/jpush/client/iOS/ios_cer_guide/)）。填写 Bundle Identifier、选择开发者，正确配置后，这里不会有任何异常警告：[![1](/images/2016/10/1.jpeg)](/images/2016/10/1.jpeg)

	
  2. Target - your target - Capabilities - 开启 Push Notifications
证书如果配置正确，这里会自动打勾。系统会在工程目录里生成一个 `projectName.entitlements` 文件，请不要随意删除、修改：
[![2](/images/2016/10/2.jpeg)](/images/2016/10/2.jpeg)

	
  3. Target - your target - Capabilities - 开启 Background Modes - 勾选最后一项 Remote Notifications（这是 iOS 7 以后支持的 App 在后台收到推送时能够让开发者执行一段代码的功能，建议开启 [[iOS 7 Background Remote Notification]](https://docs.jiguang.cn/jpush/client/iOS/ios_new_fetures/#ios-7-background-remote-notification)、[[iOS 推送全解析 - Tip5：后台推送/静默推送]](http://www.jianshu.com/p/e9c313df746f)）




#### Change 3：更新 JPush iOS SDK >= v2.1.9





	
  1. [资源下载](https://docs.jiguang.cn/jpush/resources/)

	
  2. 替换工程中原有的 JPush SDK 文件为 `JPUSHService.h`、`jpush-ios-2.1.9.a`。

	
  3. Target - your target - Build Phases - Link Binary With Libraries - 引入一个新的库 `UserNotifications.framework`
[![3](/images/2016/10/3.jpeg)](/images/2016/10/3.jpeg)




#### Change 4：更改注册推送代码




原先向系统注册并请求推送权限的代码是酱紫的，根据 iOS 8 以后及以前分两步：




    
    <code style="font-size: 0.85em; font-family: Consolas, Inconsolata, Courier, monospace; margin: 0px 0.15em; padding: 0.5em; white-space: pre; border: 1px solid #cccccc; border-radius: 3px; display: block; background-color: #f8f8f8; overflow: auto; overflow-x: auto; color: #333333; background: #f8f8f8;" class="hljs language-objc"><span style="color: #333333; font-weight: bold;" class="hljs-keyword">if</span> ([[UIDevice currentDevice]<span style="color: #008080;" class="hljs-variable">.systemVersion</span> floatValue] >= <span style="color: #008080;" class="hljs-number">8.0</span>) {
            <span style="color: #999988; font-style: italic;" class="hljs-comment">//可以添加自定义categories</span>
            [JPUSHService registerForRemoteNotificationTypes:(UIUserNotificationTypeBadge |
                                                              UIUserNotificationTypeSound |
                                                              UIUserNotificationTypeAlert)
                                                  categories:<span class="hljs-literal">nil</span>];
        }
    <span style="color: #333333; font-weight: bold;" class="hljs-keyword">else</span> {
            <span style="color: #999988; font-style: italic;" class="hljs-comment">//categories 必须为nil</span>
            [JPUSHService registerForRemoteNotificationTypes:(UIRemoteNotificationTypeBadge |
                                                              UIRemoteNotificationTypeSound |
                                                              UIRemoteNotificationTypeAlert)
                                                  categories:<span class="hljs-literal">nil</span>];
    }
    </code>




现在在前面加了一段 iOS 10 的注册方法：




    
    <code style="font-size: 0.85em; font-family: Consolas, Inconsolata, Courier, monospace; margin: 0px 0.15em; padding: 0.5em; white-space: pre; border: 1px solid #cccccc; border-radius: 3px; display: block; background-color: #f8f8f8; overflow: auto; overflow-x: auto; color: #333333; background: #f8f8f8;" class="hljs language-objc"><span style="color: #999988; font-style: italic;" class="hljs-comment">//Required</span>
    <span style="color: #333333; font-weight: bold;" class="hljs-keyword">if</span> ([[UIDevice currentDevice]<span style="color: #008080;" class="hljs-variable">.systemVersion</span> floatValue] >= <span style="color: #008080;" class="hljs-number">10.0</span>) {
            JPUSHRegisterEntity * entity = [[JPUSHRegisterEntity alloc] init];
            entity<span style="color: #008080;" class="hljs-variable">.types</span> = UNAuthorizationOptionAlert|UNAuthorizationOptionBadge|UNAuthorizationOptionSound;
            [JPUSHService registerForRemoteNotificationConfig:entity delegate:<span style="color: #333333; font-weight: bold;" class="hljs-keyword">self</span>];
        }
    <span style="color: #333333; font-weight: bold;" class="hljs-keyword">else</span> <span style="color: #333333; font-weight: bold;" class="hljs-keyword">if</span> ([[UIDevice currentDevice]<span style="color: #008080;" class="hljs-variable">.systemVersion</span> floatValue] >= <span style="color: #008080;" class="hljs-number">8.0</span>) {
            <span style="color: #999988; font-style: italic;" class="hljs-comment">//可以添加自定义categories</span>
            [JPUSHService registerForRemoteNotificationTypes:(UIUserNotificationTypeBadge |
                                                              UIUserNotificationTypeSound |
                                                              UIUserNotificationTypeAlert)
                                                  categories:<span class="hljs-literal">nil</span>];
    } <span style="color: #333333; font-weight: bold;" class="hljs-keyword">else</span> {
            <span style="color: #999988; font-style: italic;" class="hljs-comment">//categories 必须为nil</span>
            [JPUSHService registerForRemoteNotificationTypes:(UIRemoteNotificationTypeBadge |
                                                              UIRemoteNotificationTypeSound |
                                                              UIRemoteNotificationTypeAlert)
                                                  categories:<span class="hljs-literal">nil</span>];
    }
    </code>




#### Change 5：实现代理 方法




在 Change 4 中的该行代码处会报警告：




    
    <code style="font-size: 0.85em; font-family: Consolas, Inconsolata, Courier, monospace; margin: 0px 0.15em; padding: 0.5em; white-space: pre; border: 1px solid #cccccc; border-radius: 3px; display: block; background-color: #f8f8f8; overflow: auto; overflow-x: auto; color: #333333; background: #f8f8f8;" class="hljs language-objc">[JPUSHService registerForRemoteNotificationConfig:entity delegate:<span style="color: #333333; font-weight: bold;" class="hljs-keyword">self</span>];
    </code>




其中的 self 类必须实现 ，其是对 iOS 10 接收推送并处理的代理 的封装。




实现 的两个方法：




    
    <code style="font-size: 0.85em; font-family: Consolas, Inconsolata, Courier, monospace; margin: 0px 0.15em; padding: 0.5em; white-space: pre; border: 1px solid #cccccc; border-radius: 3px; display: block; background-color: #f8f8f8; overflow: auto; overflow-x: auto; color: #333333; background: #f8f8f8;" class="hljs language-objc">- (<span style="color: #333333; font-weight: bold;" class="hljs-keyword">void</span>)jpushNotificationCenter:(UNUserNotificationCenter *)center willPresentNotification:(UNNotification *)notification withCompletionHandler:(<span style="color: #333333; font-weight: bold;" class="hljs-keyword">void</span> (^)(<span style="color: #0086b3;" class="hljs-built_in">NSInteger</span>))completionHandler {
     <span style="color: #999988; font-style: italic;" class="hljs-comment">// Required</span>
     <span style="color: #0086b3;" class="hljs-built_in">NSDictionary</span> * userInfo = notification<span style="color: #008080;" class="hljs-variable">.request</span><span style="color: #008080;" class="hljs-variable">.content</span><span style="color: #008080;" class="hljs-variable">.userInfo</span>;
     <span style="color: #333333; font-weight: bold;" class="hljs-keyword">if</span>([notification<span style="color: #008080;" class="hljs-variable">.request</span><span style="color: #008080;" class="hljs-variable">.trigger</span> isKindOfClass:[UNPushNotificationTrigger class]]) {
     [JPUSHService handleRemoteNotification:userInfo];
     }
     completionHandler(UNNotificationPresentationOptionAlert); <span style="color: #999988; font-style: italic;" class="hljs-comment">// 需要执行这个方法，选择是否提醒用户，有Badge、Sound、Alert三种类型可以选择设置</span>
    }
    
    - (<span style="color: #333333; font-weight: bold;" class="hljs-keyword">void</span>)jpushNotificationCenter:(UNUserNotificationCenter *)center didReceiveNotificationResponse:(UNNotificationResponse *)response withCompletionHandler:(<span style="color: #333333; font-weight: bold;" class="hljs-keyword">void</span> (^)())completionHandler {
     <span style="color: #999988; font-style: italic;" class="hljs-comment">// Required</span>
     <span style="color: #0086b3;" class="hljs-built_in">NSDictionary</span> * userInfo = response<span style="color: #008080;" class="hljs-variable">.notification</span><span style="color: #008080;" class="hljs-variable">.request</span><span style="color: #008080;" class="hljs-variable">.content</span><span style="color: #008080;" class="hljs-variable">.userInfo</span>;
     <span style="color: #333333; font-weight: bold;" class="hljs-keyword">if</span>([response<span style="color: #008080;" class="hljs-variable">.notification</span><span style="color: #008080;" class="hljs-variable">.request</span><span style="color: #008080;" class="hljs-variable">.trigger</span> isKindOfClass:[UNPushNotificationTrigger class]]) {
     [JPUSHService handleRemoteNotification:userInfo];
     }
     completionHandler(); <span style="color: #999988; font-style: italic;" class="hljs-comment">// 系统要求执行这个方法</span>
    }
    </code>




其中：






	
  * `willPresentNotification` 在展示推送之前触发，可以在此替换推送内容，更改展示效果：内容、声音、角标。

	
  * `didReceiveNotificationResponse` 在收到推送后触发，你原先写在 `didReceiveRemoteNotification` 方法里接收推送并处理相关逻辑的代码，现在需要在这个方法里也写一份：

	
  * App 处于后台收到推送触发

	
  * 点击推送条目或横幅后，App 进入前台或 App 启动触发

	
  * App 处于前台时触发




## Change 6：Notification Service Extension & Notification Content


这两个 iOS 10 的新特性，暂未包含在 JPush SDK 中，需要用户手动创建相应的 Target 并实现。


#### Notification Service Extension


主要负责修改推送内容、增加图片、gif、audio、video 展示。
收到推送小图 - 下拉 - 展示大图
[![4](/images/2016/10/4.gif)](/images/2016/10/4.gif)


#### Notification Content


用于完全自定义推送展示 UI，响应用户操作事件，并即时更新推送展示 UI。
注意下图中点击 Accept 后，推送 UI 里日程表 UI 发生了刷新。
[![5](/images/2016/10/5.gif)](/images/2016/10/5.gif)

**关于 Change 6 的详细教程可参照**

[玩转 iOS 10 推送 —— UserNotifications Framework（下）](http://www.jianshu.com/p/25ca24215f75)



	
  * ​




		
