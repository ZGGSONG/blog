---
title: manjaro 风扇持续开启
date: 2019-11-18T10:47:00+08:00
lastmod: 2020-03-21T01:23:42+08:00
description: ""
featuredImage: ""
categories:
- toss
tags:
- linux
- manjaro
---

## 前言
风扇持续开启

## 使用大黄蜂 bumblebee
以上的配置完成后独显会持续运作虽性能较好但耗电很快风扇也会转个不停，由于 Nvidia 在 Linux 平台未提供类似 Windows 上擎天柱那样的双显交火技术，因此这里转而使开源的 bumblebee，需要用到独显时再开启独显。

### 安装
```shell
sudo pacman -S bumblebee mesa xf86_vidoe_intel lib32-virtualgl lib32-nvidia-utils
```

#### 要使用 Bumblebee，必须将常规用户添加到 bumblebee 组中：
```shell
sudo gpasswd -a user bumblebee
```

#### 启用 bumblebeed.service 服务
```shell
# sudo systemctl enable bumblebeed.service
```
### 配置
#### 去除独显配置
```shell
sudo mv /etc/X11/xorg.conf /etc/X11/xorg.conf.bak
```
#### 去除 sddm 配置
```shell
sudo vim /usr/share/sddm/scripts/Xsetup
```
#### 注释掉下面两行
```shell
xrandr --setprovideroutputsource modesetting NVIDIA-0
xrandr --auto
```

#### 这时重启后可能发现独显还是持续运行，这里还需要安装 bbswitch
```shell
pacman -S bbswitch
```
#### 重启后使用 lspci 查看显卡状态若为 rev ff 则为集显驱动，若要使用独显则使用以下命令：
```shell
optirun application
```
#### 打开 NVIDIA Settings 面板命令稍多一些：
```shell
optirun -b none nvidia-settings -c：8
```

但每次打开需要独显支持的应用还得加上 optirun 很是麻烦，我们可以在/usr/share/applications 目录下编辑应用图标在 EXEC 后加上 optirun，这样以后每次使用图形界面打开应用时就会使用独显了。

### 显卡性能测试

安装了 mesa 后可以使用 optirun glxgears -info 命令查看独显性能

## 转自
[Arch Linux 安装+KDE 桌面+双显卡驱动一站式解决方案 fulgens](https://www.jianshu.com/p/05bf79b1878b)
