title: The Fundamentals Knowledge of Python
date: 2013-08-20 02:19:22
categories: Python
tags: Python
---

## Python 基础知识

简介：这是对Coursera上的[Learn to Program: The Fundamentals](https://class.coursera.org/programming1-002/class)第一周课程的一次总结。 

<!-- more -->
> 第一点： 作为计算器使用

1. 加减乘除和其他语言的运算符号相同；
2. 两个符号
  * // 整除符号(可对应模除符号 % 记忆)
  * ** 求幂(乘方)
3. 两种数据类型
  * int: 整型
  * float: 浮点型（近似数）
4. 符号优先级
  * **最高
  * -负数
  * *,/,//,%
  * +,- 加减运算
5. 语法和语义
6. 错误
  * 例如输入``3) + 2 * 4``， 执行时会报出语法错误(SyntaxError)
  * 例如输入``89.0 / 0``， 执行时会报``ZeroDivisionError: float division by zero``

> 第二点： Python 和计算机内存

__计算机内存：__

  计算机内存中，内存地址指向存储的数据。我们会在地址前加上前缀'id'（例如，id201代表内存地址201）。备注：Python中可以通过id方法去查看内存地址，该方法返回一个整型数。help(id)

在内存中，变量是存储值的一种方法。变量命名指代的是内存中的地址，Python在相对于值之外的一个独立的list中存储变量。一个变量容纳内存中的地址，而这个地址容纳对应的值(value)。因此，这个变量映射到这个值。

__术语:__

1. A value has a memory address. 一个值有一个内存地址
2. A variable contains a memory address. 一个变量容纳一个内存地址
3. A variable refers to a value.一个变量映射一个值
4. A variable points to a value.一个变量指向一个值

> 第三点： 变量

    variable = expression

__赋值语句：__

执行赋值语句的规则是：

1. 执行表达式，它会产生一个内存地址；
2. 存储内存地址到一个变量。

__变量名：__

Python合法的变量名规则是：

1. 名字以字母或_开头；
2. 名字必须只包含字母，数字和下划线_。

对于Python，大多数场景下，约定俗成的使用凹槽方式pothole_case。

> 第四点：内置函数

__函数调用：__

    function_name(arguments)

术语：

* Argument: 形参
* Pass: 传递
* Call: 调用
* Return: 返回结果

__Function dir__

Python有一系列内置的函数。运行``dir(__builtins__)``可以查看内置的函数列表。

__Function help__

为了得到某个函数的具体信息，可以调用help方法，将函数名作为参数，如下：

    >>> help(abs)
    Help on built-in function abs in module builtins:
    abs(...)
        abs(number) -> number

            Return the absolute value of the argument.

__可选参数__

例如在pow函数中[, z]表示z参数可选。

    >>> help(pow)
    Help on built-in function pow in module builtins:

    pow(...)
        pow(x, y[, z]) -> number

            With two arguments, equivalent to x**y.  With three arguments,
                equivalent to (x**y) % z, but may be more efficient (e.g. for longs).



> 第五点：函数

__函数定义：__

    def function_name(parameters):
        body

__执行return语句过程：__

1. 执行语句的表述，产生一个内存地址；
2. 将内存地址传递给调用者，结束函数。

__执行函数调用的过程：__

1. 求实参的值，产生内存地址；
2. 存储内存地址到对应的形参；
3. 执行函数体。

__保存程序到".py"文件中:__

我们通常将Python程序保存到".py"文件中。一个文件可以包含多个函数定义以及一些其他语句表述。调用".py"文件的函数之前，要先运行这个模块，否则IDLE的shell解释器不会识别这个函数调用。
