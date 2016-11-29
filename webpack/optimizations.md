# 优化

### 最小化代码

* 命令行参数 `--optimize-minimize`
* 配置文件 `new webpack.optimize.UglifyJsPlugin()`

* 命令行参数 `--optimize-occurrence-order`
* 配置文件 `new webpack.optimize.OccurrenceOrderPlugin()`

### 减少重复


* `--optimize-dedupe`
* `new webpack.optimize.DedupePlugin()`

### Chunks

__限制最大块数__

* `--optimize-max-chunks 15`
* `new webpack.optimize.LimitChunkCountPlugin({maxChunks: 15})`

__限制块最小体积__

* `--optimize-min-chunk-size 10000`
* `new webpack.optimize.MinChunkSizePlugin({minChunkSize: 10000})`

### 多页面App

```js
var CommonsChunkPlugin = require("webpack/lib/optimize/CommonsChunkPlugin");
module.exports = {
    entry: {
        p1: "./page1",
        p2: "./page2",
        p3: "./page3"
    },
    output: {
        filename: "[name].entry.chunk.js"
    },
    plugins: [
        // new CommonsChunkPlugin("commons.chunk.js")
        new CommonsChunkPlugin('admin-commons.js', ['ap1', 'ap2']),
        new CommonsChunkPlugin('commons.js', ['p1', 'p2', 'admin-commons.js'])
    ]
}
// <script>s required:
// page1.html: commons.js, p1.js
// page2.html: commons.js, p2.js
// page3.html: p3.js
// admin-page1.html: commons.js, admin-commons.js, ap1.js
// admin-page2.html: commons.js, admin-commons.js, ap2.js
```

__高级用法__

```js
var CommonsChunkPlugin = require("webpack/lib/optimize/CommonsChunkPlugin");
module.exports = {
    entry: {
        p1: "./page1",
        p2: "./page2",
        commons: "./entry-for-the-commons-chunk"
    },
    plugins: [
        new CommonsChunkPlugin("commons", "commons.js")
    ]
};
```

在公用模块中可以运行自己的代码。