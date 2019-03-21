---
author: admin
comments: true
date: 2012-12-20 07:21:58+00:00
excerpt: "\n\t\t\t\t\t\t"
layout: post
link: http://blog.jiguang.cn/rundeck_server_maintenance_command_script/
slug: rundeck_server_maintenance_command_script
title: "\n\t\t\t\t用RunDeck对服务器进行自动化\t\t"
wordpress_id: 258
categories:
- 技术文章
tags:
- RunDeck
- Server Maintenance
---


				对运维同事来说，配置、部署、维护多台服务器是家常便饭。如果手动的ssh到各个服务器执行命令，既费时又费力，而且容易出错。当然我们也可以借助一些工具，比如pssh，fabric，dsh进行批量化处理。相对于这些命令行/脚本工具，RunDeck更胜一筹，它不但可以完全替代这些工具，还提供其它一些功能，让我们可以更好的对服务器进行自动化。

**RunDeck****介绍**

RunDeck是用Java/Grails写的开源工具，帮助用户在数据中心或者云环境中自动化各种操作和流程。通过命令行或者web界面，用户可以对任意数量的服务器进行操作，大大降低了对服务器自动化的门槛。RunDeck提供如下功能：

1. 提供web界面和命令行来执行shell命令和job

2. 自定义job步骤

3. 设置shell命令/job运行周期（类似cron table的功能）

4. 用户权限控制，支持LDAP/ActiveDirectory

5. 保存历史日志

6. 提供web API

通过以上功能，RunDeck可以在任意数量的服务器上批量执行不同的任务，降低对自动化的部署、执行、维护的工作。

**RunDeck****架构**

RunDeck是基于Java的webapp服务，运行在Servlet容器里。用户登陆RunDeck，在web界面进行操作，请求对远程服务器进行操作。web通过HTTP(s)与RunDeck服务进行通信，RunDeck服务接受服务请求后，从配置文件里面读取用户权限，以及服务器资源的一些信息。结合权限以及资源信息，RunDeck服务通过ssh对远程的服务器进行操作，并将操作结果存放在数据库中。

[![](/images/2012/12/fig0001-300x215.png)](/images/2012/12/fig0001.png)

**RunDeck****的应用**

在我们公司，推送后台需要大量的服务器做支撑，也就有需求对服务器做各种配置，安装，升级等操作。通过RunDeck，可以很方便、快速的完成这些任务。

为了更好的利用RunDeck，我们主要从以下2个方面来配置，规划，管理我们的服务器资源和脚本资源。

1. 用project和tag对服务器进行分类

根据服务器资源的不同作用，可以将服务器组分成不同的项目类别，方便不同用户操作。比如我们就有一个专门的project是管理push功能的服务器。通过配置文件，可以赋予用户不同的权限，有些用户只能查看push组的服务器资源，有些用户可以操作。

虽然用project可以对不同服务器进行分组，但是同一个project的服务器有不同的作用。比如在push组的服务器项目中，又可以分数据库服务器，下载服务器，日志服务器等等。对于这种情况，通过给服务器打不同tag，这样需要对一批服务器执行相同操作，就非常方便。

2. 通过job来管理任务

RunDeck提供一个非常强大的功能，就是可以自己定义job。job由命令、脚本、其它的job组成。比如对于服务器上的各种web server，可以定义不同的job，执行不同的操作。实际配置中，我们就对apache服务器定义了三个job，分别对应启动、停止、重启apache服务。对于nginx服务器，定义job来配置nginx的端口号。通过job机制，用户可以定义、管理、维护各种脚本、操作，极大的提高自动化效率。

[![](/images/2012/12/job_new-300x150.png)](/images/2012/12/job_new.png)

**RunDeck ****经验与技巧**

在实际运用中，碰到一些问题，总结如下：

1. ssh通过查看命令返回值来判断命令执行结果，如果非0显示为失败。 尽量避免返回值非0但是命令执行成功的情况，例如下面情况应该避免


<blockquote>_#/etc/init.d/sshd status
openssh-daemon is stpped
# echo $?
3_</blockquote>




2. 需要root权限时，命令前面加sudo。（如果是内网机制，相应帐号可以配置成有无密码的sudo权限，而且sudoers配置中需要取消tty）




3. sudo只能运行可执行命令，不能运行 shell内置命令，比如"sudo cd /root"命令会出现错误




4. rundeck会把所有命令的log都输出，建议禁止一些命令脚本的log输出





<blockquote>_$tar xzvf xxx.tar.gz >/dev/null 2>&1
if [ $? -ne 0 ]; then
echo "extract failed"
exit 1
fi_</blockquote>




5. run页面的History默认是当天的，如果需要其它日期的，需要到History页面查找




6. 需要经常执行的命令建议保存为Job，Command默认的名字都是adhoc，不好区分




7. 无法执行交互命令




**参考资料**

[http://rundeck.org/](http://rundeck.org/)		
