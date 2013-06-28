title: Building Webapps with Yeoman
date: 2013-06-28 22:11:40
tags: Node.js
categories: Node.js
---

## 使用Yeoman 快速构建Web应用

#### 简介

Yeoman不仅仅是一个应用。它也是一个工作流，一个工具集。

相关介绍参见[官网文档](http://yeoman.io/)

它由三个组件组成：

1. Yo 脚手架工具：我理解成项目工程目录和文件生成工具。用来构建生产环境和生成编译环境。
2. Grunt 构建工具：主要用来构建，预览和测试你的项目。
3. Bower 包管理工具：处理包依赖关系，不需要手动下载管理脚本。可以理解成前端的NPM。

__协作关系图：__
{% img [yo-grunt-bower] //images.cnitblog.com/blog/39469/201303/09214923-27fe6dea6eb34f468e601589ea83a675.png %}

从上面流水线一样的协作图，可以看出一个项目产品的生成可以做到很规范化，从而提高生成效率。

我觉得在说应用之前，先要思考一下使用Yeoman的场景，如果是我们负责一个项目，思考一下如下的问题：

1. 项目目录是怎样的？
2. 需要用到哪些类库？
3. (基于PHP和基于Node.js)生产环境是怎样的？
4. 如果使用CoffeeScript/sass/less，又需要增加什么样的编译环境？
5. 单元测试要如何进行？
6. 调试环境是怎么样的？
7. 打包部署要遵循什么样的流程？
8. ...

这些问题不能很好的解决，项目开发中都会存在或多或少的效率问题。

其实，解决他们很简单，yeoman提供方便快捷的解决方法，下面简单介绍一下如何构建一个项目。

### 安装它

第一步：首先确认拥有Node.js, Ruby的环境。

最好安装一下 Compass(Sass的工具库)
  
    sudo gem install compass

如果想了解一下Compass，推荐阮一峰的一篇[博文](http://www.ruanyifeng.com/blog/2012/11/compass.html)。

第二步：然后安装三个组件

    sudo npm install -g yo grunt-cli bower

第三步：安装网络应用生成器

选择web app生成器：

    sudo npm install -g generator-webapp

也可以选择安装AngularJS生成器：

    sudo npm install -g generator-angular  # install generator


### 快速构建

由于上面web应用生成器有两种，所以可以通过两种不同的方式构建我们的项目：

1. 使用`yo webapp`方式：

    yo webapp                     # 搭建项目骨架
    bower install underscore      # 依赖通过bower安装
    grunt                         # 构建测试发布环境

2. 使用`yo angular`方式：

    yo angular                    # 搭建项目骨架
    bower install angular-ui      # 依赖通过bower安装
    grunt test                    # 测试
    grunt server                  # 预览
    grunt  

### 总结

通过以上的步骤，我们很容易的构建出了一个简易的web项目。yeoman有很多特性，例如可以自定义模板（如HTML5, Boilerplate, Twitter Bootstrap等），AMD（通过RequireJS）等其他工具。

自动编译CoffeeScript和Compass也是一大亮点，值得一提的是，yeoman提供了live reload功能，在对项目文件修改保存后，前端页面回自动刷新。

还有太多太多特性了，如jslint完善脚本，内置HTTP服务器，集成包管理，ES6的语法支持，PhantomJS单元测试等等。希望大家使用愉快。
