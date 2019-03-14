---
author: hurong
comments: true
date: 2017-03-02 02:24:21+00:00
excerpt: "\n\t\t\t\t\t\t"
layout: post
link: http://blog.jiguang.cn/react-native-push-ios/
slug: react-native-push-ios
title: "\n\t\t\t\tReact-Native 工程添加推送功能 (iOS 篇)\t\t"
wordpress_id: 1785
categories:
- 产品更新
---


				作者：极光社区 - HuminOS





推送已经是是手机应用的基本功能，如果自己实现一套推送系统费时费力，所有一般我们会使用第三方的推送服务，这里我使用 极光推送 作为集成推送的例子，因为有现成的 react native 插件 [jpush-react-native](https://github.com/jpush/jpush-react-native) 可以使用。





### 前置准备工作




##### 安装软件





	
  * react native (0.41.2)

	
  * npm (V3.10.3)

	
  * xcode (V8.2.1)




##### 创建工程





	
  * 在 Apple developer 上创建应用

	
  * 并且给应用配置推送功能，创建推送证书

	
  * 到 [极光官网](https://www.jiguang.cn) 上创建自己的应用，并上传证书

	
  * 创建 React-Native 工程，修改 bundle identifier ，保证和在 Apple developer 上创建的应用一直。(Project -> Target -> General -> bundle identifier)

	
  * xcode 8 版本的话还需要在 (Project -> Target -> Capabilities ) 吧Push Notification 选项点开
第一、二、三 步骤的详细介绍可以观看 [官方集成视频](https://community.jiguang.cn/t/jpush-ios-sdk/4247)， 这里就不在重复。
(如果已有工程，则只需要完成第二和第三项步骤)




### 集成 jpush-react-native




##### 配置工程




在完成上面的准备工作后，我们就可以正式开始插件的集成，这里会解释每个步骤背后都做了哪些操作。
按照官方的集成说明安装 jpush-react-native (这里使用最新版本 V1.5.0)






	
  1. 


安装 jpush-react-native 插件




    
    <code style="font-size: 0.85em; font-family: Consolas, Inconsolata, Courier, monospace; margin: 0px 0.15em; padding: 0.5em 0.7em; white-space: pre; border: 1px solid #cccccc; background-color: #f8f8f8; border-radius: 3px; display: block !important; overflow: auto;">npm install jpush-react-native --save
    </code>




安装完后可以看到 package.json 的 dependencies 字段多了 “jpush-react-native” 依赖




	
  2. 


然后安装 jcore-react-native 插件




    
    <code style="font-size: 0.85em; font-family: Consolas, Inconsolata, Courier, monospace; margin: 0px 0.15em; padding: 0.5em 0.7em; white-space: pre; border: 1px solid #cccccc; background-color: #f8f8f8; border-radius: 3px; display: block !important; overflow: auto;">npm install jcore-react-native --save
    </code>




安装完后可以看到 package.json 的 dependencies 字段多了 “jcore-react-native” 依赖。 由于极光开发者服务 SDK 采用了模块化的使用模式，即一个核心（JCore）+N种服务（JPush，JAnalytics，…）的使用方式，所以把公共核心代码分离出来，这样在使用极光推送多种服务的时候不会出现冲突。




	
  3. 


链接工程




    
    <code style="font-size: 0.85em; font-family: Consolas, Inconsolata, Courier, monospace; margin: 0px 0.15em; padding: 0.5em 0.7em; white-space: pre; border: 1px solid #cccccc; background-color: #f8f8f8; border-radius: 3px; display: block !important; overflow: auto;">react-native link
    </code>




这个步骤，其实是吧 jpush plugin 工程添加到 自己的项目工程里面，可以看到 Libraries 目录多了一个 RCTJPushModule.xcodeproj 工程文件，如下图所示[![001](http://blog.jiguang.cn/wp-content/uploads/2017/03/001.png)](http://blog.jiguang.cn/wp-content/uploads/2017/03/001.png)







同时这个步骤还会自动把 JPush 所依赖的库自动添加到工程中，可以看到
TARGETS -> Build phases -> Link Binary With Libraries 多了几个系统库，如下图所示




[![002](http://blog.jiguang.cn/wp-content/uploads/2017/03/002.png)](http://blog.jiguang.cn/wp-content/uploads/2017/03/002.png)




这些步骤都是自动完成的，不需要额外的操作。





##### 添加代码




###### 1. 自动添加代码




jpush-react-native 插件提供了自动配置脚本




    
    <code style="font-size: 0.85em; font-family: Consolas, Inconsolata, Courier, monospace; margin: 0px 0.15em; padding: 0.5em 0.7em; white-space: pre; border: 1px solid #cccccc; background-color: #f8f8f8; border-radius: 3px; display: block !important; overflow: auto;">npm run configureJPush <yourAppKey> <yourModuleName>
    </code>




把 换成自己的 AppKey (在极光官网上创建应用可以获得一个 Appkey)




指的是你 Android 项目中的模块名字(对 iOS 没有影响，不填写的话默认值为 app，会影响到查找 AndroidManifest 问题。




这个脚本会自动吧极光推送的代码插入到 Appdelegate.m 中。
到这一步 jpush-react-native 插件已经集成完毕。
我们试着这编译自己工程，如果出现找不到头文件的情况则需要在 iOS 工程中如果找不到头文件可能要在 TARGETS-> BUILD SETTINGS -> Search Paths -> Header Search Paths 添加如下如路径




    
    <code style="font-size: 0.85em; font-family: Consolas, Inconsolata, Courier, monospace; margin: 0px 0.15em; padding: 0.5em 0.7em; white-space: pre; border: 1px solid #cccccc; background-color: #f8f8f8; border-radius: 3px; display: block !important; overflow: auto;">$(SRCROOT)/../node_modules/jpush-react-native/ios/RCTJPushModule/RCTJPushModule
    </code>




###### 2. 手动添加代码




手动添加代码部分 [jpusn-react-native ReadMe](https://github.com/jpush/jpush-react-native/blob/master/example/documents/iOS_Usage.md) 已经说得很清楚了,这里就不再重复





​





		
