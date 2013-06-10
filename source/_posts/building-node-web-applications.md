title: Building Node web Applications
date: 2013-06-10 18:10:49
tags: Node.js
categories: Node.js
---

## Node.js网络应用开发

{% img http://i2.minus.com/iLMAoN6C3pEtN.png %}

### HTTP server 的基本面

#### HTTP请求在Node HTTP server中的生命周期

1. HTTP 客户端（例如：浏览器）发送HTTP请求
2. Node进程接受到连接把请求的数据发送给HTTP server
3. Node进程把HTTP请求头完整解析，在'request'的回调事件中处理相应的逻辑
4. request的回调函数中体现应用的逻辑，例如产生一个response
5. 请求通过HTTP server返回，这个过程会格式化成一个标准的HTTP response返回给客户端

### Building a RESTful web service 

CURD: Creat, Update, Read, Delete
HTTP: Get, Post, Put, Delete
