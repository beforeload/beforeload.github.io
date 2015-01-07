title: Singleton in JavaScript
date: 2015-01-07 19:22:27
tags: JavaScript
---

JavaScript 中的单例
===================

在 JavaScript 中，单例的应用场景非常广泛，通过Review 了一下自己的代码（其实是不小心看到的自己之前的一段 ugly code），发现之前对单例的实现思路一直以来比较局限，写一篇博客顺便理一下实现思路。

<!-- more -->

我的传统实现如下：

```JavaScript
var Singleton = function() {
    var instance;
    Singleton = function() {
        return instance;
    }
    Singleton.prototype = this;
    instance = new Singleton();
    instance.Constructor = Singleton;
    return instance;
};

```

实现思路也算是很简洁，通过缓存实例，重新构造函数，然后将this 赋值给原型，再重新定义构造函数指针。很中规中矩的实现方式。 
后来想想可以优化这段代码，只需要重写构造函数即可。

```JavaScript
var Singleton = function() {
    var instance = this;
    Singleton = function() {
        return instance;
    }
};

```

这样的实现已经很简洁了，我自己觉得也没什么不好，但是看到别人的实现，
感觉同样惊艳。

```JavaScript
var Singleton = function(){
    if(Singleton.__instance) {
        return Singleton.__instance;
    }
    Singleton.__instance = this;
};

```

非常容易，还可以简化成一行：

```JavaScript
var Singleton = function(){
  return Singleton.instance = Singleton.instance ? Singleton.instance : this;
};

```

JavaScript 中的单例模式其实很简单，但由此引发的思路也很多，即使用对象字面量的方式也无可厚非，引用类的方式优点也很明显，例如隐藏私有方法，保护私有变量。

