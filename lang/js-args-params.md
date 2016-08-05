# ES6 中的实参与形参

ECMAScript 6 (or ECMAScript 2015) 标准中对参数引入了一些新的特性：剩余参数(rest parameters)，函数参数默认值，解构等。

### Arguments Parameters 对比

Arguments 和 Parameters 很容易混淆，但还是有区别的。在很多标准中，parameters（形参）是指函数声明中的，而 arguments（实参）是传递给函数使用的。举个栗子🌰：

```javascript
function foo(params1, param2) {
  // do sth
}

foo(1, 2)

```

如上函数，params1、params2 是形参，1、2 是实参。

### Spread Operator (…)

ES5中，给函数传一个参数数组常用方法是`apply()`。很多方法并不支持数组的传参，例如`Math.max()`方法。
在ES5中，我们想要得到数组元素中的最大值，可以这么写：

```javascript
var arr = [5, 10, 15];
Math.max(arr) // Error: NaN
Math.max.apply(Math, arr); // 15

```

像`Math.max()`这样不支持数组，只接受数字参数的方法还有很多。当传入数组参数时，它会抛出一个Error，但是可以用
`apply()` 方法，传入的数组会被解析成一个一个独立的数字，`Math.max()`可以正确的运行。

在ES6中，引入了一个展开运算符(...)。于是，不再需要`apply()`方法，就可以很容易的将表达式展开成多个实参。

```javascript
var arr = [5, 10, 15];
Math.max(...arr); // 15

```

这个地方，展开运算符把数组 `arr` 扩展成一个个独立的值，提供给函数调用。虽然可以使用`apply()` 模拟展开运算符，但是语法容易混淆，不如展开运算符灵活。展开运算符不仅使用方便，在函数调用时，
还可以多次使用，并混入其他实参，举例如下：

```javascript
function myFunc() {
  for(var i in arguments) {
    console.log(arguments[i]);
  }
}

var params = [10, 15];
myFunc(5, ...params, 20, ...[25]);  // 5 10 15 20 25

```

展开运算符的另一大优势是，很容易用于构造函数，🌰如下：

```javascript
new Date(...[2016, 8, 3]); // Sat Sep 03 2016 00:00:00 GMT+0800 (CST)

```

当然，在ES5中，可以使用前面的代码重现实现，但是可能需要使用一个比较复杂的模式来避免类型错误。代码如下：

```javascript
new Date.apply(null, [2016, 4, 24])
// Uncaught TypeError: Date.apply is not a constructor(…)(anonymous function)

new (Function.prototype.bind.apply(Date, [null].concat([2016, 8, 3])));
// Sat Sep 03 2016 00:00:00 GMT+0800 (CST)
```

##### 展开运算符在函数调用中浏览器支持情况

__桌面浏览器__

| CHROME | FIREFOX | INTERNET EXPLORER | MICROSOFT EDGE | OPERA | SAFARI |
|:---:|:---:|:---:|:---:|:---:|:---:|
| 46 | 27 | – | Supported | – | 7.1 |

__Mobile 浏览器__

| CHROME FOR ANDROID | FIREFOX MOBILE | SAFARI MOBILE | OPERA MOBILE | IE MOBILE |
|:---:|:---:|:---:|:---:|:---:|:---:|
| 46 | 27 | 8 | – | – |

### Rest Parameters

剩余参数的语法和展开运算符语法相同，但是不再试展开数组传给形参。它将所有的形参转化成一个数组。

```javascript
function testRestParams(...options) {
  return options;
}

testRestParams('a', 'b', 'c');
// ["a", "b", "c"]

```

如果没有实参，剩余参数会被设置成一个空数组。

```javascript

function testRestParams(...options) {
  return options;
}

testRestParams(); // []

```

在创建一个可变参数函数（接受变长实参的函数）时，剩余参数相当有用。它有数组的优势，可以替代`arguments`对象。
在ES5中，操作参数需要这么写：

```javascript
function checkSubstrings(string) {
  for(var i = 1; i < arguments.length; i++) {
    if(string.indexOf(arguments[i]) === -1) {
      return false;
    }
  }
  return true;
}

checkSubstrings('this is a string', 'is', 'this');  // true

```

上述方法检验一个字符串是否包含一个子串。首先，我们必须查看函数体里是否接受多个实参，其次迭代必须从 1 开始，而不是从 0 开始。
因为`arguments[0]`指向的是第一个实参。如果我们后面在字符串之前或之后添加一个参数，有可能会忘记更新内部的循环。
使用剩余参数可以很容易的避免这些问题。

