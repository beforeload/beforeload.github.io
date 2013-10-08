title: Know your PC
date: 2013-10-08 12:00:39
categories: Linux
tags: Linux
---

你的电脑，你了解多少？
=====================

写这篇博客的原因是，我在使用Linux到现在位置还不知道怎么样去查找电脑相关信息，受到Coursera上[Introduction to Computing 计算概论A](https://class.coursera.org/pkuic-001)课程启发，学习同时分享一下自己查看电脑信息的相关命令和过程。

### Q1: 关于CPU

```shell
cat /proc/cpuinfo   # 指令 1

lscpu               # 指令 2

```

1. 处理器品牌 model name ```cat /proc/cpuinfo | grep name | cut -f2 -d: | uniq -c```
2. 工作频率 指令1得到的cpu MHz是当前工作的频率，指令2得到的cpu MHz是系统可以达到的工作频率
3. 处理器核心数 cpu cores
4. 一级缓存大小 cat /sys/devices/system/cpu/cpu0/cache/index1/size
5. 二级缓存大小 cat /sys/devices/system/cpu/cpu0/cache/index2/size
6. 三级缓存大小 cat /sys/devices/system/cpu/cpu0/cache/index3/size
7. 支持的指令集 

### Q2: 关于内存

1. 内存品牌
2. 内存类型
3. 内存大小
4. 内存条数量
5. 最高工作频率
6. 工作电压
7. 内存延迟

### Q3: 关于显示器

1. 显示器类型(CRT/LCD?)
2. 色彩数
3. 屏幕比例(x:y)
4. 最高分辨率(x*y)
5. 响应时间(ms)
6. 点距(mm)

### Q4: 关于硬盘

1. 硬盘品牌
2. 硬盘大小
3. 硬盘技术类型
4. 硬盘接口类型
5. 数据传输率
6. 硬盘转速

