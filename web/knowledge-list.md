知识体系
=======

目标：建立移动web开发知识体系
  
> 写在前面的话：无线开发的知识点远不止这些，结合自己开发的一些实践，整理出来一些无线开发知识体系，以及方便进阶的学习路线。

### Contents

1. [布局](#layout)
2. [事件](#events)
3. [动画](#animation)
4. [性能](#performance)
5. [路线](#roadmap)
6. [测试](#test)
7. [资源](#resources)

### Layout

1. 无线开发基本概念
  * [移动端的头部标签和 meta](http://www.atatech.org/articles/33460)
  * 尺寸单位 px, em, rem(, vh, vw, vmin, vmax)的区别
  * -webkit-device-pixel-ratio 等常见的 iPhone 和 Android 屏幕参数
  * media query, media type
2. 无线布局适配
  * grid, flex等布局方式
  * 等比和非等比宽度布局实现

### Events

1. 事件类型
  * 手势事件  touchstart, touchmove, touchend, touchcancel
      - touch事件支持的相关属性touches, targetTouches, changedTouches
  * 触摸事件  gesturestart, gesturechange, gestureend
  * 屏幕事件  onorientationchange
  * 鼠标事件
  * MS Pointer事件
2. 事件封装
  * 手势方向判断
  * 非原生事件封装的原理（如：tap事件）
3. 常见问题
  * 单击延迟
  * 点击穿透

### Animation
1. 动画实现
  * jQuery Animation: setTimeout, top/left
  * transition，animation
  * transform
  * requestAnimationFrame(自动调节频率)
2. 动画渲染
  * 渲染过程
      - Parse HTML
      - Recalculate Style
      - Layout
      - Update Layer Tree
      - Paint
      - Composite Layer（从 GPU 渲染到屏幕）
  * 屏幕刷新频率
      - 渲染一帧的时间 目标 16ms
3. 动画优化
  * GPU acceleration （硬件加速）vs CPU
      - Texture (CPU 传输到GPU的是一个位bitmap)
      - GPU 能快速进行位移，缩放，旋转，修改透明度等操作
      - 开启硬件加速 will-change:transform,opactiy;
  * Layer 模型
      - 浏览器根据CSS属性生成 Layer
      - Layer（即Texture）上传GPU
      - GPU直接进行 Layer 的 transform, opacity 变化（跳过 Layout，paint 过程）
  * Layer 创建标准
      - 3d transform
      - animation, transition 实现 opacity, transform
      - video, canvas, Flash
      - CSS filters
      - z-index 大于相邻节点的 Layer 的元素
  * 减少触发Layout
      - 改变大小位置属性（width, height, position, margin ...）会触发layout
      - 分离读写，尽量先读后写
  * 减少触发 Paint
      - 简化绘制的复杂度: 修改展示类属性（border-radius, box-shadow, color...）会触发Paint
      - 避免不必要的绘制: Gif 会触发（display: none; 也会）
      - 减少绘制区域
  * Composite
      - GPU 有限度，不要创建不必要的Layer
  * 事件带来的影响（touchmove, mousemove, scroll, etc)
      - 事件处理函数不要修改样式
      - ontouchmove 阻塞 Composite Thread
      - touch-action 由CSS属性决定是否阻止默认行为
      - scroll 过程中，hover会导致repaint，可以通过在滚动后添加event-pointer: none解决
  * CSS style 对动画影响 [csstriggers](http://csstriggers.com/)
4. 动画实践
  * 常见 CSS3 动画（旋转，缩放，淡入淡出）的实现
  * scrollview（推荐学习iscroll）
  * slider 转屏动画

### Performance

1. 常见性能指标
  * 白屏时间
  * 首屏时间
  * 加载时间
2. HTML5 API
  * canvas
  * Device Orientation API
  * audio
  * cookie 和 localstorage
  * Application cache

### Roadmap

以上四点是一些知识点的总结，联系具体实际，在不同阶段需要能够具备什么样开发能力，这里做一个小结。

1. 初级
  * 能够实现适配的布局
      - 案例：一排几布局，非等比布局
      - 知识点：Layout 1, 2
  * 熟练使用无线相关事件
      - 知识点：Events 1
2. 中级
  * 能够提供兼容性的适配方案
      - 案例：flex布局，REM方案
      - 知识点：Layout 2
  * 能够了解事件相关原理，解决常见的事件问题
      - 案例：点击穿透，点击延迟等
      - 知识点：Events 2
  * 能够实现常见 CSS3 动画
      - 知识点：Animation 1, 4
  * 了解基本性能指标，了解统计相关知识
      - 知识点：Performance 1
  * 能够实现无线常见功能
      - 案例：瀑布流
      - 知识点：Layout, Events, Animation 4
  * 能够了解和使用html5 Api
      - 案例：使用 localstorage 离线存储
      - 知识点：Performance 2
3. 高级
  * 能够实现复杂交互，并考虑性能
      - 案例：微信宣传动画
      - 知识点：Animation
  * 能够使用HTML5 API实现复杂功能
      - 案例：刮一刮 (canvas)，摇一摇 (Device Orientation API)，咻咻咻 (audio)，啪啪啪 (getUserMedia)
      - 知识点：Performance 2
  * 能够结合前端工程，提供优化解决方案
      - 案例：单页面应用，离线应用
      - 知识点：Performance 2, 前端工程相关
  * hybrid app 和 react native

### Test
1. 调试工具
  * chrome 扩展 远程调试 chrome://inspect/#devices
  * Safari Web 检查器
  * weinre
2. 代理工具
  * Fiddler
  * Charles

### Resources

1. 工具
  * [CSS3属性查找](http://ued.ctrip.com/blog/wp-content/webkitcss/index.html)
  * [caniuse](http://caniuse.com/)
2. 资源
 * [移动WEB开发入门](http://junmer.github.io/mobile-dev-get-started/)
 * [移动开发资源集合](https://github.com/jtyjty99999/mobileTech)
 * [The Mobile Web Handbook](http://quirksmode.org/mobilewebhandbook/)
 * [MobileWeb 适配总结](http://www.w3ctech.com/topic/979)
 * [移动前端不得不了解的html5 head 头标签](http://www.css88.com/archives/5480)
 * [浅谈移动前端的最佳实践](http://www.cnblogs.com/yexiaochai/p/4219523.html)
 * [移动端真机调试终极利器-BrowserSync](http://www.codingserf.com/index.php/2015/03/browsersync/)

