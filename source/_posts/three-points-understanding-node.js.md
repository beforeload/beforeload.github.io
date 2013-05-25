title: Three-points to Understanding Node.js
date: 2013-05-04 02:24:52
tags:
- Node.js
- JavaScript
categories: Node.js
---

##从三点去理解Node.js

Node is:  

  * 基于JavaScript(Built on JavaScript)
  * 事件驱动和异步(Evented and Asynchronous)
  * 对于数据密集型实时应用(For Data-Intensive Real-Time Applications)

<!-- more -->

###基于JavaScript

第一点很好理解，是用JavaScript开发，由谷歌的[V8引擎](https://code.google.com/p/v8/)驱动运行。Node使用V8,在服务端运行JavaScript，减少了中间步骤，执行过程中，解释器直接编译成自然机器语言。好处多多：

1. 开发者只需要用一种语言就可以开发一套网络应用，减少服务端和客户端开发时所需要的上下文切换，且允许服务端和客户端代码共享(例如重用表单验证，或者游戏的逻辑)。
2. JSON这种数据格式很性感，它就是自然的JavaScript
3. JavaScript可以使用多种NoSQL数据库（如：CouchDB/MongoDB），可以和他们无缝连接，非常适合（如：MongoDB shell和查询语言就是JS，CouchDB map/reduce 是JS）。
4. 有很多语言以JavaScript为编译目标      
[List of languages that compile to JS](https://github.com/jashkenas/coffee-script/wiki/List-of-languages-that-compile-to-JS)
5. Node使用虚拟引擎V8,并遵守ECMAScript标准。

###时间驱动和异步

第二点，Node.js给服务器端JavaScript提供了一个事件驱动和异步的平台。JavaScript跑在服务端其实和JavaSript跑在客户端在很大程度上是相似的。理解浏览器的怎么运行对于理解Node的运行很重要。两者都是事件驱动（使用的是事件循环）和无阻塞IO处理（如：异步IO）。

未完！

###DIRTy Applications(___Data-Intensive Real-Time___ Applications)




###推荐资源：

1. 强烈推荐视频[Javascript: Your New Overlord](//www.youtube.com/watch?v=Trurfqh_6fQ), 由Douglas Crockford大神主讲。
