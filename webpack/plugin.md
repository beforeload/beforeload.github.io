# plugin

插件分为内置插件和第三方插件，涉及到项目打包的各个阶段。

在项目打包中，对配置、输出、优化、模块规范、依赖注入、国际化、调试等方面，`webpack` 都拥有组件进行功能扩展和支持。

### 插件的使用

```javascript
var webpack = require("webpack");
var HtmlWebpackPlugin = require('html-webpack-plugin');
module.exports = {
    plugins: [
      new webpack.DefinePlugin({
        'process.env': {
          'NODE_ENV': JSON.stringify('production')
        },
        'CDN': JSON.stringify('//ytk.fbcontent.cn'),
        'GALLERY_URL': JSON.stringify('//gallery.yuanfudao.com'),
        'GALLERY_CDN': JSON.stringify('//gallery.fbcontent.cn'),
        'IMG_URL': JSON.stringify('/api/ape/images/'),
        'USER_IMG_URL': JSON.stringify('/api/ape/user-images/'),
        'STATIC_URL': JSON.stringify('/tiku/homework/dist/')
      }),
      new HtmlWebpackPlugin({
        title: 'Custom template',
        template: 'tpl/index.html', // Load a custom template
        inject: 'body' // Inject all scripts into the body
      })
    ]
};
```

[插件列表](http://webpack.github.io/docs/list-of-plugins.html)

### 插件运行原理

`webpack` 中的很多对象继承`Tapable`类，都会暴露一个 `plugin` 方法。
__`webpack` 的插件通过 `plugin` 方法注入构建的各个步骤。__
常用的有 `compiler.plugin` 和 `compilation.plugin`。
基本上在整个构建流程每个步骤中，插件的调用都绑定了一个回调函数。

组件只会在 `webpack` 启动时安装或者说加载一次。
__`webpack` 通过 `apply` 方法加载一个组件。__
组件的引用传给 `webpack` 的 `compiler` 对象。
开发者可以通过调用 `compiler.plugin` ，注入编译的各个步骤。

```javascript
function MyPlugin(options) {
  // Configure your plugin with options...
}

MyPlugin.prototype.apply = function(compiler) {
  compiler.plugin("compile", function(params) {
    console.log("The compiler is starting to compile...");
  });

  compiler.plugin("compilation", function(compilation) {
    console.log("The compiler is starting a new compilation...");

    compilation.plugin("optimize", function() {
      console.log("The compilation is starting to optimize files...");
    });
  });

  compiler.plugin("emit", function(compilation, callback) {
    console.log("The compilation is going to emit files...");
    callback();
  });
};

module.exports = MyPlugin;
```

_两种类型的插件接口_

1. 基于时间戳
  * 同步(默认) 使用 `return`
  * 异步 最后一个参数是回调，签名`function(err, result)`
  * 并行 处理函数也是并行的
2. 基于返回值
  * not bailing(默认) 没有返回值
  * bailing 处理函数按序返回
  * parallel bailing 处理函数并行调用（异步方式），按照顺序，重要的是第一个返回值
  * waterfall 每个处理函数拿上一个处理函数的结果作为参数

__Compiler实例__

`webpack` 插件需要在原型链上定义 `apply` 方法，才有权限调用所有的 `compiler` 的实例方法：

* run(compiler: Compiler) 异步方法，启动编译过程
* watch-run(watching: Watching) 异步方法，启动监听的编译过程
* compilation(c: Compilation, params: Object)


### 开发一个插件