---
author: javen
comments: true
date: 2016-12-13 09:50:27+00:00
excerpt: "\n\t\t\t\t\t\t"
layout: post
link: http://blog.jiguang.cn/three-strategies-about-open-rate/
slug: three-strategies-about-open-rate
title: "\n\t\t\t\t提高 iOS App 通知功能启用率的三个策略\t\t"
wordpress_id: 1699
categories:
- 技术文章
---


				[![3](/images/2016/12/3.jpg)](/images/2016/12/3.jpg)

我们都知道推送通知在 App 运营中的作用巨大。但是，很多用户却并不买帐，App 第一次启动提示是否「启用推送通知」时，他们直接选择了「否」。

是的，最近我本人就转变成了这样的人 - 认真地评估每个应用是否要启用推送通知功能。App 第一次启动就让我做选择，我没有预期我要收这个 App 的通知做什么，我为什么要选择开启通知功能，而受各种非预期的通知的打扰呢？

据统计，平均只有 40% 的人启用应用的推送通知功能。具体来说也要看 App 属性了，比如微信这种通讯类 App，用户会预期要及时地收到消息通知，自然就会启用。但大多数 App 没有这么幸运，用户没有一个明确的预期或理由，App 会推送什么通知。关于不同类别的 App 通知启用率的分析与对策，可参考这篇文章：[为什么 Uber 的推送打开率超过 60%？](https://community.jiguang.cn/t/uber-60/4035/1).

App 开发者有没有办法，让用户更多点地开启通知功能呢？还是有点思路的。本文总结三个有效的策略。


### 策略一：提前声明通知的作用，让用户有预期


绝大多数 App 在申请推送通知功能开启时，都是简单粗暴的：App 首次打开时代码注册推送通知功能，让系统弹出「是否启用推送通知」的提示框。

这个粗暴作法的结果也很简单，用户直接点击「否」。

有没有更友好的作法呢？有。就是在使用 App 的某个阶段，先业务上提示，向用户说明，「开启通知功能」对这个 App 的体验有多么重要。然后，再调用系统远程通知注册，系统会弹出请求通知权限的提示框，让用户做选择。

[![11](/images/2016/12/11.jpg)](/images/2016/12/11.jpg)[![12](/images/2016/12/12.jpg)](/images/2016/12/12.jpg)以上截图是「星空」这款 App 刚打开时给我的展示界面。在弹出系统的提示是否允许通知前，它有个介绍的页面，告诉你启用「定位」对这个 App 有什么好处，启用「通知」对这个 App 有什么好处。看到关于通知的说明，我点击「允许」按钮后，就会弹出下图里系统的界面，让你允许启用通知功能。
[![13](/images/2016/12/13.jpg)](/images/2016/12/13.jpg)

这个截图是来自于应用 「AppSo」，一款做应用推荐的 App。看到有「限免」通知，我就毫不犹豫地点击了「允许」推送通知。

总之，在用户面对系统的提示框前，先给用户说明「通知」对这个应用的意义。如果在这一步用户就表示不同意，就不必弹出系统提示了，以后有机会需要开启时再次提示。


### 策略二：通知分类，给用户更多的控制权


用户不愿意启用一个 App 的「通知」功能，是出于这样的考虑：App 会推送什么通知我不知道，我不想要非我预期的通知信息来打扰我。

我们如果能够想办法让用户更清晰地了解，我们这个 App 会给他推送什么通知，则可以一定程度上打消用户的顾虑，从而让他启用这个应用的「通知」功能。

让用户清晰地了解推送的内容的方式，就是对可能的「通知」进行分类，并且让用户勾选他希望收到通知的分类。

[![14](/images/2016/12/14.jpg)](/images/2016/12/14.jpg)

比如「亚马逊购物」这个应用，推送通知的类型有这些可选，我选择开通了：帐户状态通知、配送状态通知、个性化推荐，而秒杀不是我感兴趣的。如果他不提供这个设置项，则我会因为有秒杀这种广告性质的通知而完全关闭这个 App 的通知功能。


### 策略三：找时机提示用户重新考虑打开「通知」功能，并提供方便


如果用户就是没有启用我这个 App 的「通知」，是不是就完全没有办法了呢？还是有机会的：当用户访问到通知相关功能时，再次提示用户去打开「通知」功能。

很多用户的策略就是，新安装一个应用默认不启用「推送通知」。对于这种用户，只能采取本条的思路了：必要时再次提示用户。

有些用户不熟悉如何去设置里打开「通知」，其实即使如我这样熟悉的人，在那么长的 App 列表里去找到要操作的 App 来打开通知，也是有负担的。所以，如果 App 里有快捷入口，点击一下就切换到系统设置里对应的界面，就会很方便了。

下图是「天猫」的界面，点击「新消息提示总开关」这里，就切换到了系统设置里「天猫」应用相关的设置项页面。

[![15](/images/2016/12/15.jpg)](/images/2016/12/15.jpg)[![16](/images/2016/12/16.jpg)](/images/2016/12/16.jpg)

需要注意的是，提示用户其实也有个时机把握问题，我对 QQ 的提示就不太爽：经常冷不丁弹出提示，说我没有启用通知建议我启用。而有些应用的提示就还可接受，即在 App 使用场景真的与通知相关时才提示我，比如电商类 App 我在查询发货进度时。

总结来说，iOS App 如果想要基于 Push 更充分地运营，就必须要做好上述三个策略，以便从用户那儿争取到他「启用通知」。遗憾的是，目前大部分的 App 还没有做好这几个方面的功课，甚至有些大 App 也做得不是非常让人满意。但要承认的是，的确也在逐渐地改进了。希望本文对你的 App 改进有所帮助！

		
