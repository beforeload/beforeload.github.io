适配方案
=======

> 内容简介  
1. meta 缩放和 Media Query
2. rem 解决方案
3. flex 实现
4. 问题与讨论

### meta 缩放和 Media Query

__1. meta 缩放：__

我们来看这样一段代码：

```
<meta name="viewport" content="width=device-width,initial-scale=1,maximum-scale=1,user-scalable=0"/>
```

控制 scale 的值，可以让页面缩放到指定尺寸，例如，__宽度__我们以320尺寸为基准，而 iphone6 plus 的宽度是414。那么我们可以通过如下代码将页面进行缩放。

```
<meta name="viewport" content="width=320,initial-scale=1.3,maximum-scale=1,user-scalable=1.3"/>
```

比例 scale ＝ 414 / 320 = 1.29375。

__2. Media Query__

CSS3 的 __Media Query ＝ Media Type（判断条件）＋ CSS（样式规则）__。

常见的写法举例如下：

（1）放在link标签上：

```
/*375px的宽度加载样式文件*/
<link rel="stylesheet" media="only screen and (min-device-width:320px) and (max-device-width:375px)" href="m375.css" type="text/css" />
```

（2）写在文件里，语法如下：

```css
@media screen and (max-width: 600px) {
    选择器 {
        属性：属性值；
    }
}
```

常用判断条件：screen，min-width，max-width，max-device-width，-webkit-min-device-pixel-ratio，orientation等。

这里引入一个小__tips__：

在ios8的手机浏览器上已经支持0.5px 的 border，原有的代码可以通过这样的方式去实现：

```css
@media (-webkit-min-device-pixel-ratio: 2) {
    .thick-border {
        border-width: 0.5px;
    }
}

@media (-webkit-min-device-pixel-ratio: 3) {
    .thick-border {
        border-width: 0.7px;
    }
}
```

而页面中设置border的元素添加 thick-border 类名即可实现 0.5px 边框。

Media Query 详细的文档见[这里](https://developer.mozilla.org/en-US/docs/Web/Guide/CSS/Media_queries)

### rem 解决方案

rem(root em) 是 CSS3 的一种相对单位，IE8及以下浏览器不支持，幸运的是移动web开发不需要考虑兼容 IE8。

如何在开发中更好使用REM，可能大家都会有自己的意见。
但是其原理都是一致的，根据根节点的 font-size 作为基本单位。例如 ```html { font-size: 100px; }```
，那么```1rem = 100px```，浏览器默认值是```1rem = 16px```。

而适配各种机型，那就变的简单了，只需要根据不同屏幕设置不同的根节点的font-size，而我们 css 的代码里写的 1rem 即可对应不同的尺寸了。

假如我们拿到 375px 尺寸的设计稿，通过以下代码去设置不同屏幕的根节点的 font-size：

```
!(function (doc, win) {
    var el = doc.documentElement,
        resizeEvt = 'orientationchange' in window ? 'orientationchange' : 'resize';
    
    function setSize() {
        var clientWidth = el.clientWidth;
        if (!clientWidth) return;
        el.style.fontSize = 100 * (clientWidth / 375) + 'px';
    }
    if (!doc.addEventListener) return;
    win.addEventListener(resizeEvt, setSize, false);
    doc.addEventListener('DOMContentLoaded', setSize, false);
})(document, window);
```

如此，我们在 375px 的设计稿上量取某元素高度为 100px, 只需要 除以100，在 CSS 种写 ```height: 1rem;```即可。

### flex 实现

flex 解决方案详情见

### 问题与讨论
