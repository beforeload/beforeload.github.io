title: 内容聚合Nodejs的实现
date: 2013-04-28 10:16:15
tags: 
- Translate
- Node.js
categories: Node.js
---

###[翻译]Content Syndication with Node.js

-----------
原文链接 [Content Syndication with Node.js](http://howtonode.org/content-syndication-with-node)

对于任何一个想和其他系统信息共享的网站而言，网络聚合是必须的。建议最好去了解一下RSS或者Atom之类的消息来源标准的格式规范，如果不采用一个处理这种格式的模块，生成这样的文本信息会是一件非常麻烦且耗时的工作。非常感谢[NPM](https://npmjs.org/)，正由于nodejs的包管理工具的强大之处，自从有了它，再也不用担心生成自己的消息来源会耗费大量时间了。

<!-- more -->

####安装feed包
  在开始之前，进入你的项目文件夹下，安装feed的最新版本

```
$npm install feed
```

####建立订阅

第一步：建立一个Feed(用来接收该信息来源更新的接口)的对象。当我们初始化这个对象的时候，提供我们网络聚合订阅的基本信息（这句话感觉怎么翻译怎么不通顺，我觉得就是根据我们了解的RSS或者Atom之类的格式，结合自己的网站提供相应信息）。如下：

```
// 需求的包
varFeed=require('feed');

// 初始化feed对象
var feed =newFeed({
  title:          'My Feed Title',
  description:    'This is my personnal feed!',
  link:           'http://example.com/',
  image:          'http://example.com/logo.png',
  copyright:      'Copyright © 2013 John Doe. All rights reserved',

  author:{
    name:       'John Doe',
    email:      'john.doe@example.com',
    link:       'https://example.com/john-doe'
  }
});
```


第二步：你可能希望区分自己的订阅专题。RSS和Atom标准格式都提供了一个或多个分类。当然，添加它们也超级简单：

``
feed.category('Node.js');
feed.category('JavaScript');
``

第三步：每个订阅都需要至少一个项目。（最好是一个入口）要做到这一点，你必须使用这些功能项目并提供合适的对象。当然，当你正在运行一个内容网站（就像你的博客），很可能发生的情况是你有多个项目。为了填充你的订阅信息，使用一个for循环，如下：

```
for(var key in posts){
  feed.item({
    title:  posts[key].title,
    link:  posts[key].link,
    description: posts[key].description,
    date: posts[key].date
  }
}
```


到这一步，生成RSS或者Atom标准的订阅的一切准备都已经OK了，使用下面的render方法

``
var output = feed.render()
``

这是含蓄的方式调用render请求。默认的是，它会渲染成RSS标准的订阅。你也可以使用这种含蓄的方式，选择RSS或者Atom

```
// Rendering a RSS 2.0 valid feed
feed.render('rss-2.0');

// Rendering an Atom 1.0 valid feed
feed.render('atom-1.0');
```

是的，就是这么简单！

feed结合 Express.js 一起会变得超级简单。告诉你一个使用app.get()方法去路由/rss路径。为了推送你的订阅，像我们之前提到的那样渲染我们的订阅。然后，把Content-type 设置成 text/xml：

```JavaScript
app.get('/rss',function(req, res){
  // Initializing feed object
  var feed =newFeed({
    title:          'My Feed Title',
    description:    'This is my personnal feed!',
    link:           'http://example.com/',
    image:          'http://example.com/logo.png',
    copyright:      'Copyright © 2013 John Doe. All rights reserved',

    author:{
      name:       'John Doe',
      email:      'john.doe@example.com',
      link:       'https://example.com/john-doe'
    }
  });

  // Function requesting the last 5 posts to a database. This is just an
  // example, use the way you prefer to get your posts.
  Post.findPosts(function(posts, err){
    if(err)
      res.send('404 Not found',404);
    else{
      for(var key in posts){
        feed.item({
          title:          posts[key].title,
          link:           posts[key].url,
          description:    posts[key].description,
          date:           posts[key].date
        });
      }
      // Setting the appropriate Content-Type
      res.set('Content-Type','text/xml');

      // Sending the feed as a response
      res.send(feed.render('rss-2.0'));
    }
  });
});
```
 
###总结：
就这样了，现在人们终于可以从你的nodejs应用中读取订阅你的项目。


译者：其实到这个地方还没有结束，本人在处理这个问题时感觉远远不想大神说的那么easy。有兴趣的最好自己尝试一下。


wrapper —— 包
feed —— 订阅
Feed —— 用来接收该信息来源更新的接口
