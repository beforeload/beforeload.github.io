title: Vim Powerline installation on Ubuntu 13.10
date: 2014-01-02 12:53:58
categories: Linux
tags: [Linux, Vim]
---

# 在Ubuntu 13.10上安装vim-powerline

 最近又重装了一次ubuntu，发现在ubuntu上每次都要去找``vim-powerline``的安装及配置太麻烦，而Ubuntu上的字体每次都需要使用fontpather打一次补丁，不然``let g:Powerline_symbols = 'fancy'``没有效果，下面的箭头无法显示出来。所以整理这篇博客，记录配置过程的一些问题和解决方法。

{% img http://i.stack.imgur.com/T6APG.png Vim Powerline Normal %}
{% img http://i.stack.imgur.com/4R3XS.png Vim Powerline Insert %}
{% img http://i.stack.imgur.com/VTB31.png Vim Powerline Visual %}
{% img http://i.stack.imgur.com/HhgMo.png Vim Powerline Replace %}

<!-- more -->

## 一、安装Powerline

1. 首先安装python-pip和git:

```
sudo apt-get install python-pip git
```

2. 安装Powerline

分两种情况：    
1) 对于个人用户

在控制台中运行：

    pip install --user git+git://github.com/Lokaltog/powerline

修改``~/.profile``，将``~/.local/bin``加到``$PATH``中，在末尾加上：

```
if [ -d "$HOME/.local/bin" ]; then
    PATH="$HOME/.local/bin:$PATH"
fi
```

2) 对于系统用户

在控制台中运行：

    su -c 'pip install git+git://github.com/Lokaltog/powerline'

## 二、字体的安装

对于Ubuntu的用户来说，安装字体再简单不过了，只需要双击文件，点击Install即可。这里不在赘述。

不过，我发现从[powerline-fonts](https://github.com/Lokaltog/powerline-fonts)上下载的字体不能直接用，还是需要配置，配置过程如下：

1. Fontconfig：(推荐)

    sudo apt-get install fontforge
    sudo apt-get install fontforge-python

1) 对于个人用户

    mkdir -p ~/.fonts/ && cd ~/.fonts/
    wget https://github.com/Lokaltog/powerline/raw/develop/font/PowerlineSymbols.otf 
    wget https://raw.github.com/Lokaltog/powerline-fonts/master/SourceCodePro/Sauce%20Code%20Powerline%20Black.otf
    ~/.vim/bundle/vim-powerline/fontpatcher/fontpatcher *.otf
    mkdir -p ~/.config/fontconfig/conf.d/ cd ~/.config/fontconfig/conf.d/ 
    wget https://github.com/Lokaltog/powerline/raw/develop/font/10-powerline-symbols.conf
    fc-cache -vf ~/.fonts

2) 对于系统用户

    wget https://github.com/Lokaltog/powerline/raw/develop/font/PowerlineSymbols.otf
    wget https://raw.github.com/Lokaltog/powerline-fonts/master/SourceCodePro/Sauce%20Code%20Powerline%20Black.otf
    wget https://github.com/Lokaltog/powerline/raw/develop/font/10-powerline-symbols.conf
    /etc/vim/bundle/vim-powerline/fontpatcher/fontpatcher *.otf
    sudo mv *.otf /usr/share/fonts/
    sudo fc-cache -vf
    sudo mv 10-powerline-symbols.conf /etc/fonts/conf.d/

如果嫌麻烦，后面有我的打过补丁的字体可以直接下载使用。

## 三、配置.vimrc

1. Vim状态栏的配置

在``~/.vimrc``或者``/etc/vim/vimrc``文件中进行配置：

```
set encoding=utf-8
set fillchars+=stl:\ ,stlnc:\
set laststatus=2
set rtp+=$HOME/.local/lib/python2.7/site-packages/powerline/bindings/vim/
set -g default-terminal "screen-256color"
set t_Co=256
let g:Powerline_symbols = 'fancy'
```

后面附上我的vimrc相关配置。

## 四、卸载

1) 对于个人用户：

    pip uninstall powerline

2) 对于系统用户：

    su -c 'pip uninstall powerline'

### 附上我的字体配置文件
1. [字体](https://github.com/beforeload/dotfiles/tree/master/fonts)
2. [.vimrc](https://raw.github.com/beforeload/dotfiles/master/.vimrc)
