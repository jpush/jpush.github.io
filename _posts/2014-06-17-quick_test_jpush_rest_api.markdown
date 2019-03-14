---
author: javen
comments: true
date: 2014-06-17 12:56:38+00:00
excerpt: "\n\t\t\t\t\t\t"
layout: post
link: http://blog.jiguang.cn/quick_test_jpush_rest_api/
slug: quick_test_jpush_rest_api
title: "\n\t\t\t\t简单快捷地测试 JPush API\t\t"
wordpress_id: 531
categories:
- 技术文章
tags:
- JPush API
- REST API
- REST Client
---


				随着[ JPush API v3](http://docs.jpush.cn/display/dev/Push-API-v3) 版本的推出，加上之前开放的 [Report API](http://docs.jpush.cn/display/dev/Report-API)，JPush API 逐渐切换为比较好的符合 REST API 的规范，从而也很容易地使用一般的 HTTP/REST 工具来进行测试。

本文做个汇总，说明简单快捷地测试 JPush API 的方法。

这里不会详述编程的方法。简单说一句就是：JPush 官方提供了[多种语言的 SDK](http://docs.jpush.cn/display/dev/Server-SDKs) 来方便你调用 API，并且[开源放在 Github](https://github.com/jpush) 上。

简单的测试主要有几类工具可用：



	
  * curl 命令

	
  * 支持 POST 的浏览器插件

	
  * 直接浏览器 url 请求，仅适用于 Get 类 API，以下不做说明

	
  * 专用工具：不够简单，本文不详述




## curl 命令


在新的 JPush API 文档里，都有 curl 命令的 API 调用的示例，可以直接 COPY 到命令行里运行的。

比如 Push v3 API 文档里，有示例为：


<blockquote>`curl -X POST -v https:``//api.jpush.cn/v3/push -H "Content-Type: application/json" -u "7d431e42dfa6a6d693ac2d04:5e987ac6d2e04d95a9d8f0d1" -d '{"platform":"all","audience":"all","notification":{"alert":"Hi,JPush!"}}'`</blockquote>


上面的命令里需要解释下的是 -u 参数后的部分，是用来做鉴权的。引号里内容，前半部分在 JPush 里是 app_key，后半部分是 master_secret。


## 浏览器插件


这是本文的重点。下面介绍 2 个使用量比较大的插件。

由于 Push API 是复杂点的，所以以此为例。


### 使用的基本思路


1. 安装后在 Chrome 里找到 Web 应用；

Chrome URL 栏上的路径是： chrome://apps/

2. Basic Auth 部分的 username 对应 JPush app_key，password 对应 JPush master_secret ；

3. Push API 使用 POST 方法；

4. Content-Type 使用 "application/json"；

5. 内容部分使用 RAW （原始内容），贴上待推送的 JSON 字符串。


### Postman - REST Client （Chrome）


下载地址：[Postman](https://chrome.google.com/webstore/detail/postman-rest-client/fdmmgilgnpjigdojojpjoooidkmcomcm)

相关截图：

[![](http://blog.jiguang.cn/wp-content/uploads/2014/06/Snip20140617_21.png)](http://blog.jpush.cn/wp-content/uploads/2014/06/Snip20140617_21.png)


### Advanced REST Client （Chrome）


下载地址：[Advanced REST Client](https://chrome.google.com/webstore/detail/advanced-rest-client/hgmloofddffdnphfgcellkdfbfbjeloo)

相关截图：

[![](http://blog.jiguang.cn/wp-content/uploads/2014/06/Snip20140617_23.png)](http://blog.jpush.cn/wp-content/uploads/2014/06/Snip20140617_23.png)

[![](http://blog.jiguang.cn/wp-content/uploads/2014/06/Snip20140617_24.png)](http://blog.jpush.cn/wp-content/uploads/2014/06/Snip20140617_24.png)



[![](http://blog.jiguang.cn/wp-content/uploads/2014/06/Snip20140617_22.png)](http://blog.jpush.cn/wp-content/uploads/2014/06/Snip20140617_22.png)		
