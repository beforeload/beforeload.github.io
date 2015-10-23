点击穿透
=======

> 1. [What? -> Events!](#what)
> 2. [Why?](#why)
> 3. [How to fix?](#how)

说在前面的话：点击穿透这种现象算是移动H5开发比较经典的问题，也常用来做面试题。借此现象谈谈移动事件。

### What

__现象描述：__
页面中存在上下两个层，上层元素具有表单，链接或者绑定相应事件，上层元素点击或触摸，导致上层DOM改变，下层中同样位置的 元素触发点击事件。这种现象就是点击穿透（Ghost Clicks）。其实我觉得用“幽冥点击”称呼它更加带感。

要理解这种现象，首先要了解移动端的相关事件 —— 触摸(Touch)事件，点击(Click)事件。

Touch 事件中，常用的为 touchstart, touchmove, touchend 三种。除此之外还有touchcancel。
__注意，原生事件中并没有tap事件。__下面会解释tap事件怎么产生的。

事件描述如下：

| 事件 | 描述 | 触发时机 |
|---|---|---|
| touchstart | 开始触摸 | 手指接触屏幕时立即触发 |
| touchmove | 移动或拖拽 | 取决于系统和浏览器 |
| touchend | 触摸结束| 手指离开屏幕时立即出发 |

而Touch事件的触发一般通过手指，还会存在多点触控，拖拽方向等情况。列出几个重要参数如下：

| 参数 | 含义 |
|---|---|
| touches | 屏幕中每根手指信息列表 |
| targetTouches | 和touches类似，把同一节点的手指信息过滤掉 |
| changedTouches | 响应当前事件的每根手指的信息列表 |

代码获取如下：

```javascript
xxDOM.addEventListener('touchstart', function(e) {
   console.log(e.touches, e.targetTouches, e.changedTouches);
});
```

手指触发触摸事件的过程如下：

```
touchstart -> touchmove -> ... touchmove -> touchend
```

由此，我们可以在 ontouchstart 事件上记录开始触摸开始，ontouchend 记录触摸结束信息。
通过上述这些参数，很容易的去计算幽冥点击的时间，以及点击穿透的相关信息，包括响应的坐标情况。

### Why

问题来了，__click 事件什么时候触发？__

浏览器在 touchend 之后会等待约 300ms ，如果没有 tap 行为，则触发 click 事件。
而浏览器等待约 300ms 的原因是，判断用户是否是双击（double tap）行为，双击过程中就不适合触发 click 事件了。
由此可以看出 click 事件触发代表一轮触摸事件的结束。

上面说到原生事件中并没有 tap 事件，可以参考经典的 zepto.js 对 singleTap 事件的处理。[见源码 136-143 行](https://github.com/madrobby/zepto/blob/master/src/touch.js#L136-L143)

可以看出，singleTap 事件的触发时机 —— 在 touchend 事件响应 250ms 无操作后，触发singleTap。

因此，点击穿透的现象就容易理解了，在这 300ms 以内，因为上层元素隐藏或消失了，由于 click 事件的滞后性，同样位置的 DOM 元素触发了 click 事件（如果是 input 则触发了 focus 事件）。在代码中，给我们的感觉就是 target 发生了飘移。

### How

理解点击穿透的原因，我们从各种途径去阻止现象的产生。

毫无疑问，能想到的方法很多，比如中间的层添加一个 300ms 渐隐的动画，触摸结束后阻止 click 事件等。

__1. 触摸开始时__
touchstart 事件触发时，preventDefault()。毫无疑问，很容易想到这一点，而且也从根本上解决了这个问题。但是，它有一个避免不了或者说引入了很大的缺陷，页面中DOM 元素无法再进行滚动了。这个方法显然不能满足我们的需求，但是这个思路其实可以给我们更多的启发，比如说 iscroll 只允许横向滚动的实现，相关实现这里暂且不表。

__2. 触摸结束时__
touchend 事件触发时，preventDefault()。看上去好像没有什么问题，但是，很遗憾的是不是所有的浏览器都支持。

__3. 禁止页面缩放__
通过设置meta标签，可以禁止页面缩放，__部分浏览器__不再需要等待 300ms，导致点击穿透。点击事件仍然会触发，但相对较快，所以 click 事件从某种意义上来说可以取代点击事件，
而代价是牺牲少数用户（click 事件触发仍然较慢）的体验。

```
<meta name="viewport" content="width=device-width, user-scalable=no">
```

IE 10可以用 CSS 取消点击穿透的延迟：

```css
html {
    -ms-touch-action: manipulation;
    touch-action: manipulation;
}

```

IE 11+ 可以用 ```touch-action: manipulation;``` 属性来阻止元素的双击缩放。

__4. CSS3 的方法__
虽然主要讲的是事件，但是有必要介绍一个 CSS3 的属性 ——  pointer-events。

```css
pointer-events:  auto | none | visiblePainted | visibleFill | visibleStroke | visible | painted | fill | stroke | all | inherit;
```

pointer-events 属性有很多值，有用的主要是 auto 和 none，其他属性为 SVG 服务。

[查看浏览器支持情况](http://caniuse.com/#search=pointer-events) 可见移动端开发还是可以用的。

| 属性 | 含义 |
|---|---|
| auto | 默认值，鼠标或触屏事件不会穿透当前层 |
| none | 元素不再是target，监听的元素变成了下层的元素（如果子元素设置成 auto，点击子元素会继续监听事件） |

__5. 处理点击事件 —— Touch to Click__
最靠谱的方案还是从点击事件的根源上解决问题。用 js 去判断幽冥点击，然后阻止点击穿透。这种方式显然可以实现，缺点是阻止点击穿透时需要小心，不要导致原生的 HTML 元素（如：链接，多选框，单选框）无法正常运行。

通过上文中介绍的 touches，targetTouches，changedTouches 参数，我们可以构建出这样的[测试页面](http://mlaval.github.io/ghostclick/)，可以统计出点击穿透的时间，以及已经响应的情况。

<table>
  <tbody>
    <tr>
      <th></th>
      <th colspan="2">preventDefault()</th>
      <th colspan="2">点击穿透时间</th>
      <th colspan="2">点击穿透区域</th>
    </tr>
    <tr>
      <th></th>
      <th>touchstart</th>
      <th>touchend</th>
      <th>缩放页面</th>
      <th>禁止缩放页面</th>
      <th>缩放页面</th>
      <th>禁止缩放页面</th>
    </tr>
    <tr>
      <td>Safari Mobile iOS 5.1.1</td>
      <td>Yes</td>
      <td>Yes</td>
      <td>370ms after end</td>
      <td><strong>370ms</strong> after end</td>
      <td>touchstart</td>
      <td>touchstart</td>
    </tr>
    <tr>
      <td>Safari Mobile iOS 6.1.3</td>
      <td>Yes</td>
      <td>Yes</td>
      <td>370ms after end</td>
      <td><strong>370ms</strong> after end</td>
      <td>touchstart</td>
      <td>touchstart</td>
    </tr>
    <tr>
      <td>Safari Mobile iOS 7.1.1</td>
      <td>Yes</td>
      <td>Yes</td>
      <td>370ms after end</td>
      <td><strong>370ms</strong> after end</td>
      <td>touchstart</td>
      <td>touchstart</td>
    </tr>
    <tr>
      <td>Android 2.3.7</td>
      <td>Yes</td>
      <td><strong>No</strong></td>
      <td>410ms after end</td>
      <td><strong>410ms</strong> after end</td>
      <td>touchstart</td>
      <td>touchstart</td>
    </tr>
    <tr>
      <td>Android 4.0.4</td>
      <td>Yes</td>
      <td><strong>No</strong></td>
      <td>300ms after end</td>
      <td>10ms after end</td>
      <td>touchstart</td>
      <td>touchstart</td>
    </tr>
    <tr>
      <td>Android 4.1.2</td>
      <td>Yes</td>
      <td><strong>No</strong></td>
      <td>300ms after end</td>
      <td><strong>300ms</strong> after end</td>
      <td>touchstart</td>
      <td>touchstart</td>
    </tr>
    <tr>
      <td>Android 4.2.2</td>
      <td>Yes</td>
      <td><strong>No</strong></td>
      <td>300ms after <strong>start</strong></td>
      <td>10ms after end</td>
      <td>touchstart</td>
      <td><strong>touchend</strong></td>
    </tr>
    <tr>
      <td>IE10 Windows Phone 8</td>
      <td><strong>No</strong></td>
      <td><strong>No</strong></td>
      <td>310ms after end</td>
      <td>10ms after end</td>
      <td><strong>touchend</strong></td>
      <td><strong>touchend</strong></td>
    </tr>
    <tr>
      <td>Blackberry 10</td>
      <td>Yes</td>
      <td>Yes</td>
      <td>260ms after end</td>
      <td>10ms after end</td>
      <td>touchstart</td>
      <td>touchstart</td>
    </tr>
    <tr>
      <td>Chrome for iOS</td>
      <td>Yes</td>
      <td>Yes</td>
      <td>360ms after end</td>
      <td><strong>360ms</strong> after end</td>
      <td>touchstart</td>
      <td>touchstart</td>
    </tr>
    <tr>
      <td>Chrome for Android</td>
      <td>Yes</td>
      <td>Yes</td>
      <td>300ms after <strong>start</strong></td>
      <td>10ms after end</td>
      <td>touchstart</td>
      <td><strong>touchend</strong></td>
    </tr>
    <tr>
      <td>Firefox for Android</td>
      <td>Yes</td>
      <td><strong>No</strong></td>
      <td>300ms after end</td>
      <td>10ms after end</td>
      <td>touchstart</td>
      <td><strong>touchend</strong></td>
    </tr>
  </tbody>
</table>

由此可以看出：
1. 点击穿透受浏览器和页面是否缩放影响
2. 点击穿透有两种情况：快速情况有 10ms 慢速情况有 300ms
3. 在 touchend 时间上调用 preventDefault() 可以阻止多数情况的点击穿透

代码上处理建议如下：

1. 在touchend事件上调用 preventDefault()
2. 在一次成功的点击后，建议接下来的 500ms 以内取消所有的 click 事件。
3. 分析点击事件，判断如果是慢速点击穿透，则取消所有 click 事件，如果是快速点击穿透，取消触摸事件 50ms以内的 click 事件即可。

有个好消息是，移动端开发已经有人写好相应的库，帮助我们处理点击穿透。
[fastclick](https://github.com/ftlabs/fastclick)可以参考和使用。
其实现思路是，取消 click 事件（[参看源码164-173行](https://github.com/ftlabs/fastclick/blob/master/lib/fastclick.js#L164-L173)），用touchend 模拟 快速点击行为（[参看源码 521-610 行](https://github.com/ftlabs/fastclick/blob/master/lib/fastclick.js#L521-L610)）。

### 参考文章

1. [Ghost clicks in mobile browsers](http://ariatemplates.com/blog/2014/05/ghost-clicks-in-mobile-browsers/)
2. [300ms tap delay gone away](http://updates.html5rocks.com/2013/12/300ms-tap-delay-gone-away)
3. [Javascript Powered Webapps and Click Versus Tap](http://www.appwards.nl/blog/2013/01/26/javascript-powered-webapps-and-click-versus-tap)
4. [Fast Buttons](https://developers.google.com/mobile/articles/fast_buttons)