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

`webpack`中的很多对象继承`Tapable`类，都会暴露一个`plugin`方法。
__`webpack`的插件通过`plugin`方法注入构建的各个步骤。__
常用的有`compiler.plugin`和`compilation.plugin`。
基本上在整个构建流程每个步骤中，插件的调用都绑定了一个回调函数。

组件只会在`webpack`启动时安装或者说加载一次。
__`webpack`通过`apply`方法加载一个组件。__
组件的引用传给`webpack`的`compiler`对象。
开发者可以通过调用`compiler.plugin`，注入编译的各个步骤。

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

__Compiler 实例__

组件运行步骤可以想象成离散的各个任务点，从`make`开始，到`compile`编译任务，再到`after-complie`编译完成，准备生成文件`emit`,生成文件之后`emit`等。

`webpack` 插件需要在原型链上定义 `apply` 方法，才有权限调用所有的 `compiler` 的实例方法：

* `run(compiler: Compiler)` 异步方法，启动编译过程
* `watch-run(watching: Watching)` 异步方法，启动监听的编译过程
* `compilation(c: Compilation, params: Object)`
* `normal-module-factory(nmf: NormalModuleFactory)`
* `context-module-factory(cmf: ContextModuleFactory)`
* `compile(params)` 开始编译
* `make(c: Compilation)` 并行
* `after-compile(c: Compilation)` 异步
* `emit(c: Compilation)` 异步
* `after-emit(c: Compilation)`
* `done(stats: Stats)`
* `failed(err: Error)`
* `invalid()`
* `after-plugins()`
* `after-resolvers()`

__Compilation 实例__

在编译阶段，模块被加载、封装、优化、分块、哈希以及重载等。这也是任何一个编译过程的生命周期。
`Compilation`继承`compilier`，因此可以拿到组件编译的生命周期内一切`compiler`的内容。`compilation.getStats()`可以得到生产文件以及`chunkhash`的一些信息。

```javascript
compiler.plugin("compilation", function(compilation) {
    //the main compilation instance
    //all subsequent methods are derived from compilation.plugin
});
compilation.plugin('normal-module-loader', function(loaderContext, module) {
    //this is where all the modules are loaded
    //one by one, no dependencies are created yet
});
// ...
```

* `normal-module-loader`
* `seal`
* `optimize`
* `optimize-tree(chunks, modules)` 异步
* `optimize-modules(modules: Module[])`
* `after-optimize-modules(modules: Module[])`
* `optimize-chunks(chunks: Chunk[])`
* `after-optimize-chunks(chunks: Chunk[])`
* `revive-modules(modules: Module[], records)`
* `optimize-module-order(modules: Module[])`
* `optimize-module-ids(modules: Module[])`
* `after-optimize-module-ids(modules: Module[])`
* `record-modules(modules: Module[], records)`
* `revive-chunks(chunks: Chunk[], records)`
* `optimize-chunk-order(chunks: Chunk[])`
* `optimize-chunk-ids(chunks: Chunk[])`
* `after-optimize-chunk-ids(chunks: Chunk[])`
* `record-chunks(chunks: Chunk[], records)`
* `before-hash`
* `after-hash`
* `before-chunk-assets`
* `additional-chunk-assets(chunks: Chunk[])`
* `record(compilation, records)`
* `optimize-chunk-assets(chunks: Chunk[])`
* `after-optimize-chunk-assets(chunks: Chunk[])`
* `optimize-assets(assets: Object{name: Source})`
* `after-optimize-assets(assets: Object{name: Source})`
* `build-module(module)` 模块构建开始
* `succeed-module(module)` 成功构建一个模块
* `failed-module(module)` 模块构建失败
* `module-asset(module, filename)` 
* `chunk-asset(chunk, filename)`

__mainTemplate 实例__

`startup(source, module, hash)`

__parser 实例(compiler.parser)__

`parser` 实例传入一个字符串和一个回调函数，返回一个匹配的表达式。

```javascript
compiler.parser.plugin("var rewire", function (expr) {
    //if you original module has 'var rewire'
    //you now have a handle on the expresssion object   
    return true;
});
```

* `program(ast)`
* `statement(statement: Statement)`
* `call <identifier>(expr: Expression)` 
* `expression <identifier>(expr: Expression)`
* `expression ?:(expr: Expression)`
* `typeof <identifier>(expr: Expression)`
* `statement if(statement: Statement)`
* `label <labelname>(statement: Statement)`
* `var <name>(statement: Statement)`
* `evaluate <expression type>(expr: Expression)`
* `evaluate __typeof__ <identifier>(expr: Expression)`
* `evaluate Identifier <identifier>(expr: Expression)`
* `evaluate CallExpression .<property>(expr: Expression)`

__NormalModuleFactory__

* `before-resolve(data)`
* `after-resolve(data)`
* `request`
* `userRequest`
* `rawRequest`
* `loaders` 传入 `NormalModule` 中执行
* `resource` `NormalModule` 加载的资源
* `parser`

__ContextModuleFactory__

* `before-resolve(data)`
* `after-resolve(data)`
* `alternatives(options: Array)`

__Resolvers__

* `compiler.resolvers.normal`
* `compiler.resolvers.context`
* `compiler.resolvers.loader`

* `this.fileSystem` 组件的文件系统
* `this.join` 规范化路径
* `resolve(context: String, request: String)`
* `resolve-step(types: String[], request: Request)`
* `module(request: Request)`
* `directory(request: Request)`
* `file(request: Request)`

插件提供更多可扩展的点

1. 通常模块和上下文处理过程：`module -> module-module -> directory -> file`
  * `module-module`放在具体目录下的模块。`path`包括文件目录。
2. 模块加载器的处理过程：`module -> module-loader-module -> module-module -> directory -> file`
  * `module-loader-module`在模块名称应用在模块模版之前。


### 开发一个插件

了解了`compiler`和`compilation`对象，就可以开发`webpack`插件了。

__基本架构__

```javascript
function MyPlugin(options) {
  // Setup the plugin instance with options
}

MyPlugin.prototype.apply = function(compiler) {
  compiler.plugin('done', function() {
    console.log('Hello World!');
  })
}
```

在 `webpack.config.js` 配置文件中

```javascript
var MyPlugin = require('my-plugin');
var webpackConfig = {
  // ... other config
  plugins: [
    new MyPlugin({options: true})
  ]
}
```



[具体教程](https://github.com/webpack/docs/wiki/How-to-write-a-plugin)