# 代码拆分

### 定义一个分割点

AMD 和 CMD规范不同，定义不同的方法按需加载。

__CommonJS__

`require.ensure(dependencies, callback)` 只加载模块，不运行

例子：

```js
require.ensure(["module-a", "module-b"], function() {
  var a = require("module-a");
  // ...
});
```

__AMD__

`require(dependencies, callback)`

```js
require(["module-a", "module-b"], function(a, b) {
  // ...
});
```

注意，在`webpack`模块中，是按照从左到右顺序执行的。callback可以删除。

__ES6 modules__

webpack1.x.x 原生不支持，2.0.0开始支持。可以通过`babel`转换，将 `import` 语法转换成`CommonJS`或者`AMD`规范的模块。
不能动态加载。

幸运的是已经明确了JavaScript API的`loader`规范，来解决动态加载的问题：`System.load`或者 `System.import`。
这个API和`require`变量等价。但是大多数转换器不支持将`System.load`转成`require.ensure`，所以还需要手动指定。

```js
//static imports
import _ from 'lodash'

// dynamic imports
require.ensure([], function() {
  let contacts = require('./contacts')
})
```


### app 代码分割

将应用分割成两个文件 `app.js`和`vendor.js`。定义的vendor 变量传给`CommonsChunkPlugin`。

```js
var webpack = require("webpack");

module.exports = {
  entry: {
    app: "./app.js",
    vendor: ["jquery", "underscore", ...],
  },
  output: {
    filename: "bundle.js"
  },
  plugins: [
    new webpack.optimize.CommonsChunkPlugin(/* chunkName= */"vendor", /* filename= */"vendor.bundle.js")
  ]
};
```

这一步把 vendor 代码块从app 代码块中分离，生成的 bundle.js 只包含自己应用的代码，不含有其他依赖。这些外部依赖的代码都在
`vendor.bundle.js`文件中。

在HTML中，需要在`bundle.js`之前先加载 `vendor.bundle.js`。

### 多入口模块

```js
var webpack = require("webpack");
module.exports = {
  entry: { a: "./a", b: "./b" },
  output: { filename: "[name].js" },
  plugins: [ new webpack.optimize.CommonsChunkPlugin("init.js") ]
}
```

HTML中

```html
<script src="init.js"></script>
<script src="a.js"></script>
<script src="b.js"></script>
```

### 优化插件

* `LimitChunkCountPlugin`
* `MinChunkSizePlugin`
* `AggressiveMergingPlugin`

[插件地址](http://webpack.github.io/docs/list-of-plugins.html)