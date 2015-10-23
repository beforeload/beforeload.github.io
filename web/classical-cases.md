经典案例
=======

> 内容简介  
1. [样式布局](#layout)
2. [事件](#events)
3. [动画](#animation)

### Layout

__(1) REM__

参考[移动 web 适配方案](http://liudong.me/web/browser-adaption.html)

__(2) 文本上下垂直居中__

* table 方式：

```CSS
display: table-cell;
text-align: center;
vertical-align: middle;
```

* flex 方式：

```
display:-webkit-box;
-webkit-box-pack: center;
-webkit-box-align: center;
text-align:center;
```

__(3) 多行文本__

```CSS
div {
    display: -webkit-box;
    text-overflow: ellipsis;
    -webkit-line-clamp: 3;
    -webkit-box-orient: vertical;
    overflow : hidden;
}
```

__(4) Retina 高清图片__

```CSS
div {
  background-image: url(no-image-set.png);
  background: image-set(url(foo-lowres.png) 1x,url(foo-highres.png) 2x) center;
}
```

不支持 image-set 时解析的是 background-image。

__(5) 背景透明__

```
background: rgba(0, 0, 0, 0.8);
```

用opacity 会连同文字一起变的透明。

__(6) 阻止字体自动调整__

```
html, body, form, fieldset, p, div, h1, h2, h3, h4, h5, h6 {
    -webkit-text-size-adjust:none;
}
```
__(7) 点击抖动__

```
-webkit-tap-highlight-color:rgba(0, 0, 0, 0);
```

__(8) 取消 a 标签点击时虚线__

```
outline：none;
```

__(9) 禁止选中__

```
user-select: none;
-moz-user-select: none;
-webkit-user-select: none;
-ms-user-select: none;
```

android机型上，点击会触发选中。
这一点在一些小的icon上使用，用户体验较好。

__(10) 禁止缩放__

```
<meta content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=0;" name="viewport" />
```

__(11) 禁止 标签弹出浏览器选项__

```CSS
-webkit-touch-callout: none;
```

__(12) 处理吸顶元素（fix 定位）键盘弹起__

参考 m.taobao.com，input元素弹起时，下方用一个遮罩，禁止上下滚动，这样页面高度和浏览器高度相同，
键盘弹起时，不会出现吸顶的元素错位的情况。

```JavaScript
$body.css({
    'height': window.innerHeight + 'px',
    'overflow': 'hidden'
});

// 禁止滚动
$body.on('touchmove', function(e) {
    e.preventDefault();
    return false;
});
```

### Events

__(1) 点击穿透__

参考 [点击穿透](http://liudong.me/web/touch-defect.html)

__(2) iOS 键盘__

```
<input type="text">  <!-- 数字键盘 -->
<input type="text" pattern="[0-9]*"> <!-- 数字键盘，只有 0-9 没有 +/- -->
<input type="url">  <!-- 地址 键盘上含有 .com 按键 -->
```

iOS 支持的键盘类型还有很多，例如 添加属性 ```autocapitalize="off"```，则取消首字母大写。

[支持的键盘类型](https://developer.apple.com/library/safari/documentation/AppleApplications/Reference/SafariHTMLRef/Articles/InputTypes.html)

[控制各种键盘展示](http://sja.co.uk/controlling-which-ios-keyboard-is-shown)

还可以消除原生外观，自定义键盘样式

```
-webkit-appearance: none;
```


__(3) IScroll__

滚动元素 设置 ```display: none``` 再改为 ```display: block``` 的时候会导致IScroll无法滚动的问题

原因：不在文档流中显示的元素offset() 得到值为0，滚动动画不生效

需要修改IScroll的源码，检测 DOM 的改变。

__(4) 局部滚动broken__

fix定位可能导致滑动不流畅，在iOS上设置以下属性，需要同时绑定touchstart事件

```
-webkit-overflow-scrolling: touch
```

bug 场景描述见 [stackoverflow](http://stackoverflow.com/questions/18736297/webkit-overflow-scrolling-touch-broken-for-initially-offscreen-elements-in-i)

### Animation

__(1) 硬件加速__

```
-webkit-transform:translate3d(0, 0, 0);
```

android 对 translate3d 动画效果明显比 iOS 要弱。
尽可能让动画元素用 absolute 定位。

__(2) 动画函数__

一个 fadeIn 效果，摘自 [animate.css](https://github.com/daneden/animate.css/blob/master/animate.css#L1150-L1173)

```CSS
.animated {
  -webkit-animation-duration: 1s;
  animation-duration: 1s;           /* 事件间隔 1秒 */
  -webkit-animation-fill-mode: both;
  animation-fill-mode: both;        /* 动画开始和结束之外的状态 */
}

@-webkit-keyframes fadeIn {
  from {
    opacity: 0;
  }

  100% {
    opacity: 1;
  }
}

@keyframes fadeIn {
  from {
    opacity: 0;
  }

  100% {
    opacity: 1;
  }
}

.fadeIn {
  -webkit-animation-name: fadeIn;
  animation-name: fadeIn;
}
```
__(3) iOS 多边形裁切__

```
-webkit-clip-path: polygon(0px 0px, 100px 0, 0 100px);
```

__(4) 动画闪烁问题__

```
-webkit-backface-visibility: hidden; 
-moz-backface-visibility: hidden;
 -ms-backface-visibility: hidden; 
backface-visibility: hidden; 
-webkit-perspective: 1000; 
-moz-perspective: 1000; 
-ms-perspective: 1000; 
perspective: 1000;
```

3D 动画由于默认情况下，是可以看到后面的元素的，因为视差，会造成闪烁情况。
上述样式可以隐藏后面的元素。

