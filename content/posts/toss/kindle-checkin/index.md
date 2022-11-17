---
title: Kindle 咪咕阅读脚本打卡
date: 2020-07-02T15:42:00+08:00
lastmod: 2020-07-10T00:41:59+08:00
description: ""
featuredImage: ""
categories:
- toss
tags:
- crontab
- kindle
---

> 2020.7.7 咪咕官方开始检测！还是老老实实看吧#（喜极而泣）
> 2020.8 喝大了，忘了打卡 卒～ 真是棵小韭菜

## 前言

前几个月咪咕出的`Kindle`免费拿，白嫖党该上车的都上车了，能否成功下车，就看着 100 天了。但是事情一多，难免就会出现忘了打卡的情况，400 买这么老款的`Kindle`血亏，毕竟咸鱼也才一两百，那脚本打卡岂不是很香！


![](https://cdn.zggsong.cn/2020/06/10/136fe79f3891f.jpg!webp)

![](https://cdn.zggsong.cn/2020/06/14/26a52e7079a49.jpg!webp)

![](https://cdn.zggsong.cn/2020/07/02/b6840bb59e81d.jpeg!webp)


## 实操

- 电脑定时打开模拟器（当然也可以让模拟器一直跑在后台）

- 模拟器录制宏

- 模拟器定时执行宏

### 定时开启模拟器

先安装模拟器，这里我用的是`网易 MuMu`安卓模拟器

> 本人比较懒，一般不关电脑，也不想手动开启关闭

- 安装`crontab `

> crontab 命令常见于 Unix 和类 Unix 的操作系统之中，用于设置周期性被执行的指令

```
#我的 mac 上自带，没有的话，手动装一下

sudo apt install crontab    #ubuntu

brew install crontab   #mac
```

- 定时开启软件

```
#f1 f2 f3 f4 f5 program
#其中 f1 是表示分钟，f2 表示小时，f3 表示一个月份中的第几日，f4 表示月份，f5 表示一个星期中的第几天。program 表示要执行的程式。

#我的代码
crontab -e
01 00 * * * open -a /Applications/NemuPlayer.app
#这里的意思是每天`00:01`执行此操作
```

`crontab`使用详情 [crontab 实例+详解](https://blog.csdn.net/shaobingj126/article/details/5638006)

### 模拟器操作录屏

> `网易 MuMu`为例


![打开操作录屏](https://cdn.zggsong.cn/2020/07/02/f77c5615d975b.png!webp)

![新建录屏操作](https://cdn.zggsong.cn/2020/07/02/af1b97287a9f4.png!webp)

建立完成之后保存即可

### 自动关闭模拟器

- 新建自动关闭脚本

```
vim autoStop.sh

#!/bin/bash
echo -e "\n\nAuto Stop Software ... \n"
for assetstatus in "`ps -ef | grep NemuPlayer | awk '{print $2}' | sed -n '1'p`"
do
	echo "$assetstatus"
	kill -9 $assetstatus;
	echo "Nemuplayer shutdown [OK]"
done

#保存退出并给予执行权限
chmod u+x autoStop.sh
```

- 加入定时执行

```
crontab -e
30 00 * * * /Users/swj/Documents/shell/autoStop.sh
#这里的意思是每天`00:30`执行此操作
```
