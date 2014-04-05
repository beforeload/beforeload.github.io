title: Tutorial on Building Single Page Application with AngularJS and Node.js
date: 2014-04-06 01:08:48
update: 2014-04-06 01:08:48
categories: Node.js
tags: Node.js
---

# 用Node.js和AngularJS构建单页面应用
清明节放三天假，想来想去，还是把很早之前写的一个应用重构一下，用博客记录一下自己的开发过程，由于个人经验有限，不足之处请尽情吐槽。

<!-- more -->

### 准备工作

这三天主要完成三个模块：

1. 注册登录
2. 图书列表的增删查改
3. 个人书籍的增删查改

### 第一步：原型设计

也没有什么原型设计，也就注册登录几个简单页面。外加读书列表的增删查改，至于评论功能有时间再去完成它。

以前设计的原型图的初稿也可以参考一下，[戳这里](https://github.com/beforeload/navi/tree/master/doc/design)。

### 第二步： 数据库设计

详细文档[戳这里](https://github.com/beforeload/navi/blob/master/doc/db.md)

user表:

```
{
  _id: ObjectId,
  name: String(unique),
  email: String(unique),
  password: String,
  createTime: Date(Date.now),
  updateTime: Date(Date.now)
}
```

user_books表: (user,book关联表)

```
{
  _id: ObjectId,
  userId: ObjectId,
  books: [{
    bookId: ObjectId,
    addTime: Date(Date.now)
  }]
}
```

book表:

```
{
  _id: ObjectId,
  title: String,
  desc: String,
  author: String,
  press: String,
  releaseDate: Date,
  addTime: Date(Date.now),
  updateTime: Date(Date.now)
}
```

### 第三步：接口设计

设计RESTFul API接口, 详细文档[戳这里](https://github.com/beforeload/navi/blob/master/doc/api.md)

    /api/index (GET)   		    # 获取网站全局的配置，站点参数等
    /api/user/:userId (GET) 	# 已登录用户的个人信息，阅读过的书籍
    /api/user/:userId (PUT)  	# 修改个人信息
    /api/user/register  (POST)	# 用户注册
    /api/user/login (POST)	    # 用户登录
    /api/user/logout (GET)	    # 退出登录
    /api/account/active (GET)	# 账号激活
    /api/:userid/books (GET)	# 用户阅读书籍信息
    /api/:userid/books (POST)	# 用户增加阅读书籍
    /api/:userid/books (PUT)	# 用户修改阅读书籍
    /api/:userid/:bookId (DELETE)	# 用户删除阅读书籍
    /api/books      (GET)	    # 获取图书列表
    /api/books/list (GET)	    # 获取图书列表
    /api/books/:bookId (GET)	# 获取图书信息
    /api/books   (POST)		    # 添加图书
    /api/book/:bookId (PUT)	    # 修改图书
    /api/book/:bookId (DELETE)	# 删除图书

### 前后端数据接口

具体每个接口传输和接受的数据在每个模块开发中单独定义，这里先不列举出来了。

准备工作到这里结束了，明天完成注册登录模块。
