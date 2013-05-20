title: Fix Some Problems
date: 2013-05-20 22:10:58
tags:
---

###解决的一些问题汇总：

1. Fix ASUS Webcam Problem:vertically flip my webcam's image

___问题描述___：华硕电脑摄像头视频图像倒立

解决方法： 在google上搜索了一下 ubuntu camera invert找到几篇解决的博文，步骤如下：

(1) Install

    $echo -e "\n# libv4l PPA\ndeb http://ppa.launchpad.net/libv4l/ppa/ubuntu `lsb_release -c | awk '{print $2}'` main" | sudo tee -a /etc/apt/sources.list

    $sudo apt-key adv --recv-keys --keyserver keyserver.ubuntu.com C3FFB4AA

    $sudo apt-get update

    $sudo apt-get install libv4l-0

(2) Start application

```
$export LIBV4LCONTROL_FLAGS=3 && cheese
$export LIBV4LCONTROL_FLAGS=3 && LD_PRELOAD=/usr/lib/i386-linux-gnu/libv4l/v4l1compat.so skype
```

为了以后不需要输入这么多，建立一个bash
Step 1:

```
$sudo gedit /usr/local/bin/skype
```

填写下面内容

    LD_PRELOAD=/usr/lib/i386-linux-gnu/libv4l/v4l1compat.so /usr/bin/skype

Step 2:

```
sudo chmod a+x /usr/local/bin/skype
```

___注意___：
1. v4l1compat.so的位置在Ubuntu 13.04上可能和之前的版本不同，使用locate指令可以先确定一下位置。

```
$ locate v4l1compat.so
/usr/lib/i386-linux-gnu/libv4l/v4l1compat.so
```

___参考___：

(1) [Fix upside-down or inverted webcam on Ubuntu linux](http://www.paullabis.com/2010/08/fix-upside-down-or-inverted-webcam-on.html)

(2)[How to make Webcam compatible with Skype.](http://community.linuxmint.com/tutorial/view/219)
