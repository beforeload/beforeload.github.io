title: What is Inheritance in JavaScript
date: 2014-04-13 18:20:55
update: 2014-04-13 18:20:55
categories: JavaScript
tags: JavaScript
---

# 理解JavaScript中的继承

什么是继承？简而言之，父亲和儿子，儿子对父亲有继承关系。一句话能说明的关系在JavaScript中，使用起来并不容易。

从以下几个方面理解JavaScript的继承：

1. 类，对象和原型
2. 类的定义
3. 原型继承

<!-- more -->

## 类，对象和原型

JavaScript中继承机制其实一点都不复杂，JavaScript是基于对象的弱类型语言，以对象为基础，以函数为模型，以原型为继承机制。
对JavaScript的继承的理解，不能像Java或者其他语言的类继承一样去理解，而要理解原型继承。

首先理解原型。字面上理解，prototype是类的原型。作为构造函数的属性，一个对象拥有的原始的属性和方法都在prototype中。
也可以这样理解，对于守财奴的藏宝库，重要的宝贝都放在里面。prototype就相当于这样的一个藏宝室。
所以我们可以把prototype理解成一个数据集合，理解成一个指向宝藏的指针。

类是对象的模型化，而对象则是类和实例的关系演化，守财奴的父亲拥有一大笔财产（类是模型），他的每个儿子都对他的财产有继承权（对象继承）。
所以对于构造类（父亲）实例化的构造实例（儿子），构造实例（儿子）的原型对象（Prototype）指向了构造类（父亲）的原型属性（prototype）。

在JavaScript中没有传统意义上的类，而类的思想是用函数来模拟和表达。在控制台中可以体验一下下面的代码：

```JavaScript
function P(x) {
    this.x = x;
}

P.prototype.x = 1; // 这里会被上面的属性x覆盖
var s = new P(10);
var s1 = new P();
alert(s.x);  // 10
alert(s1.x);  // undefined
```

对象查询属性的顺序是最优先查询本地属性，如果没有查询对象本身的原型属性。因此，
虽然通过prototype设置了属性x的值，但是由于构造函数的原型属性和本地属性同名了，所以本地属性覆盖了原型属性。

JavaScript是动态的语言，设置原型的值会影响所有的实例。所以对上面的代码可以这样修改一下：

```JavaScript
function P(x){      //构造方法
    if(x) {
        this.x = x;
    }
}

P.prototype.x = 1;
var s = new P();
alert(s.x);   // 1
var s1 = new P(10);
alert(s1.x);  // 10

```

关于prototype属性的应用还有很多方面，比如说数据备份，设置只读属性，批量复制等，这里不一一赘述。

在JavaScript每个object都会有prototype属性，prototype指向另外的一个object对象。
通过上面的例子，我们可以知道，当我们需要读取一个object中某个属性x的时候，JavaScript引擎先查找这个object本身是否存在属性x。
如果不存在，才会查找这个object的prototype中是否存在属性p。

当我们new一个对象时，会给这个对象设置prototype属性，这个属性值来自它的构造函数的prototype属性值。
所有函数在定义时，就自动创建和初始化了prototype属性。这个prototype属性指向只包含constructor属性的对象，而constructor属性指向function本身。
因此，每一个object都会有一个constructor属性。

下面详细讲解以下类的定义的几种方式。

## 类的定义

在JavaScript中，定义一个类通常有四种方式：

（1）工厂模式

```JavaScript
/* 减少重复创建函数的开销 */
var method = function() {
        alert(this.name + ':' + this.age);
}
function Person(name, age) {
    var p = new Object();
    p.name = name;
    p.age = age;
    p.what = method;
    return p; // 初始化后的对象
}
```

它是伪装的构造函数，不推荐使用。

(2) 构造函数模式

```JavaScript
function Person(name, age) {
    this.name = name;
    this.age = age;
}

var p = new Person('Daniel', 22);
```

上面this指向当前实例对象。如果构造函数返回对象，那么返回的对象会覆盖this的值。如下：

```JavaScript
function Person(name, age) {
    this.name = name;
    this.age = age;
    return this;
}
```

(3) 原型模式

```JavaScript
function Person() {}
Person.prototype.name = 'Daniel'
Person.prototype.age = 22
Person.prototype.what = function(){
    alert(this.title + ': ' + this.age);
}
```

原型模式和构造函数模式的区别就是，构造函数模式的属性值是本地化的，而原型模式的属性值一旦更改，所有实例都会受到影响。

(4) 构造函数 + 原型模式

因为在原型模式不能轻易更改一个对象实例的属性值，而构造函数模式中又希望能够共享的使用一些属性和方法，所以引入“构造函数 + 原型模式”的混合设计。

如下：

```JavaScript
function Person(name, age) {    // 构造函数的设计
    this.name = name;
    this.age = age;
}

Person.prototype.what = function() {    // 原型模式设计
    alert(this.name + ': ' + this.age);
}
```

如果我们希望遵循面向对象的设计原则，类成员都封装在类结构中，那么我们可以对构造函数原型模式进行加锁方式修改：

```JavaScript
function Person(name, age) {
    this.name = name;
    this.age = age;
    if(typeof Person.isLock == 'undefined') {
        Person.prototype.what = function() {
            alert(this.name + ': ' + this.age);
        }
        Person.isLock = true;  // 避免重复创建
    }
}
```

## 原型继承

原型继承可以完全从对象角度来考虑，而淡化类和实例。因为在原型继承中，不用去定义类，直接定义对象，当对象被其他对象引用时，形成了继承关系。
这种继承关系可以通过原型链来查找。其中引用对象称为原型对象（object prototype）。

```JavaScript
function A(x) {
    this.x1 = x;
    this.get = function(){
        return this.x1;
    }
}

function B(x) {
    this.x2 = x;
    this.add = function(){
        return this.x2 + this.x2;
    }
}

B.prototype = new A(1);
function C(x) {
    this.x3 = x;
    this.mul = function(){
        return this.x3 * this.x3;
    }
}
C.prototype = new B(2);

var b = new B(2);
var c = new C(3);
alert(b.x1);    // 1
alert(c.x1);    // 1
alert(c.mul()); // 9
alert(c.add()); // 4
```

原型继承是直接复制已经存在的原型对象实现继承关系，基于原型而没有类的概念。不能很好的支持多参数和动态参数的父类。
结构简单，不支持多重继承，不够灵活，原型声明的同时需要实例化父类对象，限制了父类实例化的灵活性。

## 类继承


## 封装类继承

## 实例继承

## 复制继承

## 克隆继承
