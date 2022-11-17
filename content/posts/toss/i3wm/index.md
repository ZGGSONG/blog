---
title: i3wm 窗口管理器
date: 2020-01-16T16:53:00+08:00
lastmod: 2020-03-21T01:48:39+08:00
description: ""
featuredImage: ""
categories:
- toss
tags:
- i3wm
---

## 最终效果

![moment](https://cdn.zggsong.cn/2020/03/21/575bd46184484.png!webp)

i3wm，一个窗口管理器，琐碎的事情比较多，前后配置了两天次才完成。emmmmm~还是油管教程靠谱～～～

##  解决问题

- 安装中文输入法

- 安装 compton 渲染器

- 安装 variety

- 修改 xmodmap 

- 增加音频控制

## 美化

- 修改壁纸

```shell
sudo apt install feh

feh --bg-scale 图片路径
```

- 字体

> powerline fonts: Inconsolata for Powerline BlodForPowerline 

```shell
# clone
git clone https://github.com/powerline/fonts.git --depth=1
# install
cd fonts
./install.sh
# clean-up a bit
cd ..
rm -rf fonts
```

- dmenu & rofi
- dmenu_run -nb black -sb gray -l 10
- sudo apt install rofi
- rofi -show run
- 窗口渲染器
```shell
sudo apt install compton
```

- 截图工具
```shell
#安装
sudo apt install flameshot

#使用
flameshot gui
```

- i3-gaps on debian(ubuntu)
- 
> That's difficult!!!

```shell
#first
sudo apt install -y libxcb1-dev libxcb-keysyms1-dev libpango1.0-dev libxcb-util0-dev libxcb-icccm4-dev libyajl-dev libstartup-notification0-dev libxcb-randr0-dev libev-dev libxcb-cursor-dev libxcb-xinerama0-dev libxcb-xkb-dev libxkbcommon-dev libxkbcommon-x11-dev autoconf libxcb-xrm0 libxcb-xrm-dev automake libsnack2-dev libxcb-shape0-dev autoconf libev-dev build-essential git

#second
git clone https://www.github.com/Airblader/i3 i3-gaps
cd i3-gaps

#third
autoreconf --force --install
rm -rf build/
mkdir -p build && cd build/
../configure --prefix=/usr --sysconfdir=/etc --disable-sanitizers
make && sudo make install

#forth(in .config/i3/conf)
gaps inner 8
gaps outer 4
```

## ranger

> 配置与美化

```shell
#安装
sudo apt install ranger

#配置文件
ranger --copy-config=all

#高亮
sudo apt install highlight

#图片预览
sudo apt install w3m

#图片预览配置
vim ~/.config/ranger/rc.conf
设置 set use_preview_script true
```

> 图标插件
```shell
##安装插件
git clone https://github.com/alexanderjeurissen/ranger_devicons ~/.config/ranger/plugins/ranger_devicons

cd ~/.config/ranger/plugins/ranger_devicons

make install

##下载`nerd_font`字体
##个人比较推荐的一款字体

https://github.com/ryanoasis/nerd-fonts/raw/master/patched-fonts/Meslo/M/Regular/complete/Meslo%20LG%20M%20Regular%20Nerd%20Font%20Complete.ttf

mv Meslo LG M Regular Nerd Font Complete.ttf ~/.local/share/fonts/

##修改'Terminal`字体即可
```
> [其他配置](https://www.crs811.com/archives/814)

> 我的配置文件 [i3wm](https://github.com/ZGGSONG/Backup/tree/master/i3wm/)