```javascript
function checkSubstrings(string, ...keys) {
  for(var key of keys) {
    if(string.indexOf(key) === -1) {
      return false;
    }
  }
  return true;
}

checkSubstrings('this is a string', 'is', 'this');  // true

```

这两种方法的实现结果相同，不同的是剩余参数是将剩余的实参放到一个数组中，赋值给变量`keys`。

使用剩余参数取代`arguments`对象，提升了代码的可读性，同时避免了`arguments`导致JavaScript中的函数无法优化的[问题][1]。
然而剩余参数不是万能的，它必须放在最后一个参数，否则会报一个语法错误。

```javascript
function logArguments(a, ...params, b) {
        console.log(a, params, b);
}
logArguments(5, 10, 15);
// Uncaught SyntaxError: Rest parameter must be last formal parameter
```

另外一个限制就是剩余参数在函数声明中只允许出现一次。

```javascript
function logArguments(...params1, ...params2) {}

logArguments(5, 10, 15);
// Uncaught SyntaxError: Rest parameter must be last formal parameter

```

##### 剩余参数浏览器支持情况

__桌面浏览器__

| CHROME | FIREFOX | INTERNET EXPLORER | MICROSOFT EDGE | OPERA | SAFARI |
|:---:|:---:|:---:|:---:|:---:|:---:|
| 47 | 15 | – | Supported | 34 | – |


__移动端浏览器__

| CHROME FOR ANDROID | FIREFOX MOBILE | SAFARI MOBILE | OPERA MOBILE | IE MOBILE |
|:---:|:---:|:---:|:---:|:---:|:---:|
| 47 | 15 | - | – | – |

### Default Parameters

##### ES5 中的默认参数

ES5中，其实是不支持默认参数的，只是有一种很容易的解决方法。在函数内使用或操作(||)，可以很方便的模拟默认参数。

```javascript
function foo(param1, param2) {
  param1 = param1 || 10;
  param2 = param2 || 10;
}

foo(5, 5); // 5 5
foo(5);    // 5 10
foo(10);    // 10 10
```

这个函数接受两个两个参数。如果调用时没有传参，它会使用默认值。在函数内部，没有实参会自动设置成`undefined`,
因此，我们可以使用逻辑或操作符(`||`)，检测缺少实参并设置默认值。或操作符监测第一个参数，如果为真，直接返回，如果为false,
返回第二个参数。

这种方法在函数中非常常用，但是他有一个缺陷。传入 `0` 或者 `null` 等参数会返回默认值。因为`0, "", null, undefined, and NaN` 都会被判为false。如果我们实际上需要传入`0` 或 `null` 给函数，可能需要另一个方式检测是否缺少实参。

```
function foo(param1, param2) {
  if(param1 === undefined) {
    param1 = 10;
  }
  if(param2 === undefined) {
    param2 = 10;
  }
}

foo(0, null); // 0, null
foo();  // 10, 10
```

在函数内部，检测传入实参的类型，确保它们是 `undefined`，再赋值默认值。这样的判断需要一点代码量，不过对于传入 `0` 和 `null` 很安全。

##### ES6 中的默认参数

在 ES6 中，不再需要检测 `undefined` 值来模拟默认形参。可以直接把默认值放到函数声明中：

```javascript
function foo(a = 10, b = 10) {
  console.log(a, b);
}

foo(5); // 5 10
foo(0, null); // 0 null
```

如上所示，遗漏实参会触发默认值，但是传入 `0` 或 `null` 不会。甚至可以使用函数查找默认值来供给默认参数。如下：

```javascript
function getParam() {
  console.log('getParam was called');
  return 3;
}

function multiply(param1, para2 = getParam()) {
  return param1 * param2;
}

multiply(2, 5); // 10
multiply(2);    // getParam was called 6

```

注意到 `getParam` 函数只有在第二个实参缺失时，才会调用，当我们使用两个参数调用`multiply()`函数时，不会在控制台输出｀getParam was called｀。

默认参数还有一个非常有趣的特性，我们可以在函数调用中，引用其他参数和值。

```javascript
function defaultParams(a = 10, b = a) {
  console.log('a = ' + a + '; b = ' + b);
}

defaultParams();    // a = 10; b = 10
defaultParams(22);  // a = 22; b = 22
defaultParams(2, 4);  // a = 2; b = 4
```

甚至还可以在函数调用中进行运算操作。

```javascript
function foo(a, b = ++a, c = a * b) {
  console.log(a, b, c);
}

foo(5); // 6 6 36
```

注意，和其他语言不同的是，JavaScript 判断默认值是在函数调用时。

