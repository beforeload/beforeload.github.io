title: My Software Installation and Configuration on Ubuntu 13.04
date: 2013-06-22 21:23:50
update: 2013-10-19 11:37:52
categories: Linux
tags: 
- Ubuntu
- Linux
---

## 在Ubuntu 13.04上常用软件的安装配置

安装完Ubuntu 13.04 后，我的一些软件安装和配置过程。

1. 安装zsh和oh-my-zsh
2. 终端透明化
3. 安装chrome
4. 安装git
5. 安装VLC播放器
6. 安装fcitx + 搜狗拼音
7. 安装vim
8. 安装Node.js
9. 安装锐捷校园网客户端
10. 安装配置jdk

<!-- more -->

#### 安装zsh和oh-my-zsh

___安装 curl:___

    sudo apt-get install curl

然后:

    sudo apt-get install zsh

___可以通过curl方式安装：___

    curl -L https://github.com/robbyrussell/oh-my-zsh/raw/master/tools/install.sh | sh

___也可以通过wget：___

    wget --no-check-certificate https://github.com/robbyrussell/oh-my-zsh/raw/master/tools/install.sh -O - | sh

[参考文档](https://github.com/robbyrussell/oh-my-zsh)

#### Ubuntu 终端透明化

Ctrl+Alt+T 打开终端，菜单 

    Edit->Profile Preference->Background->Transparent background

#### 安装 chrome

___安装依赖:___

    sudo apt-get install libudev0

然后从google chrome官方网站上下载安装

    sudo dpkg -i google-chrome-stable_*.deb

终端输入`google-chrome`即可启动。

#### 安装git

    sudo apt-get install git

___相关配置:___

    git config --global user.name=beforeload
    git config --global user.email=fe.daniel91@gmail.com
    git config --global color.diff=auto
    git config --global color.branch=auto
    git config --global color.ui=true
    git config --global color.interactive=auto
    git config --global color.status=auto
    git config --global push.default=current
    git config --global core.editor=vim
    git config --global core.autocrlf=off
    git config --global merge.tool=vimdiff

#####查看配置#####

___查看所有配置:___

    git config --list

___查看某个配置:___

    git config user.name

或者 

    cat ~/.gitconfig

#### 安装VLC视频播放器

    sudo apt-get install vlc

终端输入`vlc`启动，在菜单栏

    Tools-> Preference -> Subtitles & OSD 修改Default encoding为Universal(UTF-8)
    
还可以设置相关中文字体（中文可选）。

然后在右上角:

    System Settings -> Details -> Default Applications -> Video选择VLC media player

这样打开相关视频文件，可以直接使用VLC播放。


#### 安装sougou + fcitx

    sudo add-apt-repository ppa:fcitx-team/nightly
    sudo apt-get update
    sudo apt-get install fcitx-sogoupinyin

然后在``System Settings -> Language Support -> Keyboard input method system`` 中选择 fcitx。


___[可选]安装皮肤：___

下载[fcitx-sougou-skin](http://pan.baidu.com/share/link?shareid=591450&uk=4060302862)

    sudo dpkg -i fcitx-skin-sogou_0.0.2_all.deb

在fcitx外观中选择sougou即可。

最后注销，重新登录，输入法生效。

#### 安装配置VIM

    sudo apt-get install vim

___配置：___

安装vundle

    git clone https://github.com/gmarik/vundle.git ~/.vim/bundle/vundle

用[我的.vimrc配置文件](https://github.com/beforeload/myconf/blob/master/.vimrc)
覆盖`~/.vimrc`文件即可

在vim中执行`:BundleInstall`,自动安装vim插件。

Powerline中下面的箭头可能存在字体问题，解决的办法是给字体打补丁。

#### 安装Node.js

安装依赖

    sudo apt-get install build-essential libssl-dev

编译安装

    mkdir node && cd node
    git clone https://github.com/joyent/node.git
    ./configure
    make 
    make install

#### 安装锐捷校园网客户端

下载[Linux SU V 1.01](http://ncs.hust.edu.cn/download/soft/Linux\ SU\ V1.01版.rar)

安装unrar

    sudo apt-get install unrar

    unrar ***.rar
    tar -zxvf ***.tar.gz

    cd rjsupplicant/

查看设置

    sudo ./rjsupplicant.sh -h

设置好后启动

    sudo ./rjsupplicant.sh

#### 安装配置JDK

相关安装步骤已经在另外[一篇博文](//beforeload.github.io/2013/06/29/building-distributed-systems-development-environment/)中做出更新。
