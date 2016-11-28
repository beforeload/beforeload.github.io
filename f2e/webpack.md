# webpack

模块打包工具。

### 概述

八大核心功能：

1. __Plugin__：拥有丰富的组件接口，webpack 的很多功能都是通过插件实现的。
2. __Performance__：使用异步 I/O，多级缓存，增量编译速度奇快。
3. __Loaders__：预处理文件，支持打包任意静态资源，不仅仅是 JS。使用 Node.js 可以开发自己的加载器。
4. __Support__：webpack 支持 AMD 和 CommonJS 等规范，对代码的抽象语法树（AST）的静态分析上表现良好。
甚至它还有一个运行引擎，可以执行简单的表达式，对很多已有的库提供支持。
5. __Code Splitting__：将代码库分割成代码片段，按需加载，降低最初的加载时间。
6. __Optimizations__：可以做大量优化，减少输出体积。通过文件名 hash 来支持请求缓存。
7. __Development Tools__：支持 SourceUrls 和 SourceMaps 进行简单调试。可以通过开发中间件和开发环境的服务来完成自动重载。
8. __Multiple targets__：主要目标是 web 项目。也支持为 WebWorkers 和 Node.js 项目进行打包。

### Plugin