```javascript
function add(value, array = []) {
  array.push(value);
  return array;
}

add(5); // [5]
add(6); // [6], not [5, 6]
```

##### 默认参数浏览器支持情况

__桌面浏览器__

| FEATURE | CHROME | FIREFOX | INTERNET EXPLORER | MICROSOFT EDGE | OPERA | SAFARI |
|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| Basic support | 49 | 15 | – | 14 | - | – |
| Parameters without defaults after default parameter | 49 | 26 | – | 14 | – | – |


__移动端浏览器__

| FEATURE | CHROME FOR ANDROID | FIREFOX MOBILE | SAFARI MOBILE | OPERA MOBILE | IE MOBILE |
|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| Basic support | 49 | 15 | - | – | – |
| Parameters without defaults after default parameter | 46 | 26 | – | – | – |

### Destructuring

解构是 ES6 中的新特性，主要用来将数组或者对象中的值提取出来，赋值给语法类似于对象或者数组常量的变量。解构的语法简单易于理解，在给函数传参过程中相当有用。

在ES5中，处理大量可选参数，特别是对象属性无序时，通常使用配置管理对象的方式。
如下函数：

```javascript
function initiateTransfer(options) {
    var protocol = options.protocol,
        port = options.port,
        delay = options.delay,
        retries = options.retries,
        timeout = options.timeout,
        log = options.log;
    // code to initiate transfer
}
options = {
  protocol: 'http',
  port: 800,
  delay: 150,
  retries: 10,
  timeout: 500,
  log: true
};
initiateTransfer(options);
```

对于 JavaScript 开发者而言，这种方式很常见，且非常有用。但是还是必须检查函数体内部，检验形参是否符合我们预期。
使用解构形参的方式，我们在函数声明中，可以很清晰的指明形参。使用方式如下：

```javascript
function initiateTransfer({protocol, port, delay, retries, timeout, log}) {
     // code to initiate transfer
};
var options = {
  protocol: 'http',
  port: 800,
  delay: 150,
  retries: 10,
  timeout: 500,
  log: true
}
initiateTransfer(options);
```

如上方法，我们使用了对象解构的方式，取代配置管理对象的方式，使得代码简洁，便于阅读。

我们还可以把代码解构和常见传参相结合：

```javascript
function initiateTransfer(param1, {protocol, port, delay, retries, timeout, log}) {
     // code to initiate transfer
}
initiateTransfer('some value', options);

```

注意，如果在函数调用中省略了参数，会报类型错误。

```javascript
({protocol, port, delay, retries, timeout, log}) {
     // code to initiate transfer
}
initiateTransfer();
// Uncaught TypeError: Cannot match against 'undefined' or 'null'.
```

这种行为是当我们需要的参数已经明确。如果想让参数可选呢？为了避免缺少参数引入错误，这就需要给解构参数提供一个默认值：

```javascript
function foo({a, b, c} = {}) {
  // TODO
}

a(); // no error
```

一个空对象作为默认值提供给解构参数，这样函数调用时如果没有任何形参，也不会有任何错误。

我们也可以给解构参数的每一项提供一个默认值：

```javascript
function destructuredParams({
  a = 'abc',
  b = 1234,
  c = true
}) {
  // TODO
}

```

这个例子中，每个属性有一个默认参数，消除了我们人工检查`undefined`参数的需求，并在函数体内部拥有了默认值。

##### 解构浏览器支持情况

__桌面浏览器__

| FEATURE | CHROME | FIREFOX | INTERNET EXPLORER | MICROSOFT EDGE | OPERA | SAFARI |
|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| Basic support | 49 | 2.0 | – | 14 | - | 7.1 |
| Parameters without defaults after default parameter | 49 | 47 | – | 14 | – | – |


__移动端浏览器__

| FEATURE | CHROME FOR ANDROID | FIREFOX MOBILE | SAFARI MOBILE | OPERA MOBILE | IE MOBILE |
|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| Basic support | 49 | 1 | 8 | – | – |
| Parameters without defaults after default parameter | 49 | 47 | – | – | – |

参考链接

### Passing Arguments

JavaScript 中只有两种函数传参方式：引用传递和值传递。引用传参的参数修改会影响全局，而值传递的参数修改只影响函数体内部。

有一些语言，如VB，PowerShell，是可以选择参数使用值传递还是引用传递，JavaScript没有这样的选项。

##### 值传递

JavaScript 可以只传递值。当传入实参的值给函数，在函数作用域内，参数的值会创建一份拷贝，任何对这些值的改变只会在函数作用域内生效。如下所示：

