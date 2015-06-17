理解JavaScript中的继承
====================

什么是继承？简而言之，父亲和儿子，儿子对父亲有继承关系。一句话能说明的关系在JavaScript中，使用起来并不容易。

从以下几个方面理解JavaScript的继承：

1. 类，对象和原型
2. 类的定义
3. 原型继承
4. 构造函数继承
5. 封装构造函数继承
6. 复制继承

<!-- more -->

### 类，对象和原型

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
如果不存在，才会查找这个object的prototype中是否存在属性x。

当我们new一个对象时，会给这个对象设置prototype属性，这个属性值来自它的构造函数的prototype属性值。
所有函数在定义时，就自动创建和初始化了prototype属性。这个prototype属性指向只包含constructor属性的对象，而constructor属性指向function本身。
因此，每一个object都会有一个constructor属性。

下面详细讲解以下类的定义的几种方式。

### 类的定义

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

### 原型继承

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

### 构造函数继承

众所周知，构造函数也是函数的一种，只是比普通函数特殊一点而已。
当我们把构造函数A的方法赋值给构造函数B，然后调用该方法，构造函数A在B的内部执行，这时，B的内部就拥有了构造函数A中定义的属性和方法。
这种B继承A的实现过程跟传统的类继承还是非常相似的。所以在一些书籍中，这样的继承也成为类继承。

请看下面的例子：

```
/* 构造函数A */
function A(x) {
    this.x = x;
    this.say = function(){
        alert(this.x);
    }
}

/* 构造函数B */
function B(x, y) {
    this.m = A;
    this.m(x);
    delete this.m;
    this.y = y;
    this.call = function(){
        alert(this.y);
    }
}

/* Tester */
var a = new A(1);
var b = new B(2, 3);
a.say();    // 1
b.say();    // 2 这里说明继承成功
b.call();   // 3
```

上面的写法是不是感觉很难受，需要定义一个临时的方法``m()``，构造函数参数x传递给构造函数A，执行后又把临时方法删除，实在是有种卸磨杀驴的味道。
将父类的构造函数绑定在子类的构造函数上，可以使用call或者apply方法，推荐下面的写法：

```
function B(x, y) {
    A.apply(this, arguments);
    this.x = x;
    this.y = y;
    this.call = function() {
        alert(this.y);
    }
}

```

即使这样优化后，还是欠妥，因为这样的构造函数继承中相互耦合性太高，不能适用复杂的编程中。

所以在构造函数继承中，引入prototype是很有必要的，请看下面的例子：

```
function A(x){
    this.x = x;
}

A.prototype.getx = function(){
    return this.x;
}

function B(x, y) {
    this.y = y;
    A.call(this, x);
}

B.prototype = new A();  // 这个时候 B.prototype.constructor指向了A
B.prototype.constructor = B;
B.prototype.gety = function() {
    return this.y;
}

var b = new B(2, 3);
alert(b.getx());
alert(b.gety());
```

在原型继承中，在为B类的原型赋值为A类的实例前，所以定义的任何属性和方法都将会被覆盖。
扩展B类的原型方法，要在原型绑定之后，再定义扩展方法。

### 封装构造函数继承

了解上面的构造函数继承后，我们可以定义一个形式如下的封装函数，帮助我们处理子类和超类的继承模式。

```JavaScript
function extend(Sub, Sup) { // Sub 子类，Sup 超类
    var F = function(){};    // 这是一个空函数
    F.prototype = Sup.prototype;
    Sub.prototype = new F();
    Sub.prototype.constructor = Sub;
    Sub.sup = Sup.prototype;
    /* 检测超类的原型构造器是否与Object的原型构造器发生耦合 */
    if(Sup.prototype.constructor == Object.prototype.constructor) {
        Sup.prototype.constructor = Sup;
    }
}
```

值得注意的是上面的函数输入是子类和超类对象，没有输出。空函数F的作用是中转作用，F的原型为超类的原型，F的实例作为子类的原型。
这样做的好处是如果超类自身规模较大，直接实例化超类占用内存较大，对系统负荷较大。

对于上面封装函数的实现，我们可以用下面的例子来测试一下：

```JavaScript
function A(x) {
    this.x = x;
    this.get = function() {
        return this.x;
    }
}

A.prototype.double = function(){
    return this.x * 2;
}
A.prototype.mul = function() {
    return this.x * this.x;
}

function B(x) {
    A.call(this, x);    // 内部数据绑定
}

extend(B, A);
var b = new B(2);
alert(b.get());     // 2
alert(b.double());  // 4
alert(b.mul());     // 4
B.prototype.double = function() {
    return this.x + '' + this.x;
}
alert(b.double());     // '22' 字符串
```

B类新定义的add方法会覆盖A类的同名方法。在B类的原型方法double()中可以调用A类的原型方法double()，从而避免代码耦合。

```
B.prototype.double = function(){
    return B.sup.double.call(this);     // 内部调用
}
```

### 复制继承

之所以提一下复制继承，是因为这是继承的最原始的方法，它的思路也非常清晰：利用for in去遍历对象属性赋值给新的对象，
这种思路常常让我想起作业抄袭答案的场景。下面封装一个复制继承的函数：

```JavaScript
function extend(p, copy) {
    var copy = copy || {};
    for(var i in p) {
        if((typeof p[i]).toString().toLowerCase() === 'object') {
            copy[i] = (p[i].constructor === Array) ? []: {};
            extend(p[i], copy[i]);
        } else {
            copy[i] = p[i];
        }
    }
    return copy;
}
```

也有人说这样的拷贝思路是深拷贝，因为他对数组和对象进行了递归处理。
jQuery貌似也是用这种方式实现继承方法的。

针对上面的继承函数，下面简单测试一下：

```
var A = {
    name: 'A'
}

A.arr = [1, 2]; // 这个要写在extend()前面
var B = extend(A);
B.arr.push(3)
alert(A.arr);   // 1,2
alert(B.arr);   // 1,2,3
```

在另外一本书上我看到了这样的封装方法，仅供参考：

```JavaScript
FUnction.prototype.extend = function(o){
    for(var i in o) {
        this.constructor.prototype[i] = o[i];
    }
}

/* Test */
function F(x, y) {
    this.x = x;
    this.y = y;
    this.add = function() {
        return this.x + this.y;
    }
}
F.prototype.mul = function() {
    return this.x * this.y;
}

var f = new F(2, 3);
var o = function(){};
o.extend(new F(2, 3));
```

这段代码其实是通过反射机制，复制类对象的所有可枚举的属性和方法来模拟继承。对这段代码可以进行优化如下：

```JavaScript
Function.prototype.clone = function(o) {
    function Temp() {};
    Temp.prototype = o;
    return new Temp();
}

var o = Function.clone(new F(2, 3));
alert(o.x);         // 2
alert(o.y);         // 3
alert(o.add());     // 5
alert(o.mul());     // 6
```

### 小结

对JavaScript的语言来说，要了解的东西还是很多，继承只是其中冰山一角。
JavaScript的继承尽管多样化，相比较Java而言还是优雅而简洁的（不忘记黑一下Java）。
希望这篇文章能够对你有所帮助，写了很多废话，如果有什么不当的地方，请务必告诉我，非常感谢！


### 参考资料

1. [编写高质量代码](http://book.douban.com/subject/20366358/)
2. [Classical Inheritance in JavaScript](http://www.crockford.com/javascript/inheritance.html)
3. [阮一峰的网络日志](http://www.ruanyifeng.com/blog/2010/05/object-oriented_javascript_inheritance_continued.html)
