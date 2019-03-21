---
author: feipeng
comments: true
date: 2013-11-12 08:11:27+00:00
excerpt: "\n\t\t\t\t\t\t"
layout: post
link: http://blog.jiguang.cn/redis-twemproxy-benchmark/
slug: redis-twemproxy-benchmark
title: "\n\t\t\t\tRedis 存储分片之代理服务Twemproxy 测试\t\t"
wordpress_id: 394
categories:
- 技术文章
tags:
- Cluster
- Redis
- Twemproxy
---


				

# 概述


实际业务场景中单点 Redis 容量、并发都是有限的，所以有 Redis Cluster 的需求。

但是官方的 Redis Cluster 一再跳票，还不可用。

只好先使用最简单的方式：Proxy。有很多可选，但在大范围生产使用的， Twitter 开源的 Twemproxy  看起来是个理想的选择 - [https://github.com/twitter/twemproxy](https://github.com/twitter/twemproxy) 。

我们期望的目标：



	
  * tag/alias 缓存集群（现在单点容量支持越来越不够）

	
  * 数据统计时高并发缓存


下面的文章内容也是基于实际生产需要而进行的一系列测试.


# 测试环境说明


本次测试的机器都是虚拟机，对应的母机配置为 Dell R710   Intel(R) Xeon(R) CPU       E5606  @ 2.13GHz  2CPU(单CPU 4核)  32G内存，上面安装了4台虚拟机，配置分别如下：





<table style="width: 649px; height: 109px;" > 
<tbody >
<tr >

<td style="text-align: left;" >序号
</td>

<td style="text-align: left;" >机器IP
</td>

<td style="text-align: left;" >配置
</td>

<td style="text-align: left;" >部署服务
</td>

<td colspan="1" style="text-align: left;" >redis服务数量
</td>
</tr>
<tr >

<td style="text-align: left;" >1
</td>

<td style="text-align: left;" >192.168.2.65
</td>

<td style="text-align: left;" >4cpu,8GRam
</td>

<td style="text-align: left;" >redis,twemproxy
</td>

<td colspan="1" style="text-align: left;" >4个端口分别为(10000,10002,10003,10004)
</td>
</tr>
<tr >

<td style="text-align: left;" >2
</td>

<td style="text-align: left;" >192.168.2.66
</td>

<td style="text-align: left;" >4cpu,8GRam
</td>

<td style="text-align: left;" >redis
</td>

<td colspan="1" style="text-align: left;" >2个端口分别为(10000,10002)
</td>
</tr>
<tr >

<td style="text-align: left;" >3
</td>

<td style="text-align: left;" >192.168.2.67
</td>

<td style="text-align: left;" >4cpu,8GRam
</td>

<td style="text-align: left;" >redis
</td>

<td colspan="1" style="text-align: left;" >2个端口分别为(10000,10002)
</td>
</tr>
<tr >

<td style="text-align: left;" >4
</td>

<td style="text-align: left;" >192.168.2.68
</td>

<td style="text-align: left;" >4cpu,4GRam
</td>

<td style="text-align: left;" >redis,twemproxy
</td>

<td colspan="1" style="text-align: left;" >2个端口分别为(10000,10002)
</td>
</tr>
</tbody>
</table>





虚拟机系统： **CentOS release 6.3 (Final) ****    **

Redis版本：2.6.16

Twemproxy版本：nutcracker-0.2.4

部署示意图

![](/images/2013/11/twemproxy部署示意图.jpg)

下面的测试都是根据上面的配置不同组合来进行测试得出对应的结论。

测试工具：Redis Benchmark.


# 测试结论




## 功能





	
  1. 前端使用 Twemproxy 做代理，后端的 Redis 数据能基本上根据 key 来进行比较均衡的分布。

	
  2. 后端一台 Redis 挂掉后，Twemproxy 能够自动摘除。恢复后，Twemproxy 能够自动识别、恢复并重新加入到 Redis 组中重新使用。

	
  3. Redis 挂掉后，后端数据是否丢失依据 Redis 本身的策略配置，与 Twemproxy 基本无关。

	
  4. 如果要新增加一台 Redis，Twemproxy 需要重启才能生效；并且数据不会自动重新 Reblance，需要人工单独写脚本来实现。

	
  5. 如同时部署多个 Twemproxy，配置文件一致（测试配置为**distribution**：ketama,modula），则可以从任意一个读取，都可以正确读取 key对应的值。

	
  6. 多台 Twemproxy 配置一样，客户端分别连接多台 Twemproxy可以在一定条件下提高性能。根据 Server 数量，提高比例在 110-150%之间。

	
  7. 如原来已经有 2 个节点 Redis，后续有增加 2 个 Redis，则数据分布计算与原来的 Redis 分布无关，现有数据如果需要分布均匀的话，需要人工单独处理。

	
  8. 如果 Twemproxy 的后端节点数量发生变化，Twemproxy 相同算法的前提下，**原来的数据必须重新处理分布，否则会存在找不到key值的情况**。




## 性能


不管 Twemproxy 后端有几台 Redis，前端的单个 Twemproxy 的性能最大也只能和单台 Redis 性能差不多。


# Twemproxy介绍


Twemproxy 也叫 nutcraker。是 Twtter 开源的一个 Redis 和 Memcache 代理服务器，主要用于管理 Redis 和 Memcached 集群，减少与Cache 服务器直接连接的数量。

Twemproxy特性：



	
  * 轻量级、快速

	
  * 保持长连接

	
  * 减少了直接与缓存服务器连接的连接数量

	
  * 使用 pipelining 处理请求和响应

	
  * 支持代理到多台服务器上

	
  * 同时支持多个服务器池

	
  * 自动分片数据到多个服务器上

	
  * 实现完整的 memcached 的 ASCII 和再分配协议

	
  * 通过 yaml 文件配置服务器池

	
  * 支持多个哈希模式，包括一致性哈希和分布

	
  * 能够配置删除故障节点

	
  * 可以通过端口监控状态

	
  * 支持 linux, *bsd,os x 和 solaris


Twemproxy安装配置参考官网：[https://github.com/twitter/twemproxy](https://github.com/twitter/twemproxy) 或 附后的 Reference。

启动命令：


<blockquote>$/usr/local/twemproxy/sbin/nutcracker -d -c /usr/local/twemproxy/etc/test.yml -i 2000 -o logs/nutcracker.log</blockquote>


或者修改源码的 scripts 中的 ini 代码以 service 方式启动。

运行中如果需要查看运行状态使用下面方式，结果为json格式，需要自己格式化。

1.web界面运行启动服务的http://ip:22222查看，如本次测试查看url：[http://192.168.2.68:22222/](http://192.168.2.68:22222/)

2.使用nc命令查看 Twemproxy 状态语句：


<blockquote>$nc 192.168.2.68 22222|python -mjson.tool</blockquote>




## Twemproxy支持命令测试


源码的scripts中包含一些脚本可以进行基本功能测试，如下：




**scripts目录脚本**






<table align="left" border="0" >
<tbody >
<tr >

<td style="text-align: left;" >[root@test66 scripts]$ ls -tlh
total 76K
-rwxr-xr-x 1 root root 496 Oct 23 10:16 pipelined_read.sh
-rwxr-xr-x 1 root root 639 Oct 23 10:16 pipelined_write.sh
-rwxr-xr-x 1 root root 495 Oct 23 10:16 populate_memcached.sh
-rw-r--r-- 1 root root 665 Oct 23 10:16 redis-check.py
-rwxr-xr-x 1 root root 48K Oct 23 10:16 redis-check.sh  #检测redis基本命令是否可以使用
-rwxr-xr-x 1 root root 526 Oct 23 10:16 multi_get.sh
-rw-r--r-- 1 root root 1.2K Oct 23 10:16 nutcracker.init
-rw-r--r-- 1 root root 1.3K Oct 23 10:16 nutcracker.spec
</td>
</tr>
</tbody>
</table>

































从执行结果看，下面命令都可以使用(结合我们常用的命令)






<table align="left" border="0" >
<tbody >
<tr >

<td style="text-align: left;" >del psetex linsert smove zscore dump set llen spop zunionstore exists setbit lpop srandmember eval expire psetex lpush srem persist setnx lpushx sunion expireat setrange lrange sunionstore expire hdel lrem zadd pttlhexists ltrim zcard ttl hget rpop zcount type hgetall append hincrby rpush zinterstore bitcount hincrbyfloat

rpushx zrange get hkeys sadd zrangebyscore getbit hlen scard zrank getrange zrem 

getset hmset sdiffstore zremrangebyrank incr hset sinter zremrangebyscore incrby 

hsetnx sinterstore zrevrange incrbyfloat hvals sismember zrevrangebyscore mget 

lindex smembers zrevrank rpoplpush zincrby hmget sdiff
</td>
</tr>
</tbody>
</table>




















测试不支持的几个命令： restore decr  decrby

如果生产环境中使用的话，建议先检查是否有无法使用的命令后在决定使用。


## Twemproxy 和单机 Redis 对比测试


测试方法：使用 Redis 自带压力测试工具 redis-benchmark 测试2-3次，取其中比较好的结果。

注：测试本机上面的 Redis 都是在其他客户端上面执行，避免由于不通过网络导致测试不准确.另外每次运行结果都有不同差距。

执行命令类似如下：


<blockquote>$/usr/local/bin/redis-benchmark -h 192.168.2.68 -p 10000 -c 100 -q</blockquote>


Twmemproxy 配置的后端 Redis 信息如下：
<table border="0" >
<tbody >
<tr >

<td style="text-align: left;" >...
distribution： ketama
...
servers:
- 192.168.2.67:10000:1
- 192.168.2.66:10000:1
- 192.168.2.65:10000:1
- 192.168.2.68:10000:1
- 192.168.2.68:10002:1
- 192.168.2.68:10003:1
</td>
</tr>
</tbody>
</table>
从自带的压力测试工具看，基本上 Twemproxy 的性能和单台 Redis 服务基本差不多。后端自带 Redis 只是为数据更好的split(由于从实际统计信息看看，压力测试实际上只访问了后端的其中某一台redis服务，故这个结果也属于正常)



<table >
<tbody >
<tr >
命令\IP
2.68:10000
2.68:10002
2.68:10003
2.68:10004
2.68:10005
2.67:10000
2.66:10000
2.65:10000
twemproxy(9999)
</tr>
<tr >

<td style="text-align: left;" >SET
</td>

<td style="text-align: left;" >21231.42
</td>

<td style="text-align: left;" >21598.27
</td>

<td style="text-align: left;" >21645.02
</td>

<td style="text-align: left;" >22026.43
</td>

<td style="text-align: left;" >21413.28
</td>

<td style="text-align: left;" >23364.49
</td>

<td style="text-align: left;" >23584.91
</td>

<td style="text-align: left;" >23310.02
</td>

<td colspan="1" style="text-align: left;" >25125.63
</td>
</tr>
<tr >

<td colspan="1" style="text-align: left;" >GET
</td>

<td colspan="1" style="text-align: left;" >26246.72
</td>

<td colspan="1" style="text-align: left;" >21739.13
</td>

<td colspan="1" style="text-align: left;" >20661.16
</td>

<td colspan="1" style="text-align: left;" >20876.83
</td>

<td colspan="1" style="text-align: left;" >21367.52
</td>

<td colspan="1" style="text-align: left;" > 22831.05
</td>

<td colspan="1" style="text-align: left;" >23041.47
</td>

<td colspan="1" style="text-align: left;" >23809.53
</td>

<td colspan="1" style="text-align: left;" >25510.21
</td>
</tr>
<tr >

<td colspan="1" style="text-align: left;" >LPOP
</td>

<td colspan="1" style="text-align: left;" >20120.72
</td>

<td colspan="1" style="text-align: left;" >21276.60
</td>

<td colspan="1" style="text-align: left;" >21367.52
</td>

<td colspan="1" style="text-align: left;" >17152.66
</td>

<td colspan="1" style="text-align: left;" >21097.05
</td>

<td colspan="1" style="text-align: left;" >23474.18
</td>

<td colspan="1" style="text-align: left;" >23201.86
</td>

<td colspan="1" style="text-align: left;" >23696.68
</td>

<td colspan="1" style="text-align: left;" >22075.05
</td>
</tr>
<tr >

<td colspan="1" style="text-align: left;" >SADD
</td>

<td colspan="1" style="text-align: left;" >20449.90
</td>

<td colspan="1" style="text-align: left;" >21052.63
</td>

<td colspan="1" style="text-align: left;" >20833.33
</td>

<td colspan="1" style="text-align: left;" > 20876.83
</td>

<td colspan="1" style="text-align: left;" >21551.72
</td>

<td colspan="1" style="text-align: left;" >23364.49
</td>

<td colspan="1" style="text-align: left;" >23094.69
</td>

<td colspan="1" style="text-align: left;" >23923.44
</td>

<td colspan="1" style="text-align: left;" >17825.31
</td>
</tr>
<tr >

<td colspan="1" style="text-align: left;" >LPUSH
</td>

<td colspan="1" style="text-align: left;" >26178.01
</td>

<td colspan="1" style="text-align: left;" >20833.33
</td>

<td colspan="1" style="text-align: left;" >21413.28
</td>

<td colspan="1" style="text-align: left;" >20876.83
</td>

<td colspan="1" style="text-align: left;" >21008.40
</td>

<td colspan="1" style="text-align: left;" >23809.53
</td>

<td colspan="1" style="text-align: left;" >23696.68
</td>

<td colspan="1" style="text-align: left;" >23696.68
</td>

<td colspan="1" style="text-align: left;" >23148.15
</td>
</tr>
<tr >

<td colspan="1" style="text-align: left;" >LRANGE_100
</td>

<td colspan="1" style="text-align: left;" >17452.01
</td>

<td colspan="1" style="text-align: left;" >14534.88
</td>

<td colspan="1" style="text-align: left;" >14245.01
</td>

<td colspan="1" style="text-align: left;" >14684.29
</td>

<td colspan="1" style="text-align: left;" >14144.27
</td>

<td colspan="1" style="text-align: left;" >15060.24
</td>

<td colspan="1" style="text-align: left;" >15267.18
</td>

<td colspan="1" style="text-align: left;" >15408.32
</td>

<td colspan="1" style="text-align: left;" >15503.88
</td>
</tr>
</tbody>
</table>



![redis压力测试对比](/images/2013/11/redis_test.jpg)


## Twemproxy后端接入不同 Redis 数量测试对比


本测试主要验证 Twemproxy 后端接入不同 Redis 数量后，测试的ops比较，结果只能作为参考意义。

设置测试方式，Twemproxy 的分布方式设置为 hash，测试对比如上图。基本上和单台redis性能差不多。实际看后端也只访问一台redis。

分布参数设置为random模式。可以比较均匀分布。

测试类似命令：


<blockquote>/usr/local/redis/bin/redis-benchmark -h 192.168.2.65 -p 9999 -t SET  -n 1000000 -c 100 -q</blockquote>


目前环境： 2台机器都搭建 Twemproxy Server，每台端口为9999，19999，29999

目前6台 Redis 分别测试的结果为:


<blockquote>(31438.63+32406.51+37087.86+26886.78+34291.20+32544.67)/6=32442.6083</blockquote>


可以近似看成是 Redis 的单台性能。

测试方式：

1.后端 Redis 节点数量不变，不同 Twemproxy server 测试及多个同时运行测试结果如下：



<table > 
<tbody >
<tr >

<td style="text-align: left;" >twemproxy server运行数量(port)
</td>

<td style="text-align: left;" >1(A server)
</td>

<td style="text-align: left;" >1(B Server)
</td>

<td style="text-align: left;" >2
</td>

<td style="text-align: left;" >4
</td>

<td style="text-align: left;" >6
</td>
</tr>
<tr >

<td style="text-align: left;" >测试结果(/s)
</td>

<td style="text-align: left;" >30278.26
</td>

<td style="text-align: left;" >32867.71
</td>

<td style="text-align: left;" >35143.28
</td>

<td style="text-align: left;" >40176.777
</td>

<td style="text-align: left;" >52345.5152
</td>
</tr>
</tbody>
</table>



从上面数据可以看出，单台最多也只能达到单个 Redis 的性能；2个节点运行性能增加大概110%左右。4个 server 运行，性能大概增加了123%，6个 server 接入运行160%。

2.前端使用1个 Twemproxy server，后端 Redis 数量分别为2，3，4，5，6来进行压力测试，看测试结果,测试数据如下：



<table > 
<tbody >
<tr >

<td style="text-align: left;" >redis节点数
</td>

<td style="text-align: left;" >2
</td>

<td style="text-align: left;" >3
</td>

<td style="text-align: left;" >4
</td>

<td style="text-align: left;" >5
</td>

<td style="text-align: left;" >6
</td>
</tr>
<tr >

<td style="text-align: left;" >测试结果(/s)
</td>

<td style="text-align: left;" >34882.1
</td>

<td style="text-align: left;" >34749.97
</td>

<td style="text-align: left;" >32296.61
</td>

<td style="text-align: left;" >32438.04
</td>

<td style="text-align: left;" >32867.71
</td>
</tr>
</tbody>
</table>



从数据可以看出，后端节点数量与 Twemproxy 的性能基本无关，最大性能也就是单个 Redis 的性能。


## Twemproxy功能测试




1.Twemproxy 正常访问，后端 Redis 挂掉一台，前端访问是否正常;后端 Redis 挂掉的恢复，不重启 Twemproxy，观察恢复的数据是否有继续增加

从测试结果看，基本正常，由于使用命令/usr/local/bin/redis-cli，无法看到错误信息，不能确认在中断瞬间是否有报错。

从各 Redis 的 keys 数量看，基本可以满足.测试过程中 Redis 中断不到1分钟。从实际数据看只丢失了15个key(总key数量为26W)，可以认为Twemproxy 能够自动摘掉故障的 Redis 及自动恢复。
<table align="left" border="0" >
<tbody >
<tr >

<td style="text-align: left;" >#初始化redis，各redis中的keys为空
[root@test_2_68 bin]$ sh getkeys.sh
192.168.2.68:10000 keys: 0 or not exists
192.168.2.68:10002 keys: 0 or not exists
192.168.2.68:10003 keys: 0 or not exists
192.168.2.65:10000 keys: 0 or not exists
192.168.2.66:10000 keys: 0 or not exists
192.168.2.67:10000 keys: 0 or not exists
#运行测试程序一段时间后查看
[root@test_2_68 bin]$ sh getkeys.sh
192.168.2.68:10000 keys: 5055
192.168.2.68:10002 keys: 5619
192.168.2.68:10003 keys: 6031
192.168.2.65:10000 keys: 5708
192.168.2.66:10000 keys: 4646
192.168.2.67:10000 keys: 4453
#kill掉一台redis后查看
[root@test_2_68 bin]$ sh getkeys.sh
192.168.2.68:10000 keys: 9045
192.168.2.68:10002 keys: 8860
192.168.2.68:10003 keys: 9552
192.168.2.65:10000 keys: 12047
192.168.2.66:10000 keys: 8920
Could not connect to Redis at 192.168.2.67:10000: Connection refused
192.168.2.67:10000 keys: 0 or not exists
#恢复启动redis后查看
[root@test_2_68 bin]$
[root@test_2_68 bin]$ sh getkeys.sh
192.168.2.68:10000 keys: 14170
192.168.2.68:10002 keys: 11525
192.168.2.68:10003 keys: 13349
192.168.2.65:10000 keys: 15750
192.168.2.66:10000 keys: 12206
192.168.2.67:10000 keys: 9327
#测试程序运行完毕后查看
[root@test_2_68 bin]$ sh getkeys.sh
192.168.2.68:10000 keys: 43186
192.168.2.68:10002 keys: 38090
192.168.2.68:10003 keys: 43069
192.168.2.65:10000 keys: 49291
192.168.2.66:10000 keys: 46428
192.168.2.67:10000 keys: 39921
#测试程序总共插入26W keys。从测试中看基本差15key，可以认为是redis中断期间未插入的。生产上如有容错机制，应可以接受
[root@test_2_68 bin]$ echo "43186+38090+43069+49291+46428+39921"|bc
259985
</td>
</tr>
</tbody>
</table>


2.正常装载一部分数据，计算后端各 Redis 的 key 分布情况是否均匀
<table align="left" border="0" >
<tbody >
<tr >

<td style="text-align: left;" >#总共插入26W个key,通过twemproxy的端口操作
$sh getkeys.sh
192.168.2.68:10000 keys: 42881
192.168.2.68:10002 keys: 37990
192.168.2.68:10003 keys: 42600
192.168.2.65:10000 keys: 48144
192.168.2.66:10000 keys: 45905
192.168.2.67:10000 keys: 42480
#从操作结束后查看各redis的keys看，基本上能够差不多一致，每个redis分布相对比较均匀
</td>
</tr>
</tbody>
</table>


3. Twemproxy 默认使用(**distribution: ketama**)先使用后端3个节点装载key数量：300708 ，然后后端节点增加到6个(**distribution: ketama**)，在装载300708个 key 值，对比分布趋势：
<table align="left" border="0" >
<tbody >
<tr >

<td style="text-align: left;" >#插入300708个key，后端节点为3个redis
[root@test_2_68 bin]$ sh getkeys.sh
192.168.2.68:10000 keys: 95673
192.168.2.68:10002 keys: 0 or not exists
192.168.2.65:10002 keys: 0 or not exists
192.168.2.65:10000 keys: 94225
192.168.2.66:10000 keys: 110810
192.168.2.66:10002 keys: 0 or not exists
#继续插入300708个key，后端节点为6个redis
[root@test_2_68 bin]$ sh getkeys.sh
192.168.2.68:10000 keys: 140883 #新增45210
192.168.2.68:10002 keys: 51135
192.168.2.65:10002 keys: 49022
192.168.2.65:10000 keys: 144687 #新增50462
192.168.2.66:10000 keys: 167928 #新增57118
192.168.2.66:10002 keys: 47761
#可以看出，新增加后，数据继续增加还是根据key比较均匀分布,与已经存在的数据无关.现有的数据需要自己使用脚本重新分割
</td>
</tr>
</tbody>
</table>


相同算法下，后续的数据都可以正常提取查找，但是原来已经在 Redis 的数据信息，部分找不到。具体数据如下：



<table >
<tbody >
<tr >
算法规则
总keys数
未找到的keys
找到的keys
找到keys的比例
备注
</tr>
<tr >

<td style="text-align: left;" >distribution: ketama
</td>

<td style="text-align: left;" >300708
</td>

<td style="text-align: left;" >147757
</td>

<td style="text-align: left;" >152951
</td>

<td colspan="1" style="text-align: left;" >51.86%
</td>

<td style="text-align: left;" >缺省默认的算法
</td>
</tr>
<tr >

<td style="text-align: left;" >distribution: random
</td>

<td style="text-align: left;" >300708
</td>

<td style="text-align: left;" >250731
</td>

<td style="text-align: left;" >49977
</td>

<td colspan="1" style="text-align: left;" >16.62%
</td>

<td style="text-align: left;" > 
</td>
</tr>
<tr >

<td style="text-align: left;" >distribution: modula
</td>

<td style="text-align: left;" >300708
</td>

<td style="text-align: left;" >250408
</td>

<td style="text-align: left;" >50300
</td>

<td colspan="1" style="text-align: left;" >16.72%
</td>

<td style="text-align: left;" > 
</td>
</tr>
<tr >

<td style="text-align: left;" >distribution: ketama
</td>

<td style="text-align: left;" >300708
</td>

<td style="text-align: left;" >147757
</td>

<td style="text-align: left;" >152951
</td>

<td colspan="1" style="text-align: left;" >50.86%
</td>

<td style="text-align: left;" >缺省默认的算法
</td>
</tr>
</tbody>
</table>



从上面可以看出，增加后端 Redis 后，Twemproxy 使用计算新的算法 key 保存的值。从缺省算法的成功率上可以看出，找不到的比例和增加的新的 Redis 点有一定关系(刚好大概一半找不到。我们增加了1倍的节点)


## 数据一致性测试


测试方法，分别开3个 Twemproxy server(port)，分布格式为 ketama，ketama，random。从其中一个 ketama 分布的插入。然后分别从其他2个不同类型的 server 读取，判断读取值是否正确。

装载数据共300708记录，然后使用 get 方式读取，对应的 key 还是从源文件读取 key。可以看到，类型为 ketama 的2台 server 都可以全部获取到对应的key值，而 random 有2/3获取不到(总记录300708，能获取到key的记录:100347).


# Reference


在测试中参考的网络部分资料，本文章中部分内容也有引用，在此感谢！



	
  * 官网：[https://github.com/twitter/twemproxy](https://github.com/twitter/twemproxy)

	
  * 翻译介绍： [http://1.breakwang.sinaapp.com/?p=78](http://1.breakwang.sinaapp.com/?p=78)

	
  * 安装测试：[http://blog.mkfree.com/posts/515bce9d975a30cc561dc360#](http://blog.mkfree.com/posts/515bce9d975a30cc561dc360)

	
  * Redis : [http://redis.io/](http://redis.io/)


		
