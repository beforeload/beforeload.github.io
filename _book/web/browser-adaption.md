# 移动 web 适配方案

> 内容简介  
1. 适配方案简介
2. rem 解决方案
3. flex 实现
4. 问题与讨论

### 适配方案简介

从实现方式上划分，一般H5页面开发有以下几种实现方式：

1. meta 缩放
2. Media Query
3. Rem
4. flex

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
    .global-border {
        border-width: 0.5px;
    }
}

@media (-webkit-min-device-pixel-ratio: 3) {
    .global-border {
        border-width: 0.7px;
    }
}
```

而页面中设置border的元素添加 global-border 类名即可实现 0.5px 边框。

__3. rem__

rem 是
__4. flex__


### rem 解决方案

### flex 实现

### 问题与讨论
