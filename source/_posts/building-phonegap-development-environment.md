title: building PhoneGap development environment
date: 2013-06-12 02:29:06
tags: 
- Ubuntu
- Android
categories: Ubuntu
---

### Ubuntu 13.04 Android开发基本环境配置
#### 基本环境
    sudo apt-get install ia32-libs #64位系统的32位程序兼容库adb需要此库(32位系统的当然略过此步骤)
    sudo apt-get install ant

<!-- more -->

#### 安装独立SDK
下载[SDK](http://developer.android.com/sdk/index.html)

    tar zxvf path/to/android-sdk_r21.0.1-linux.tgz -C ~/DevTools
    cd ~/DevTools/android-sdk-linux/tools/
    ./android sdk

#### 配置环境变量
在~/.bashrc或者/etc/environemnt

    export PATH=$PATH:~/DevTools/android-sdk-linux/tools/:~/DevTools/android-sdk-linux/platform-tools/

#### 测试

    android list targets    #查看可用平台
    android create project –t 1 –n MyFirstApp –p ~/Development/android-project/MyFirstApp –a MainActivity –k com.example.myfirstapp
    android avd   #点击New，选择一个设备创建模拟器，完了点击Start启动模拟器 
    cd ~/DevTools/android-project/MyFirstApp
    ant debug   #debug模式打包
    adb install bin/MyFirstApp-debug.apk    #用adb安装到模拟器

具体教程参见[Building Your First App](http://developer.android.com/training/basics/firstapp/index.html)
