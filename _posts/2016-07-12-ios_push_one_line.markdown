---
author: javen
comments: true
date: 2016-07-12 05:49:45+00:00
excerpt: "\n\t\t\t\t\t\t"
layout: post
link: http://blog.jiguang.cn/ios_push_one_line/
slug: ios_push_one_line
title: "\n\t\t\t\t一行命令搞定 iOS 推送\t\t"
wordpress_id: 1072
categories:
- 技术文章
tags:
- iOS Push
- 命令行
- 工具
---


				

<blockquote>编者按：本文为极光员工 [Pikacode](http://www.jianshu.com/users/f8650cb2b4eb) 为进一步方便开发者集成 JPush SDK 而开发的小工具，极致地简单。</blockquote>


本文写给那些想极速集成推送功能的开发者，不用阅读任何文档，不用写一行代码，1分钟集成，立即使用！



	
  * 不论你的项目是用 Objective-C 开发的 Native App；

	
  * 还是基于 Cordova （PhoneGap, ionic, We5x 等框架）的 Web App；

	
  * 或是 Cocos2d-x 的 iOS 项目，统统只需打开**终端**输入一行命令：

    
    <code class="sql">appkey=yourAppkey platform=yourPlatform npm <span class="hljs-operator"><span class="hljs-keyword">install</span> one-line-jpush-ios</span></code>


下载推送 SDK、添加代码、添加类库全部自动进行，完成后立刻推送！

参数说明：

	
    * appkey：上传证书在[【极光推送控制台】](https://www.jpush.cn/common/apps)获取 appkey [【获取 appkey 方法】](http://docs.jpush.io/guideline/ios_guide/#sdk)

	
    * platform：部署平台，可选值：native、cordova、cocos2dx（默认 native ） 。例：

    
    <code class="sql">//native
    appkey=yourAppkey npm <span class="hljs-operator"><span class="hljs-keyword">install</span> one-line-jpush-ios
    appkey=yourAppkey platform=<span class="hljs-keyword">native</span> npm <span class="hljs-keyword">install</span> one-line-jpush-ios
    //cordova
    appkey=yourAppkey platform=cordova npm <span class="hljs-keyword">install</span> one-line-jpush-ios
    //cocos2d-x
    appkey=yourAppkey platform=cocos2dx npm <span class="hljs-keyword">install</span> one-line-jpush-ios</span></code>








在相应目录下执行完对应的一条命令后，Apple 证书配置正确的情况下[(iOS 证书设置指南)](http://docs.jpush.io/client/ios_tutorials/#ios_1)，**立即推送！**

**针对不同项目，该命令会做不同的事情，需要在对应目录下执行：**


### Native App(Object-C) 项目




#### 执行命令


从终端进入到项目**根目录的与项目同名文件夹**下(如：/HelloWorld/HelloWorld/)
[![1](/images/2016/07/1.jpg)](/images/2016/07/1.jpg)



	
  * 在该目录下，终端执行命令即可：

    
    <code class="sql">appkey=yourAppkey npm <span class="hljs-operator"><span class="hljs-keyword">install</span> one-line-jpush-ios</span></code>


或

    
    <code class="sql">appkey=yourAppkey platform=native npm <span class="hljs-operator"><span class="hljs-keyword">install</span> one-line-jpush-ios</span></code>


其中 `yourAppkey` 替换成在 [极光控制台](https://www.jpush.cn/common/apps) 创建 app 时得到的 appkey




#### 完成！！


安装成功命令行提示以下内容 [![12](/images/2016/07/12.jpg)](/images/2016/07/12.jpg)


#### 命令说明


platform=native 时帮大家做以下事情：



	
  * 自动从极光官网下载并安装 [极光推送最新版本 SDK](http://docs.jpush.io/updates/#jpush-ios-sdk-v217) 到根目录所在项目中

	
  * 自动写入**启动 SDK**及**接收推送**的必要代码 (在 AppDelegate.m 中)

	
  * 自动添加必须的类库到 iOS 项目中




### Cordova 项目




#### 执行命令





	
  * 从终端进入到 Cordova 项目的**根**目录下：


[![2](/images/2016/07/2.png)](/images/2016/07/2.png)


	
  * 在该目录下，终端执行命令即可：

    
    <code class="sql">appkey=yourAppkey platform=cordova npm <span class="hljs-operator"><span class="hljs-keyword">install</span> one-line-jpush-ios</span></code>


其中 `yourAppkey` 替换成准备工作中在[极光控制台](https://www.jpush.cn/common/apps)创建 app 时得到的 appkey




#### 完成！！


安装成功命令行提示以下内容 [![21](/images/2016/07/21.jpg)](/images/2016/07/21.jpg)


#### 命令说明


platform=cordova 时帮大家做以下事情：



	
  * 自动创建 iOS 项目

	
  * 自动从 GitHub 上下载最新的 极光推送 [JPush Phonegap Plugin](https://github.com/jpush/jpush-phonegap-plugin) 插件

	
  * 自动安装该插件到当前项目并添加必要配置

	
  * 自动添加必须的类库到 iOS 项目中




### Cocos2d-x 项目




#### 执行命令





	
  * 从终端进入到 Cocos2d-x 项目的 `proj.ios_mac` 目录下，跟 `ios`、`mac` 文件夹同一级：




[![3](/images/2016/07/3.png)](/images/2016/07/3.png)





	
  * 在该目录下，终端执行命令即可：

    
    <code class="sql">appkey=yourAppkey platform=cocos2dx npm <span class="hljs-operator"><span class="hljs-keyword">install</span> one-line-jpush-ios</span></code>


其中 `yourAppkey` 替换成在[极光控制台](https://www.jpush.cn/common/apps)创建 app 时得到的 appkey




#### 完成！！


安装成功命令行提示以下内容
[![32](/images/2016/07/32.jpg)](/images/2016/07/32.jpg)


#### 命令说明


platform=cocos2dx 时帮大家做以下事情：



	
  * 自动从 GitHub 上下载最新的 Cocos2d-x 插件 [JPush Cocos2d-x Plugin](https://github.com/jpush/jpush-cocos2d-x-plugin)

	
  * 自动安装该插件到当前项目并添加必要配置

	
  * 自动写入**启动 SDK**及**接收推送**的必要代码 (在 AppController.mm 中)

	
  * 自动添加必须的类库到 iOS 项目中




### 终端使用方法 & 控制台推送方法


[终端使用方法 & 控制台推送方法 点我](https://github.com/Yasashi/one-line-jpush)

		
