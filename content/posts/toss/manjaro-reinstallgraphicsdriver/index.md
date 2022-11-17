---
title: manjaro 重装显卡驱动 1080 分辨率
date: 2019-11-13T22:56:00+08:00
lastmod: 2021-03-21T21:04:01+08:00
description: ""
featuredImage: ""
categories:
- toss
tags:
- linux
- manjaro
---

## 前言

一直在使用 manjaro Linux，挺好用的，但是手贱重装了显卡驱动，装好之后显示分辨率没有 1920*1080 了，所以只能手动添加了

## 方法

```shell
# 19201080 是你想要的分辨率 60 是刷新率
cvt 1920 1080 60
 
# 输出一般是
1920x1080 59.96 Hz （CVT 2.07M9） hsync： 67.16kHz; pclk： 173.00 MHz
　　Modeline “1920x1080_60.00” 173.00 1920 2048 2248 2576 10801083 1088 1120 -hsync +vsync
 
# 设置 mode
xrandr --newmode "1920x1080_60.00" 173.00 1920 2048 2248 2576 1080 1083 1088 1120 -hsync +vsync
 
# 查看 VGA 编号
xrandr
 
# 增加 Mode
xrandr --addmode eDP1 "1920x1080_60.00"

#eDP1 是你的显示器名称（xrandr 命令可查）

xrandr --output eDP1 --mode "1920x1080_60.00"
```

在终端中操作过后即可，但是重启过后就还是会恢复，所以就给他添加到/etc/profile 里面

```shell
#进入/etc/profile 文件
sudo vim /etc/profile

#shift+g 进入文档末尾
#可能有所不同，是你命令行里 cvt 1920 1080 60 输出的内容

xrandr --newmode "1920x1080_60.00" 173.00 1920 2048 2248 2576 1080 1083 1088 1120 -hsync +vsync
xrandr --addmode eDP1 "1920x1080_60.00"
xrandr --output eDP1 --mode "1920x1080_60.00"

#保存退出
source /etc/profile
```
