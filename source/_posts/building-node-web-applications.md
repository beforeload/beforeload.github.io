title: Building Node web Applications
date: 2013-06-10 18:10:49
tags: Node.js
categories: Node.js
---

## Node.js网络应用开发

1. Handling HTTP requests with Node's API
2. Building a RESTful web service
3. Serving static files
4. Accepting user input from forms
5. Securing your application with HTTPS

<!-- more -->

Node.js网络应用开发的简单图示如下：

{% img http://i.minus.com/iLMAoN6C3pEtN.png %}

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

1. 使用POST创建资源
2. 使用GET获取资源
3. 使用DELETE删除资源

    function(req, res){
      switch(req.method){
      case: 'GET': 
        // to do
        break
      case: 'POST':
        // ...
        break
      case: 'DELETE':
        // ...
        break
      }
    }

### Serving static files

1. Get /index.html 用户向服务器请求一个静态文件
2. Node 服务器接受请求，应用的逻辑部分去读取这个文件
3. 文件通过一个读取流的实例传递给服务器
4. 文件的ReadStream通过'piped'拼接方式，以HTTP的response形式返回给客户端

    ...
    var stream = fs.createReadStream(path)
    // stream.on('data', function(chunk){
    //   res.write(chunk)
    // })
    // stream.on('end', function(){
    //   res.end()
    // })
    stream.pipe(res)
    ...

相关的图片如下：

{% img http://i.minus.com/iOZW6WxLIsBNk.png %}

...
stream.pipe(res)
stream.on('error',function(err){
  res.statusCode = 500
  res.end('Internal Server Error!')
})
...

文件不存在错误处理"ENOENT, No such file or dictionary"

根据``fs.stat(path, function(err, stat){})``的err判断

### 接受用户表单提交数据

1. 表单域
2. 上传文件node-formidable
3. 实时计算上传进度

两种类型的表单提交

* "application/x-www-form-urlencoded": the default for HTML forms
* "multipart/form-data": used when form contains files, non-ascii, or binary data

node-formidable这个包可以帮助我们解决表单提交的绝大多数问题。
也可以计算伪实时的文件上传进度，如下所示：
  
    form.on('process', function(bytesReceived, bytesExpected){
        var percent = bytesReceived / bytesExpected * 100
        console.log(percent)
    })

剩下的就是将进度在前端页面上显示出来，利用如socket.io这样的实时模块可以轻松的搞定。我将在我的Repo: [node-upload](https://github.com/beforeload/node-upload)中完成它。

### 使用HTTPS 确保应用的安全性

生成一个私钥(加密)
    openssl genrsa 1024 > key.pem

生成证书(解码)
    openssl req -x509 -new -key key.pem > key-cert.pem

___HTTPS server选项___
    var https = require('https')
      , fs = require('fs')
      , options = {
        key: fs.readFileSync('./key.pem'),
        cert: fs.readFileSync('./key-cert.pem')
      }

    https.createServer(options, function(req, res){
      res.writeHead(200)
      res.end('Hello world!')
    }).listen(4000)



证书可以共享，它包括public key和持有者的身份信息。当然如果要发布一个网站，就需要在Certificate Authority(CA)上去注册，获取一个真实的，受信任的SSL证书。
