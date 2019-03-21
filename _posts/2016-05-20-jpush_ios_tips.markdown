---
author: javen
comments: true
date: 2016-05-20 06:10:17+00:00
excerpt: "\n\t\t\t\t\t\t"
layout: post
link: http://blog.jiguang.cn/jpush_ios_tips/
slug: jpush_ios_tips
title: "\n\t\t\t\t使用 JPush iOS SDK 注意事项\t\t"
wordpress_id: 945
categories:
- 技术文章
---


				

<blockquote>

> 
> 编者按：iOS 推送集成有太多坑。这些坑大多是由苹果的证书导致的。本文是公司资深技术支持MM 的深度总结，是集成 JPush iOS 不可错过的参考。
> 
> 
</blockquote>




**如果你刚开始集成、使用 JPush iOS SDK，你可能会遇到各种各样的问题，下面是我们整理的一些注意事项，给大家作为参考。**




**1、**




从iOS9系统开始，使用通配证书也能成功获取到Token，但是无法收到推送的通知（APNs）；




**2、**




运行安装项目到测试设备，假设你的Provisioning Profile选择自动，如果测试设备是iOS9系统，且本地的Provisioning Profile不含此设备，仍能正常运行安装到测试设备并获取到Token！其实你这时安装的是通配证书的应用，你将无法收到APNs推送；请参考JPush的教程文档配置项目证书：[XCode的证书配置教程](http://docs.jpush.io/client/ios_tutorials/#xcode)




**3、**




从iOS9系统开始，每次卸载安装都会获取到新的Token。如果你没有使用传IDFA的JPush初始化接口，那JPush会认为你当前是一个新的设备，RegistrationId会随之改变，之前设置的tag、alias需要重新设置；




**4、**




从iOS9系统开始，新安装某个调用apns注册接口的应用，弹出［“xxx”想给您发送推送通知］的提示时，无论你是否允许，应用都能获取到Token；




**5、**




JPush没有提供开关推送的接口，但是可以调用apple的注册APNs接口和反注册APNs接口来设置是否有通知提醒！




注：从iOS9.3系统开始，调用代码注册APNs，再调用反注册APNs接口，再调用注册APNs接口，需要把应用进程杀掉，重新开启才能恢复APNs提示；




**6、**




JPush 目前的SDK并不支持https，集成的时候需要到xxx-info.plist里面配置下http，否则无法统计通知点击和调用JPUSHService setBadge:<#(NSInteger)#>来上报badge，具体配置看文档：[https解决方式](http://docs.jpush.io/guideline/faq/#ios)




**7、**




如果你是直接运行安装到测试设备（运行调试状态），那你的安装的应用的证书环境只有可能是开发环境或者通配的。向该设备（RegistrationID）推送开发环境的通知，如果能收到（目标和成功为：1/1），则说明是当前安装的应用是开发环境的证书；如果目标和成功为（0/0），则可能是通配证书。应用证书环境具体以Code Signing配置为准：




![bc0fd846851c923480f190dfda089265b14d461f_1_690x251](/images/2016/05/bc0fd846851c923480f190dfda089265b14d461f_1_690x251.png)




**8、**




如果你想内部测试生产环境的推送，请去apple上面为该appid创建一个AD-Hoc的Provisioning Profile，下载，添加到XCode，Archive项目，export ipa文件，把该ipa拖到iTunes，再同步到测试设备。




导出的时候下图标注的Profile必须是你所创建的AD-Hoc Profile：




[![15b31dd72354a85148e8d2f9cd594757db857fc7_1_690x411](/images/2016/05/15b31dd72354a85148e8d2f9cd594757db857fc7_1_690x411.png)](/images/2016/05/15b31dd72354a85148e8d2f9cd594757db857fc7_1_690x411.png)




******9、**




第7、8点说到安装应用证书环境的判断依据，那这里再说明下：推送的时候如何指定iOS通知的推送环境。




进入官方控制台的发送通知页面，可通过如下选项指定 iOS通知的推送环境：




[![6697d318cdf01da86d82fbcedc14e226cf0c2ad4_1_690x362](/images/2016/05/6697d318cdf01da86d82fbcedc14e226cf0c2ad4_1_690x362.png)](/images/2016/05/6697d318cdf01da86d82fbcedc14e226cf0c2ad4_1_690x362.png)




****如果你通过api推送,则需要注意options下面的apns_production字段。官网发送通知的推送对象选择里 iOS开发环境、iOS生产环境对应 API 的 "apns_production":flase 和 "apns_production":true。[推送API文档](http://docs.jpush.io/server/rest_api_v3_push/#options)里下图有进一步的说明。




[![94993bd4760513780feb218a9de82c94d4c04021_1_690x452](/images/2016/05/94993bd4760513780feb218a9de82c94d4c04021_1_690x452.png)](/images/2016/05/94993bd4760513780feb218a9de82c94d4c04021_1_690x452.png)




**10、**




请不要在初始化应用的方法




    
    <span class="s1">- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
    }</span>




调用 tag/alias 设置与获取 registrationID。如果你这样做将无法获取到registrationID，设置tag、alias会打印：




    
    <span class="s1">E - [JPUSHService] app not registed, give up set tag:xxx,alias:xxx</span>




因为这个时候 SDK 还未启动完成。建议如下（附图建议：在 Login 回调里）：




[![9d243b6e3cc48188a3437dd56b0654be74372c24_1_689x286](/images/2016/05/9d243b6e3cc48188a3437dd56b0654be74372c24_1_689x286.png)](/images/2016/05/9d243b6e3cc48188a3437dd56b0654be74372c24_1_689x286.png)




****注：想了解集成文档的［五个监听］[请戳我](http://docs.jpush.io/guideline/ios_guide/#_8)




如不知道该如何添加监听，请参考官方 demo的：RootViewController.m；




**11、**




应用前台运行的时候收到 APNs 不会有提示（横幅、sound），只会调用 Apple 提供的APNs获取接口：[获取 APNs（通知） 推送内容](http://docs.jpush.io/client/ios_api/#apns)；




**12、**




后台收到普通APNs，点击icon进入应用并不会触发APNs获取接口，只有点击通知才会触发获取接口；




**13、**




从JPush下的发iOS自定义消息，只有在前台运行的时候才能收到（不会有apns提示），如果想要获取由JPush 下发的自定义消息,请看:[获取自定义消息推送内容](http://docs.jpush.io/client/ios_api/#_25)





<blockquote>

> 
> 注：如果想了解通知和自定义消息的区别请看：[常见问题](http://docs.jpush.io/guideline/faq/)的［通知与自定义消息有什么区别？］
> 
> 
</blockquote>




**14、**




建议在 AppDelegate.m 的 applicationWillResignActive 方法写代码调整本地的badge 显示和重置JPush服务器上面 Badge值，示例代码如下：




    
    - (void)applicationWillResignActive:(UIApplication *)application {
            [JPUSHService setBadge:0];//重置JPush服务器上面的badge值。如果下次服务端推送badge传"+1",则会在你当时JPush服务器上该设备的badge值的基础上＋1；        
            [[UIApplication sharedApplication] setApplicationIconBadgeNumber:0];//apple自己的接口，变更应用本地（icon）的badge值；
    }




注：






	
  * JPush的badge上报接口可根据实际使用场景计算再调用接口上报；

	
  * 应用icon上显示的badge最好在 applicationWillResignActive 方法里面设为最终计算所得的值。




**15、**




如果你之前能正常推送iOS通知，忽然所有推送记录的结果都变成了：0/0。这个时候你需要去到JPush控制台，进入该应用的 [应用设置]页面，检查证书的有效期，如果没有过期，则去到apple账号检查该证书是否还在（以appid+有效期来看）




注：无论是上述何种原因，你都需要在 Apple 账号上下载该 bundle id的有效APNs证书，添加到本地，导出p12文件，上传到JPush控制台。

		
