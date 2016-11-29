# loaders

文件转换器：source -> 函数 -> new source

[loaders列表](http://webpack.github.io/docs/list-of-loaders.html)

1. 可以链式调用，管道方式，最后的loader 返回`JavaScript`，中间传递是二进制格式
  * `loader: style!css!less`
2. 可以同步也可以异步
3. loaders 使用Node.js 运行
4. loaders 接受query 参数
  * `loaders: [babel?presets[]=react,presets[]=es2015]`
  * `url-loader?mimetype=image/png`
5. 文件扩展名或正则表达式绑定loader
6. npm 发布和安装
7. 通过模块也可以导出一个 loader
8. loaders 可以访问配置
9. 可以生成附加文件

### 用法

1. 使用`webpack`配置文件
2. 使用`CLI`命令行
3. 使用`require` (避免使用，Node.js环境和浏览器环境)

__在`webpack`配置里__

```javascript
{
  module: {
    loaders: [{
      test: /\.(js|jsx)$/,
      exclude: /node_modules/,
      loaders: ['babel?presets[]=react,presets[]=es2015'],
      include: path.join(__dirname, '/src/')
    }, {
      test: /\.css$/,
      loader: 'style!css'
    }, {
      test: /\.less/,
      loader: 'style!css!postcss!less'
    }, {
      test: /\.(png|jpg|gif|woff|woff2)$/,
      loader: 'url-loader?limit=8192'
    }, {
      test: /\.(ttf)$/,
      loader: 'url-loader?limit=65000&mimetype=application/octet-stream'
    }]
  }
}
```

__命令行__

```shell
$ webpack --module-bind jade --module-bind 'css=style!css'
$ webpack --module-bind 'png=url-loader?mimetype=image/png'
```

__require__

```javascript
require('./loader!./dir/file.txt');
require('jade!./template.jade');
require('!style!css!less!bootstrap/less/bootstrap.less');
require('url-loader?mimetype=image/png!./file.png');
```

### loader 执行顺序

[配置](http://webpack.github.io/docs/configuration.html)

1. preloaders
2. loaders
3. 使用`require`特定请求loader
4. postLoaders

loader 在模块请求（如：`require`）的顺序可以用特殊方法覆盖配置：(不推荐使用)

* `!` 可以禁用 `preLoaders` `require('!raw!./script.coffee')`
* `!!` 禁用所有其他配置 `require('!!raw!./script.coffee')`
* `-!` 禁用`preLoaders`和`loaders`，但是不包括`postLoaders`  `require('-!raw!./script.coffee')`

使用loader 的一些小建议：

* preLoader: 图片压缩
* loader：配置文件转换（如coffee，typescript）
* request：bundler 加载
* postLoader：代码覆盖率检测

### 开发一个 loader

非常简单！

一个`loader`，就是一个导出一个`function`的`node`模块。

[教程](http://webpack.github.io/docs/how-to-write-a-loader.html)

__同步loader__

```javascript
module.exports = function(content) {
 return someSyncOperation(content);
}
```

__异步loader__

```javascript
module.exports = function(content) {
  var callback = this.async();
  if(!callback) return someSyncOperation(content);
  someAsyncOperation(content, function(err, result) {
    if(err) return callback(err);
    callback(null, result);
  });
}
```

建议给异步loader 一个可以回滚到同步loader 的模式，虽然 `webpack` 没有要求，但是可以更好的兼容`require`。

__原生loader__

把源文件当作`utf-8`字符串，传入`loader`进行处理。设置`raw`为`true`开启传输原生的`Buffer`。

每个loader 都允许使用字符串或者使用`Buffer`的方式传输数据。编译器会在不同的`loader`中进行转换。

__pitching loader__

这种`loader`从右到左执行，不关心前后loader返回的结果甚至是源文件，只关心`metadata`。

__loader 上下文__

* `version` 1
* `request`
* `query`
* `data`
* `cacheable`
* `loaders`
* `loaderIndex` 从0开始
* `resource` 包括`query`
* ...
* `target` 编译目标，值有 `'web'`, `'node'`
* `webpack` true 表示使用`webpack`编译
* `emitFile` `emitFile(name: string, content: Buffer|String, sourceMap: {...})`

1. 只做一件事，如非必要，不要转成`JavaScript`
1. 生成模块的模块
1. 尽可能标记缓存：调用`this.cacheable()`
1. 不要保存中间状态 每个`loader`独立存在
1. 标记依赖 使用的`external`资源需要标记，用来去缓存，在监听模式下重新编译。
1. 解决依赖关系
  * `url(file) -> require('./file')`
  * `url(~module) -> require('module')`
1. 展开公用的代码（在每个模块中不要生成太多公用代码）在loader中创建一个运行时文件，生成一个`require`指向这段公用的代码。
1. 不要包含绝对路径