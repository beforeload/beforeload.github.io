常用软件的安装配置
================

系统环境：Ubuntu 13.04

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
11. 安装vim-powerline

### 安装zsh和oh-my-zsh

___安装 curl:___

    sudo apt-get install curl

然后:

    sudo apt-get install zsh

___可以通过curl方式安装：___

    curl -L https://github.com/robbyrussell/oh-my-zsh/raw/master/tools/install.sh | sh

___也可以通过wget：___

    wget --no-check-certificate https://github.com/robbyrussell/oh-my-zsh/raw/master/tools/install.sh -O - | sh

[参考文档](https://github.com/robbyrussell/oh-my-zsh)

### Ubuntu 终端透明化

Ctrl+Alt+T 打开终端，菜单 

    Edit->Profile Preference->Background->Transparent background

### 安装 chrome

___安装依赖:___

    sudo apt-get install (依赖包)

然后从google chrome官方网站上下载安装

    sudo dpkg -i google-chrome-stable_*.deb

终端输入`google-chrome`即可启动。

### 安装git

    sudo apt-get install git

___相关配置:___

    git config --global user.name beforeload
    git config --global user.email fe.daniel91@gmail.com
    git config --global color.diff auto
    git config --global color.branch auto
    git config --global color.ui true
    git config --global color.interactive auto
    git config --global color.status auto
    git config --global push.default current
    git config --global core.editor vim
    git config --global core.autocrlf off
    git config --global merge.tool vimdiff

_查看配置_

___查看所有配置:___

    git config --list

___查看某个配置:___

    git config user.name

或者 

    cat ~/.gitconfig

### 安装VLC视频播放器

    sudo apt-get install vlc

终端输入`vlc`启动，在菜单栏

    Tools-> Preference -> Subtitles & OSD 修改Default encoding为Universal(UTF-8),或者GBK
    
还可以设置相关中文字体（中文可选）。

然后在右上角:

    System Settings -> Details -> Default Applications -> Video选择VLC media player

这样打开相关视频文件，可以直接使用VLC播放。


### 安装sougou + fcitx

    sudo add-apt-repository ppa:fcitx-team/nightly
    sudo apt-get update
    sudo apt-get install fcitx-sogoupinyin

然后在``System Settings -> Language Support -> Keyboard input method system`` 中选择 fcitx。


___[可选]安装皮肤：___

下载[fcitx-sougou-skin](http://pan.baidu.com/share/link?shareid=591450&uk=4060302862)

    sudo dpkg -i fcitx-skin-sogou_0.0.2_all.deb

在fcitx外观中选择sougou即可。

最后注销，重新登录，输入法生效。

### 安装配置VIM

    sudo apt-get install vim

___配置：___

安装vundle

    git clone https://github.com/gmarik/vundle.git ~/.vim/bundle/vundle

用[我的.vimrc配置文件](https://github.com/beforeload/myconf/blob/master/.vimrc)
覆盖`~/.vimrc`文件即可

在vim中执行`:BundleInstall`,自动安装vim插件。

Powerline中下面的箭头可能存在字体问题，解决的办法是给字体打补丁。

### 安装Node.js

安装依赖

    sudo apt-get install build-essential libssl-dev

编译安装

    mkdir node && cd node
    git clone https://github.com/joyent/node.git
    ./configure
    make 
    make install

### 安装锐捷校园网客户端

下载[Linux SU V 1.01](http://ncs.hust.edu.cn/download/soft/Linux\ SU\ V1.01版.rar)

### 安装unrar

    sudo apt-get install unrar

    unrar ***.rar
    tar -zxvf ***.tar.gz

    cd rjsupplicant/

查看设置

    sudo ./rjsupplicant.sh -h

设置好后启动

    sudo ./rjsupplicant.sh

### 安装配置JDK

相关安装步骤已经在另外[上一篇博文－分布式开发环境搭建](/distributed-dev-environment.html)中做出介绍。

 在ubuntu上每次都要去找``vim-powerline``的安装及配置太麻烦，而Ubuntu上的字体每次都需要使用fontpather打一次补丁，不然``let g:Powerline_symbols = 'fancy'``没有效果，下面的箭头无法显示出来。

![img](http://i.stack.imgur.com/T6APG.png)
![img](http://i.stack.imgur.com/4R3XS.png)
![img](http://i.stack.imgur.com/VTB31.png)
![img](http://i.stack.imgur.com/HhgMo.png)

### 安装Powerline

(1) 首先安装python-pip和git:

```
sudo apt-get install python-pip git
```

(2) 安装Powerline

分两种情况：    
1) 对于个人用户

在控制台中运行：

```
pip install --user git+git://github.com/Lokaltog/powerline
```

修改``~/.profile``，将``~/.local/bin``加到``$PATH``中，在末尾加上：

```
if [ -d "$HOME/.local/bin" ]; then
    PATH="$HOME/.local/bin:$PATH"
fi
```

2) 对于系统用户

在控制台中运行：

```
su -c 'pip install git+git://github.com/Lokaltog/powerline'
```

(3) 字体的安装

对于Ubuntu的用户来说，安装字体再简单不过了，只需要双击文件，点击Install即可。这里不在赘述。

不过，我发现从[powerline-fonts](https://github.com/Lokaltog/powerline-fonts)上下载的字体不能直接用，还是需要配置，配置过程如下：

_Fontconfig：(推荐)_

```
sudo apt-get install fontforge python-fontforge
```

1) 对于个人用户

```
mkdir -p ~/.fonts/ && cd ~/.fonts/
wget https://github.com/Lokaltog/powerline/raw/develop/font/PowerlineSymbols.otf 
wget https://raw.github.com/Lokaltog/powerline-fonts/master/SourceCodePro/Sauce%20Code%20Powerline%20Black.otf
~/.vim/bundle/vim-powerline/fontpatcher/fontpatcher *.otf
mkdir -p ~/.config/fontconfig/conf.d/ cd ~/.config/fontconfig/conf.d/ 
wget https://github.com/Lokaltog/powerline/raw/develop/font/10-powerline-symbols.conf
fc-cache -vf ~/.fonts
```

2) 对于系统用户

```
wget https://github.com/Lokaltog/powerline/raw/develop/font/PowerlineSymbols.otf
wget https://raw.github.com/Lokaltog/powerline-fonts/master/SourceCodePro/Sauce%20Code%20Powerline%20Black.otf
wget https://github.com/Lokaltog/powerline/raw/develop/font/10-powerline-symbols.conf
/etc/vim/bundle/vim-powerline/fontpatcher/fontpatcher *.otf
sudo mv *.otf /usr/share/fonts/
sudo fc-cache -vf
sudo mv 10-powerline-symbols.conf /etc/fonts/conf.d/
```

如果嫌麻烦，后面有我的打过补丁的字体可以直接下载使用。

(3) 配置.vimrc

_Vim状态栏的配置_

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

(4) 卸载

1) 对于个人用户：

    pip uninstall powerline

2) 对于系统用户：

    su -c 'pip uninstall powerline'

(5) 附上我的字体配置文件
1. [字体](https://github.com/beforeload/dotfiles/tree/master/fonts)
2. [.vimrc](https://raw.github.com/beforeload/dotfiles/master/.vimrc)

