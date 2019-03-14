---
author: javen
comments: true
date: 2013-12-02 05:50:58+00:00
excerpt: "\n\t\t\t\t\t\t"
layout: post
link: http://blog.jiguang.cn/jpush-report-received-api/
slug: jpush-report-received-api
title: "\n\t\t\t\tJPush 开放送达统计 API\t\t"
wordpress_id: 447
categories:
- 产品更新
tags:
- API
- 送达统计
- API标准
---


				近日 JPush 开放了一个新的 API：[Report Received API （送达统计API）](http://docs.jpush.cn/display/dev/Report+Received+API)。

这个 API 是实时的，返回某 msg_id 的送达统计数据。

大约 2 个月前，JPush Web Portal 上送达统计已经实时化了 - 即刚刚消息推送出去，就可以实时看到送达统计结果。这次对外开放 API 是在把内部统计数据开放访问，让开发者可以调用取到。

之所以还是花了些时间来开放，主要是 2 方面的原因：



	
  1. 访问量

	
  2. API 标准


这个送达 API 在频率限制方面，其限额是 Push APi 的 4 倍。 即一条消息推送后，你可以多次去查询送达情况，比如 10 秒查询 一次，1 分钟查询一次。如果这条消息是广播或者Tag针对大量用户推送，你可以考虑每秒发起一次查询，这样可以得到每秒新增了多少送达，从而开发者可以绘制出来一个送达曲线 - 实时更新的消息送达曲线。我们采取了些措施来应付大量的并发访问。

API 标准方面，JPush API 计划从这个 Report Received API 开始，做得更完善，更加符合业界的 API 标准。这次改进主要体现在以下几个方面：

	
  * 全部 https。不支持 http 访问。

	
  * 使用 HTTP Basic Authorization 的验证机制，而不再用之前的 verification_code 的不规范的作法。

	
    * 这样你可以通过 curl 命令很方便、快速地测试 API 调用效果了。




	
  * 数据格式尽可能采用 JSON。


我们制定规范时，部分地参考以下 2 个文档：

	
  * [Best Practices for Designing a Pragmatic RESTful API](http://www.vinaysahni.com/best-practices-for-a-pragmatic-restful-api)

	
  * [Github API v3](http://developer.github.com/v3/)


这是 JPush 所有统计数据实时化的一个开端。我们的目标是所有统计数据全部实时化，并且条件成熟时会开放给开发者访问。敬请期待！

了解详情，请访问 Report Received API 文档：[http://docs.jpush.cn/display/dev/Report+Received+API](http://docs.jpush.cn/display/dev/Report+Received+API)

		
