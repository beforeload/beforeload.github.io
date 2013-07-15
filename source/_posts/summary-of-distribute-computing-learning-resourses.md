title: Summary of Distributed Computing Learning Resourses
date: 2013-07-11 21:01:59
categories: Distributed Computing
tags: 
- Distributed Computing
- Hadoop
- Linux
---

## 分布式计算相关资源总结

### 论文

首先是论文，强烈建议看英文原版，那些翻译的还是敬而远之吧！

>Google出品，必属精品！

<!-- more -->

#### 四大经典论文(必看)

1. The Anatomy of a Large-Scale Hypertextual Web Search Engine
  
  分布式计算的奠基制作，介绍了Google搜索引擎的原型，讲解了一个基于超文本的大型系统建立过程。
  
2. The Google File System
  
  GFS这篇论文已然成为分布式文件系统相关技术的一种标准。对于分布式应用的接口，分布式系统设计，
  数据的存储以及分布式系统监控和管理相关的技术实现具有指导意义。

3. MapReduce: Simplified Data Processing on Large Clusters

  MapReduce的奠基之作，就凭它提出了MapReduce编程模式，就必须要读这篇论文。通过它，可以初窥云计算的世界。
  Google在四位数以上的个节点上运行MapReduce，并且还在持续扩展中。

4. BigTable: A Distributed Storage System for Structured Data.

  这篇论文主要实现了分布式存储数据的结构化，Google Earth和网页索引都是基于BigTable实现。论文很清晰的讲解了BigTable的设计实现思路和方法。

#### 三篇补充论文(推荐看)

1. Dynamo

 Google原始之作！

2. [Sinfonia: A New Paradigm for Building Scalable Distributed Systems](http://www.sosp2007.org/papers/sosp064-aguilera.pdf)
  
 阐述构建分布式文件系统的范式方法。淘宝构建TFS，OceanBase和Tair等系统的时候充分参考了这篇论文！
    
3. [The Chubby lock service for loosely-coupled distributed systems](http://static.googleusercontent.com/external_content/untrusted_dlcp/research.google.com/zh-CN//archive/chubby-osdi06.pdf)
  
  Google的BigTable，MapReduce和Spanner服务都是在这个基础上构建的！分布式事务的基础，我正在看这篇论文。

4. [Spanner: Google's Globally-Distributed Database](http://static.googleusercontent.com/external_content/untrusted_dlcp/research.google.com/es//archive/spanner-osdi2012.pdf)

第一个全球意义上的分布式数据库，推荐它的其中一个原因是，我完全看不懂！！！

#### 其他论文

这里还有一份别人总结的[分布式系统领域经典论文翻译集](http://duanple.blog.163.com/blog/static/709717672011330101333271/)。

___论文来源和下载：___

大多数论文来源于OSDI上。可以直接去会议网站上下载。直接google scholar一下，也可以下载。


### 书籍

#### 分布式计算

1. [分布式系统原理与范型](http://book.douban.com/subject/3108801/)
2. [分布式系统概念与设计](http://book.douban.com/subject/2698938/)

#### Hadoop

1. 《Hadoop in action》、 《Hadoop实战》
  
  我看的第一本分布式计算的书，里面的内容有点老。入门的书籍，所以推荐的时候难免掺杂了感情分。

2. 《Oreilly.Hadoop.The.Definitive.Guide.2nd.Edition 》

  中文版本就不推荐了，直接看英文版的吧！

3. 《Hadoop权威指南》

这本书同样适合初学者，书里面有基础的实例实现。属于分布式计算方面少数可以看的中文书。
翻译的比较成功的两本书！当然看原版更好！

#### 其他书籍

1. 《推荐系统实践》
2. 《集体智慧编程》

### 其他Hadoop教程

1. 《深入浅出Hadoop实战开发30讲》
2. 《Hadoop应用开发实战 28讲》
3. 《Cloudera Hadoop 4系列实战课程40讲》

### 总结

学习分布式计算，建议先去研究一下分布式计算的理论知识，然后学习一种框架，以Hadoop为例，从入门到精通！

1. 环境搭建

  [基于Hadoop的分布式系统开发环境在Ubuntu 13.04中的搭建](http://beforeload.github.io/2013/06/29/building-distributed-systems-development-environment/)

2. MapReduce

  [MapReduce](http://beforeload.github.io/2013/04/13/analyze-mapreduce/)

3. Hadoop的一点应用
  
  * [理解 Hadoop 的 Java API](http://beforeload.github.io/2013/04/06/hdfs-java-api/)
  * [倒排索引](http://beforeload.github.io/2013/04/18/invertedindex-in-hadoop/)


看论文和书籍只是一部分，懂理论才算刚刚入门！要努力做到能应用还要争取有创新！

另外, Hadoop不代表分布式，还有很多类似于Hadoop且比Hadoop更具有优势的框架，如Spark就更适合实验室内的研究的小规模分布式系统，Storm更适用于实时数据流。

附： 

1. [推荐系统公共资源汇总](http://aoyouzi.iteye.com/blog/1845235)

