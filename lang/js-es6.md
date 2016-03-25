# JavaScript ES6概要

### Class and Module

1. Class 之间通过extends 实现继承
2. super()方法可以调用父类的构造函数，等同于super.constructor()
3. export 和import 之间可以用变量的解构赋值对应
4. export default 为模块默认方法，唯一性，import导入时可以指定任意名称
5. export * from 'module'，输出``module``模块所有属性和方法，继承 ``module`` 模块
6. module xxx from 'xxx' 模块的整体导入

### Promise

未来将要发生的事件（异步操作）

```
var promise = new Promise(function(resolve, reject) {
    // 处理异步操作 setTimeout，AJAX等
    if(/* 异步操作成功 */) {
      resolve(value)
    } else {
      reject(value)
    }
})

promise.then(function(value) {
    //success
}, function(value) {
    //failure
})

```

1. Promise 构造函数参数为一个函数，函数中两个参数分别为resolve方法和reject方法
2. 链式操作 前一个then方法中的回调函数的返回值是后一个then方法中回调函数的参数
3. catch方法捕捉错误时，错误对象具有“冒泡”性质，会一直向后传递
4. Promise.all() 方法用于将多个异步操作包装成一个新的Promise 对象
5. Promise.resolve() 方法可以讲现有对象（例如jQuery的deferred对象）转化成Promise对象
6. async 函数取代回调函数，配合 await 关键字

```
function timeout(ms) {
  return new Promise((resolve) => {
    setTimeout(resolve, ms)
  })
}
async function asyncValue(value) {
  await timeout(40) // 程序遇到await先返回，触发异步操作完成后再继续执行
  return value
}

```


### Generator

```
function* generator() {
  yield 'hello world'
  yield* func() // func也是generator函数
  yield 'end'
}

var hw = generator()
hw.next()

```

1. 内部状态遍历器，每次遍历后，状态发生改变
2. 通过next 方法可以遍历，next方法的参数为上一条yield语句的返回值
3. yield 改写回调函数，yield语句是同步运行
4. 实际操作中，一般让yield返回Promise对象（例如使用函数库Q）
5. for...of 循环可以遍历 Generator函数
6. yield* 表示返回一个遍历器


### Iterator（遍历器）

1. 一个对象制药部署了next方法，就被视为具有iterator接口，可以用for...of遍历
2. 数组原生具备iterator接口
3. for...in 遍历键名，for...of遍历键值 for(var [name, value] of arraySet)
4. for...of循环可以遍历Set和Map结构，字符串，DOM NodeList对象等


### Set 和 Map

1. var s = new Set()
2. Set数据结构的成员值唯一，可以用来数组去重
3. Set参数可以为数组，不会类型转换（5和'5'是不同的）
4. Set.prototype.constructor 构造函数，Set.prototype.size 成员总数
5. add, delete, has, clear方法
6. Array.from(set) 将set结构转换为数组
7. Map对象可以用DOM节点作为对象，不限于字符串
8. Map可以通过传入数组参数进行初始化 new Map(['name', '元千'])
9. 属性和方法 size, set(key, value), get(key), has(key), delete(key), clear()
10. keys(), values(), entries() 遍历
11. WeakMap 只接受对象作为键名（null除外），键对应得对象可能在将来会消失，避免内存泄漏

### 函数

1. 允许设置参数默认值(可选参数) ``function abc(x = 0, y = 1) {}``
2. rest参数（...变量名），用于获取对于参数，变量是一个数组
3. 扩展运算符（...) 将一个数组转换成逗号分隔的参数序列
4. 箭头“＝>”定义函数 ``var f = v => v;`` 返回对象需要加括号

### 对象

1. Object.is() 和 ‘===’ 相似。区别： ``Object.is(+0, -0)``为false，``Object.is(NaN, NaN)``为true
2. Object.assign() 复制源对象的所有可枚举属性到目标对象上
3. __proto__ 属性用来读取当前对象的prototype对象
4. Object.setPrototypeOf() 设置一个对象的prototype对象 Object.getPrototypeOf() 读取
5. 允许直接写入变量和函数作为对象的属性和方法
6. 属性名可以为表达式，表达式需要放在方括号内 ``var a = { [val]: 'value'}``
7. Symbol 接收字符串作为参数，生成的每个symbol都是不等的，Symbol函数不能使用new
8. Symbol类型作为属性名，通过Object.getOwnPropertySymbols() 和 Object.getOwnPropertyKeys()获取属性
9. Proxy 构造函数生成proxy实例对象，两个参数：代理的目标对象，拦截函数（get方法）
10. Object.observe() 监听对象变化：add、update、delete、setPrototype、reconfigure（attrbutes发生变化）、preventExtensions（不可扩展）
11. Object.unobserve() 取消监听，Object.observe 接受第三个参数，指定监听事件类型的种类

```
var a = {};
var mySymbol = Symbol();
a[mySymbol] = 'Hello';

// 等价写法
Object.defineProperty(a, mySymbol, {value: 'Hello' })

```

### 数组

1. Array.from() 将类数组对象（array-like object）和可遍历（iterable）对象转换成数组
  * ``Array.from(arrayLike, x => x * x)``
2. Array.of() 将一组数值转换为数组
3. find() 和 findIndex() 参数是回调函数，可以发现NaN
4. fill() 填充一个数组，参数二和三用于指定填充的始末位置
5. entries()、keys()和values() 遍历数组
6. for...of结构结合if语句进行数组和字符串推导 ``[for (year of years) if (year > 2000) year]``
7. Array.observe() 和 Array.unobserve() 取消监听，变化种类：add、update、delete、splice

### 数值

1. 前缀0b、0o分别表示二进制和八进制
2. isFinite()和isNaN()__进行类型转换__ 区别于Number.isFinite() 和 Number.isNaN()
3. parseInt和parseFloat方法移植到Number对象上
4. Number.isInteger() 判断是否为整数（3和3.0是同一个值），Number.isSafeInteger()判断安全整数
5. Number.MIN_SAFE_INTEGER 和 Number.MAX_SAFE_INTEGER 表示-2^53 和 2^53。
6. Math 对象扩展
  * Math.trunc() 去除小数部分
  * Math.expm1(x) 返回 e^x - 1
  * 其他数学方法

### 字符串

1. codePointAt(index)  能够正确处理4个字节存储的字符，返回Unicode 编号
2. String.fromCodePoint() 从Unicode 编号返回字符串
3. \u 加大括号可以表示超过0xFFFF数值的字符 "\u{20BB7} "
4. 正则表达式 u 修饰符处理大于0xFFFF的Unicode字符
5. contains()、startsWith()、endsWith() 返回布尔值
6. repeat(n) 重复 n 次 `` 'x',repeat(3) // "xxx" ``
7. 正则表达式 y 修饰符，从上一次匹配成功位置开始往下匹配
8. 模板字符串用反引号（\`)标识，可以定义多行字符串或者嵌入变量 `Hello ${name}`

### 变量

1. let声明变量只在块级作用域有效，不允许重复声明
2. ES6规定函数本身作用域在其所在的块级作用域之内
3. const 声明常量，作用域和let命令相同
4. 解构：从数组和对象中按照一定模式提取值对变量进行赋值
  * 数组 ``var [a, b, c] = [1, 2, 3]``
  * 对象 ``var { bar, foo } = { foo: "aaa", bar: "bbb"}``
  * 嵌套 ``var { p: [x, {y}]} = { p: ["Hello", { y: "World"}]}``
  * 交换 ``[x, y] = [y, x]``
