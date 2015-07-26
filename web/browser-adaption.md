# 移动 web 适配方案

内容简介:

> 适配方案简介
> Media Query
> rem 原理
> 兼容性 rem 解决方案
> flex 布局实现
> 问题与探究

### 适配方案简介

从实现方式上划分，一版H5页面开发有以下几种实现方式：

1. meta 缩放
2. 百分比定位
3. rem
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

比例 ＝ 414 / 320 = 1.29375

__2. 百分比定位__

__3. rem__

__4. flex__