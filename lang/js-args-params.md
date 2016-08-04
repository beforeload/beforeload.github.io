# Arguments & Parameters in ES6

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

 Chrome  Firefox Internet Explorer Microsoft Edge  Opera Safari
 49  15  – 14  – –


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

这种行为是当我们需要的参数已经明确。如果我们想让参数可选呢？为了避免参数缺少
### Passing Arguments

### Mandatory Arguments


### The Length Property


### The Callee And Caller Properties


### Conclusion


[1]: https://github.com/petkaantonov/bluebird/wiki/Optimization-killers#3-managing-arguments "Optimization-killers"


1. [Truthy](https://developer.mozilla.org/en-US/docs/Glossary/Truthy)