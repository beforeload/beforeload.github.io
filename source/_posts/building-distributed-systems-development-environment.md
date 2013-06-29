title: Building Distributed Systems Development Environment
date: 2013-06-29 01:13:47
categories: Ubuntu
tags: 
- Ubuntu
- Linux
- Hadoop
---

## 基于Hadoop的分布式系统开发环境在Ubuntu 13.04中的搭建

由于实验学习以及开发测试的需求，需要安装集群和伪分布式开发环境。

分为以下安装过程：

1. 安装jdk
2. 安装eclipse
3. 安装hadoop

<!-- more -->

#### 安装jdk

下载JDK，Ubuntu上下载最好不要使用FTP下载，不然会出现例如下面的问题。

    gzip: stdin: invalid compressed data--format violatedtar: Unexpected EOF in archivetar: Unexpected EOF in archivetar: Error is not recoverable: exiting now

从[官网](http://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html)上根据自己的系统下载相应的JDK。

    sudo mkdir /usr/java
    tar zxvf jdk-7u25-linux-x64.tar.gz
    sudo mv jdk-7u25-linux-x64.tar.gz /usr/java/

然后修改配置文件

    sudo vi /etc/profile

按G可以直接到文件尾端，填写下面内容:

    export JAVA_HOME=/usr/java/jdk1.7.0_25
    export JRE_HOME=$JAVA_HOME/jre
    export CLASSPATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar
    export PATH=$PATH:$JAVA_HOME/bin

保存退出。

    source /etc/profile
    java -version

如果显示下面结果，表示安装成功。

    java version "1.7.0_25"
    Java(TM) SE Runtime Environment (build 1.7.0_25-b15)
    Java HotSpot(TM) 64-Bit Server VM (build 23.25-b01, mixed mode)