```javascript
var a = 5;
function increment(a) {
  a = ++a;
  console.log(a);
}

increment(a); // 6
console.log(a); // 5;
```

这里在函数内部修改参数的值，不会影响函数外部原来的值。因此，如果变量在函数外部打印，它的值仍然是`5`。

##### 引用传递

其实可以这么理解，在 JavaScript 中，所有的传递都是通过值的方式。当我们传递的是一个指向对象或数组的变量，这个“值”其实就是引用对象的指针。通过变量修改其引用对象的属性，原先的对象也会随之改变。如下方法：

```javascript
function foo(param) {
  param.bar = 'new value';
}

obj = { bar: 'value' };

console.log(obj.bar); // value
foo(obj);
console.log(obj.bar); // new value
```

如上所示，`param`对象属性在函数内部发生改变，在函数体外部，也能看到修改的值。

当传递的参数不是原始的值，例如对象会数组，在这种场景下，会创建一个变量指向原来的对象在内存中的地址。这个变量会被传递给函数，修改它会影响原来的对象。

### 类型检测和缺失或扩展参数

在一个强类型的语言中，在函数声明中必须设定参数的类型。但是在 JavaScript 中，缺少这样的特性。在 JavaScript 中，数据的类型和传参的个数都比较随意。

假设有个方法只接受一个参数。当调用这个方法时，没有限制只传递一个参数给这个方法。可以传递一个，两个甚至多个参数。更加令人发指的是我们可以选择不传递任何参数，并且不会报错。

实参和形参的个数不同主要有以下两种情况：

* __实参少于形参__：缺少的参数会被当作 `undefined`
* __实参多余形参__：多余的形参会被忽略掉，但是可以通过类数组的变量`arguments`检索到。

### 强制性参数

如果在函数调用中，一个实参遗漏了，它会被设置成 `undefined`。我们可以利用这个行为，当一个实参遗漏时抛出一个错误。

```javascript
function foo(mandatory, optional) {
  if(mandatory === undefined) {
    throw new Error('Missing parameter: mandatory');
  }
}
```

在 ES6 中，可以使用默认参数的方法设置强制性的参数。

```javascript
function throwError() {
  throw new Error('Missing parameter');
}

function foo(param1 = throwError(), param2 = throwError()) {
  // TODO
}

foo(10, 20); // okay
foo(10);     // Uncaught Error: Missing parameter
```

### 实参对象

对于剩余参数的支持，早在 ES4 中，就有意向替换 `arguments` 对象，但是 ES4 标准并没有实现它。随着 ES6 的发布，JavaScript官方支持剩余参数。同时拒绝了放弃支持 `arguments` 对象的提案。

`arguments` 是一个类数组的对象，存在于所有的函数方法中。通过类似于数组下标的方法可以获取 `arguments` 里的值。这个对象允许我们传递任意个数的实参给函数。

```javascript
funciton checkParams(param1) {
  console.log(param1);  // 2
  console.log(arguments[0], arguments[1]);  // 2 3
  console.log(param1 + arguments[0]); // 2 + 2
}

checkParams(2, 3);
```

这个方法期望只接受一个实参。当我们调用这个方法时传入两个参数。第一个参数可以用形参 `param1` 或 `arguments[0]` 获取，但第二个参数只能用 `arugments[1]` 获取。注意 `arguments` 对象只能结合 `arguments`名字一起使用。

对于所有传递给函数的实参，`arguments` 都有一个入口，第一个入口的索引值从 `0` 开始。我们可以通过 `arguments[2]`，`arguments[3]` 等等获取更多实参。

我们甚至可以跳过设置形参，直接使用 `arguments` 对象：

```javascript
function checkParams() {
  console.log(arguments[1], arguments[0], arguments[2]);
}

checkParams(2, 4, 6); // 4 2 6
```

实际上，命名形参是因为使用方便，不是必要的。与之类似的，剩余参数也可以用来代表传入的参数：

```javascript
function checkParams(...params) {
  console.log(params[1], params[0], params[2]); // 4 2 6
  console.log(arguments[1], arguments[0], arguments[2]); // 4 2 6
}

checkParams(2, 4, 6);
```

`arguments` 对象是一个类数组（并不是数组）的对象。它缺少数组的方法，例如`slice()`和`foreach()`。
为了在 `arguments` 对象上使用数组方法。首先我们需要做的是转成真实的数组：

```javascript
function sort() {
  var a = Array.prototype.slice.call(arguments);
  return a.sort();
}

sort(40, 20, 50, 30); // [20, 30, 40, 50]
```

