title: A trick in python
date: 2013-10-28 20:46:09
categories: Python
tags: Python
---

## 一个让我惊讶的转换

今天写python，无意中发现一次打印的结果让我甚是惊讶！

废话不多说，见源码：

{% gist 7196281 amazing_convert.py %}

<!-- more -->

__打印结果如下：__
```
-40.0 -39
-40 -40
-1 -39 -39
-1 dollars and -39 cents

```

惊讶的发现``cents``打印出来是-39,竟然不是-40！
而``int(-40.0)``结果是-40


