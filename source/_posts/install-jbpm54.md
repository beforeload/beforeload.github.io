title: Install JBPM 5.4
date: 2013-09-03 22:49:38
categories: Linux
tags:
- Linux 
- Ubuntu
- Java
---

## JBPM5.4环境搭建

### Step 1: 准备工作

1. 下载JDK，安装配置Java环境
2. 下载Ant，配置Java自动化脚本引擎

<!-- more -->

__备注:__

1. Linux的JDK安装可以参考我的另外[一篇博文](http://beforeload.github.io/2013/06/29/building-distributed-systems-development-environment/)
2. Windows的安装可以参考[这里](http://www.cnitblog.com/intrl/archive/2009/04/11/56254.aspx)

### Step 2: 下载JBPM 5.4安装相关开发工具

1. 下载[jbpm-5.4.0.Final-installer-full.zip](http://softlayer-dal.dl.sourceforge.net/project/jbpm/jBPM%205/jbpm-5.4.0.Final/jbpm-5.4.0.Final-installer-full.zip)
2. 解压缩，从控制台(Windows用户是command)进入相应文件夹下，然后输入命令

    ant install.demo

这个过程是下载Eclipse, Jboss等工具，自动化构建开发环境。

备注： 之所以选择这样的构建方式，是因为不想在环境搭建上浪费太多时间，以便拥有更多的时间投入JBPM5的学习开发过程中。

### Step 3: 测试环境是否搭建成功

1. 启动JBPM

    ant start.demo

在控制中可以看到相关的启动情况，会看到自动化的启动了若干组件。

2. 重点是测试JBoss启动

在控制台输入http://localhost:8080/，会看到Jboss启动的情况，大致应该如下所示：

{% img http://i.minus.com/iHKyfjT9AxDg8.png %}

3. Web管理控制台

打开浏览器，输入http://localhost:8080/jbpm-console，重点看一下

登录的用户名、密码均为krisv 

4. 使用Guvnor仓库和设计 

输入如下地址可以进入

http://localhost:8080/drools-guvnor

最后，视频演示请戳[这里](http://people.redhat.com/kverlaen/install-gwt-console-jbpm.swf)

### 参考博文：

 1. [jbpm5.2学习------安装与配置](http://wanglu271991027.iteye.com/blog/1495799)
 2. [JBPM5.1的介绍](http://www.cnblogs.com/skyme/archive/2011/11/07/2238782.html)
 3. [jBPM5 入门](http://w26.iteye.com/blog/998431)
 4. [Jbpm5安装](http://blog.chinaunix.net/uid-122937-id-3731868.html)