在这个方法中，`Array.prototype.slice.call()` 用来快速转化 `arguments` 对象成一个数组。
`sort()` 方法对数组中的元素进行排序后返回。

ES6 中有更直接的方法：`Array.from()` 是 ES6 中新加的方法，用来从任何类数组的对象中创建一个新的数组。使用如下：

```javascript
function sort() {
  var a = Array.from(arguments);
  return a.sort();
}

sort(40, 20, 50, 30); // [20, 30, 40, 50]
```

### The Length Property

尽管 `arguments` 对象不是一个典型的数组，但是它有`length`属性，可以用来检测传入参数的多少。

```javascript
function countArguments() {
  console.log(arguments.length);
}

countArguments(); // 0
countArguments(10, null, "string"); // 3
```

使用 `length` 属性，我们可以更好的控制传入实参的数量。如果一个方法需要两个实参才能生效，就可以使用 `length` 属性来检测传入实参的个数。如果少于期望的参数个数，可以抛出错误：

```javascript
function foo(param1, param2) {
  if(arguments.length < 2) {
    throw new Error('This function expects at least two arguments');
  } else if(arguments.length === 2) {
    // TODO
  }
}
```

剩余参数其实是数组，因此也有 `length` 属性。在 ES6 中，前面的代码可以用剩余参数的方法改写：

```javascript
function foo(...params) {
  if(params.length < 2) {
    throw new Error('This function expects at least two arguments');
  } else if(params.length === 2) {
    // TODO
  }
}

```

### The Callee And Caller Properties

`callee` 属性指向当前正在运行的函数，而 `caller` 指向调用当前正在运行函数的函数方法。
在 ES5 的严格模式下，这些属性被废弃了，试图获取这些属性会报类型错误。

`arguments.callee` 属性在递归调用函数很有用，特别当函数名不存在（匿名函数）时。因为匿名函数没有名称，只能通过 `arguments.callee` 指向它。

```javascript
var result = (function(n) {
  if( n <= 1) {
    return 1;
  } else {
    return n * arguments.callee(n - 1);
  }
})(4); // 24
```

### 严格模式和非严格模式下的 Arguments 对象

在 ES5 非严格模式下，`arguments` 对象有个不常见的特性：它保持它的值和对应参数名的值同步。

```javascript
function foo(param) {
  console.log(param === arguments[0]); // true
  arguments[0] = 500;
  console.log(param === arguments[0]); // true
  return param
}

foo(200); // 500
```

在函数内部，一个新的值赋值给了 `arguments[0]`。因为`arguments` 的值永远和对应参数名的值同步，修改 `arguments[0]` 的值也会修改 `param` 的值。实际上，他们像同一个变量的两个不同的名字。在 ES5 严格模式下，`arguments` 对象这些混淆的行为已经被移除了。

```javascript
"use strict";
function foo(param) {
  console.log(param === arguments[0]);  // ture
  arguments[0] = 500;
  console.log(param === arguments[0]);  // false
  return param;
}

foo(200); // 200
```

这下，修改 `arguments[0]` 不会影响 `param`，输出结果也符合期望。在 ES6 中这个函数的输出和 ES5 的严格模式一样。记住，在函数声明中使用默认值，`arguments` 对象不会被影响。

```javascript
function foo(param1, param2 = 10, param3 = 20) {
  console.log(param1 === arguments[0]); // ture
  console.log(param2 === arguments[1]); // true
  console.log(param3 === arguments[2]); // false
  console.log(arguments[2]);            // undefined
  console.log(param3);                  // 20
}
```

在这个函数中，由于只有两个实参传入，即使 `param3` 有一个默认值，它也不等于 `arguments[2]`。换句话说，设置默认值不会影响 `arguments` 对象。

### Conclusion

ES6提供了超级多的新的特性，给 JavaScript 带来显著的提升。越来越多的开发者使用ES6的特性，并且难以避免的使用到它们。这篇博文只介绍 ES6 中参数处理的相关特性，还比较肤浅，更多功能特性等待挖掘。

##### 参考链接

1. [Truthy](https://developer.mozilla.org/en-US/docs/Glossary/Truthy)
2. [ECMAScript 6 Compatibility Table](https://kangax.github.io/compat-table/es6/)
3. [ECMAScript 2015 Language Specification](http://www.ecma-international.org/ecma-262/6.0/)
4. [How To Use Arguments And Parameters In ECMAScript 6](https://www.smashingmagazine.com/2016/07/how-to-use-arguments-and-parameters-in-ecmascript-6/)

[1]: https://github.com/petkaantonov/bluebird/wiki/Optimization-killers#3-managing-arguments "Optimization-killers"
