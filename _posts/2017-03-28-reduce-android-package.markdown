---
author: hurong
comments: true
date: 2017-03-28 07:29:30+00:00
excerpt: "\n\t\t\t\t\t\t"
layout: post
link: http://blog.jiguang.cn/reduce-android-package/
slug: reduce-android-package
title: "\n\t\t\t\t怎样减少 Android 应用包 60% 的大小？\t\t"
wordpress_id: 1799
categories:
- 技术文章
---


				




[![11](http://blog.jiguang.cn/wp-content/uploads/2017/03/11.png)](http://blog.jiguang.cn/wp-content/uploads/2017/03/11.png)




编译：极光社区 - hevin





<blockquote>

> 
> 简评：应用的大小也是用户体验的一个重要方面，而减少 Android 应用安装包大小其实一点也不复杂。
> 
> 
</blockquote>




对于移动应用来说，应用安装包的大小当然是越小越好。特别是对于一些欠发达地区，你不希望用户因为手机「存储空间不足」而卸载你的应用。




本文就介绍了几个小技巧来有效减少 apk 的大小：




首先，我们先用 Android Studio 提供的 APK Analyser 工具来分析下我们的 apk 文件：
[![12](http://blog.jiguang.cn/wp-content/uploads/2017/03/12.png)](http://blog.jiguang.cn/wp-content/uploads/2017/03/12.png)
可以看到占空间最多的主要是三个部分：classes.dex, res 和 resources.arsc。






	
  * classes.dex：包含有 Java 代码的字节码文件。

	
  * res：包含了资源文件，比如图片、布局文件等等。

	
  * resources.arsc：包含所有的值资源文件，如 strings, dimensions, styles, integers 等等。




## classes.dex




classes.dex 文件的大小完全取决于程序中方法数的多少。在上面展示的例子中，包含了 4392 个类和 29897 个方法。这个结果是默认没有使用 proguard 的。我们有两个默认的 proguard 文件可以使用：






	
  * [proguard-android-optimize.txt](https://android.googlesource.com/platform/sdk/+/master/files/proguard-android-optimize.txt)

	
  * [proguard-android.txt](https://android.googlesource.com/platform/sdk/+/master/files/proguard-android.txt)




从名字上就可以看出，proguard-android-optimize.txt 是更加激进的 proguard 配置，包括和 proguard-android.txt 相同的 ProGuard 规则，但还包括其他在字节码一级（方法内和方法间）执行分析的优化，以进一步减小 APK 大小和帮助提高其运行速度。




可以像这样来使用：




    
    <code style="font-size: 0.85em; font-family: Consolas, Inconsolata, Courier, monospace; margin: 0px 0.15em; padding: 0.5em 0.7em; white-space: pre; border: 1px solid #cccccc; background-color: #f8f8f8; border-radius: 3px; display: block !important; overflow: auto;">release {
     //Enable the proguard
     minifyEnabled true
     proguardFiles getDefaultProguardFile('proguard-android-optimize.txt'), "proguard-rules.pro"
    
    //Other parameters
     debuggable false
     jniDebuggable false
     renderscriptDebuggable false
     signingConfig playStoreConfig //Add your own signing config
     pseudoLocalesEnabled false
     zipAlignEnabled true
    }
    </code>




通过设置 minifyEnabled 属性为 true，会告诉 proguard 删除所有未使用的方法和指令，进一步的减少 .dex 文件的大小。
[![13](http://blog.jiguang.cn/wp-content/uploads/2017/03/13.png)](http://blog.jiguang.cn/wp-content/uploads/2017/03/13.png)
方法数从 29897 减少到了 15168，大小从 3.1MB 减少到了 1.98 MB。





## res




对于 res 文件夹，通常占空间最大的就是图片了。如果你的 Android Studio 为 2.3，并且项目的 minimum version 为 18 或以上，应该使用 webp 而不是 png 图片。webp 图片有更小的体积，图片质量还没有什么损失。




我们可以选中 drawable 和 mipmap 文件夹，右键后选择 convert to webp，将图片转为 webp 格式。
[![14](http://blog.jiguang.cn/wp-content/uploads/2017/03/14.png)](http://blog.jiguang.cn/wp-content/uploads/2017/03/14.png)
转换的结果：
[![15](http://blog.jiguang.cn/wp-content/uploads/2017/03/15.png)](http://blog.jiguang.cn/wp-content/uploads/2017/03/15.png)




我们还可以在 build.gradle 中设置 shrinkResources 属性为 true，这会在打包时删除未使用的资源：




    
    <code style="font-size: 0.85em; font-family: Consolas, Inconsolata, Courier, monospace; margin: 0px 0.15em; padding: 0.5em 0.7em; white-space: pre; border: 1px solid #cccccc; background-color: #f8f8f8; border-radius: 3px; display: block !important; overflow: auto;">release{
     //...
     //...
     shrinkResources true
     //...
    }
    </code>




另外如果你的应用不需要支持国际化，那么可以设置 resConfigs 为 “zh”,”en”，即只支持中英文：




    
    <code style="font-size: 0.85em; font-family: Consolas, Inconsolata, Courier, monospace; margin: 0px 0.15em; padding: 0.5em 0.7em; white-space: pre; border: 1px solid #cccccc; background-color: #f8f8f8; border-radius: 3px; display: block !important; overflow: auto;">defaultConfig {
     //...
     //...
     //...
    
    resConfigs "zh","en"
    }
    </code>




这么做是因为对于官方的 support library，默认是支持国际化的，也就是包含了很多不同语言的资源文件，我们就可以通过这样设置来移除用不到的语言资源文件。




通过上面的设置，这个 App 的安装包大小从 3.19MB 减少到了 1.89MB。当然还有很多其他的减少 apk 大小的技巧，但这里介绍的技巧是完全可以作为你项目的默认设置。





<blockquote>

> 
> 原文：[How you can decrease application size by 60% (In only 5 minutes)?](https://medium.com/@kevalpatel2106/how-you-can-decrease-application-size-by-60-in-only-5-minutes-47eff3e7874e#.mc0lkvs46)
> 
> 
</blockquote>




​




		
