---
title: Win 10 系统安装
date: 2019-08-29T22:14:00+08:00
lastmod: 2020-03-21T00:29:24+08:00
description: ""
featuredImage: ""
categories:
- tutorial
tags:
- windows
---

> 本文主要针对小白

### 介绍

> 重装系统是指对计算机的操作系统进行重新的安装。当用户错误操作或遭受病毒、木马程序的破坏，系统中的重要文件就会受损导致错误，甚至崩溃无法启动，因此不得不重新安装。不过重装系统大都是系统缓存垃圾过多，或者文件系统混乱才进行重装系统。
>一般系统安装有全新安装和覆盖安装，一般推荐全新安装。

## 安装方式

### Ghost 重装

Ghost 重装是最简单最方便的重装系统方式，几乎所有的电脑门店和维修店和非官方的系统光盘都是基于 Ghost 进行重装的，Ghost 重装具有操作方便，重装速度快，方法简单的优点。（系统已激活，常用软件已安装，驱动也会自动打上）
但是，Ghost 版系统一般都是经过第三方定制，不法分子，可能会留有后门，没有安全保障。

### U 盘重装

U 盘装系统是目 前一个较为方便的重装系统的方法，只需下载 U 盘启动盘制作工具制作 U 盘启动盘，然后在进入系统时设置 U 盘启动即可，制作工具可以到网上下载。

### 硬盘重装

下载 ISO，然后解压到非系统盘，接着运行其中的 setup.exe 程序，安装时选择高级，选择盘符。之后的工作是全自动无人值守方式。正版镜像如果在硬盘上进行安装，将不会格式化原系统盘，而是将原有文件移动到"Windows.old"文件夹，但是不能回到原来的操作系统（原版系统不集成驱动，需要提前备份驱动，或者下载驱动安装软件（如驱动精灵，360 驱动大师）网卡版。）---win10 会自动安装所需驱动

### 光盘安装

使用光盘重装系统是较为古老的使用方法，直接利用光盘直接启动选择重装。首先我们在 BIOS 中设置成光驱启动或者按相关快捷键进入启动菜单中选择光驱启动。
目前的笔记本电脑包括台式机也很少有装配光驱的了

## 准备工作

### 工具

**U 盘（4GB 以上，一般 8GB 即可）**、**一台能正常运行的电脑**

### 制作启动盘

- 下载 Microsoft 原版镜像文件：**https://msdn.itellyou.cn/**访问该网站，下载原版镜像
复制链接（图中④标记处）至迅雷进行下载

![下载镜像][1]

- 下载 ultraiso（软碟通）U 盘制作软件
**https://cn.ultraiso.net/xiazai.html**	下载试用即可
> 注册码  王涛 7C81-1689-4046-626F

注：我们理应支持正版，此处再放一个开源软件：
**https://github.com/pbatard/rufus/releases/download/v3.6/rufus-3.6.exe**

- 开始制作

先插上 U 盘
来到软碟通界面后，点击左上角文件，找到你磁盘中的。iso 文件，然后，双击进行加载，如图所示
等待其写入成功即可完成系统安装盘的制作

![2.png][2]

![3.png][3]

![4.png][4]

### BIOS 热键

![BIOS 热键][5]

### 更改 BIOS

#### UEFI 和 Legacy 的区别

[点击查看](https://blog.csdn.net/alianada/article/details/82977957)

#### Legacy+MBR

- UEFI/Legacy Boot 选项，选择 Both 或者 Legacy Only，CSM 选择 YES

#### UEFI+GPT

- UEFI/Legacy Boot 选项，选择 Both 或者 UEFI only

- 进入 Security—Secure Boot—Disabled。如果不修改 Secure boot 选项为 Disabled，在引导时可能会出现报错。

## 开始安装

开机按热键进入启动选项，选择 U 盘进行系统安装

![6.jpg][6]

![7.jpg][7]

![8.jpg][8]

![9.jpg][9]

![10.jpg][10]

![11.jpg][11]

### 此处可能会碰到磁盘无法安装的错误

![12.png][12]

> Shift+f10 调出命令提示符
> 然后输入 diskpart，
> 再输入 list disk，即可显示所有磁盘
> 随后在输入 select disk *（*：数字，你想要安装的磁盘的编号）
> 再输入 clean 进行格式化
> 再输入 convert gpt（根据你引导方式）
> 最后在输入 exit 退出

![13.png][13]

然后点击刷新，在选择磁盘就能够安装系统了，一直下一步即可

![14.jpg][14]

### 安装成功

![15.jpg][15]

接下来就跟你们自己刚买电脑的时候一样进行一些设置就好了

## 注意

### 安装软件的选择

目前市面上有很多诸如大白菜、老毛桃、黑鲨等 U 盘制作工具，制作时向 U 盘里集成了 PE 以及其他工具，也是挺方便的，但是其中都夹杂着很多广告，导致系统安装成功后，各种软件、游戏全部自动安装，有把系统搞得乱糟糟的，如果使用的话，请先百度如何关闭***工具内自带广告。

### 系统选择

在选择安装系统的版本时，最好选择家庭版，那样安装好的话，系统直接是激活的，而且也是正版的（针对近几年买的大多数笔记本电脑）
如果非要选择专业版的话也是可以的，只不过进入系统后系统，系统是未激活的状态
这边也提供数字激活软件：

[HWIDGen_60.01.7z](https://github.com/ZGGSONG/HWIDGen/blob/5485e21fa5c0b25f21642f55390cb6c078d970d9/HWIDGen_60.01.7z)

> 下载慢可以使用 [https://ghproxy.com/](https://ghproxy.com/) 进行下载

还是建议选择家庭版，支持正版比较好，至于专业版相较于家庭版，也就多一个自带虚拟机和一些其他的小功能，具体的自行百度，对普通用户来说没有影响~

  [1]: https://cdn.zggsong.cn/2020/03/21/88a98aa1055b9.png!webp
  [2]: https://cdn.zggsong.cn/2020/03/21/1064df196330d.png!webp
  [3]: https://cdn.zggsong.cn/2020/03/21/5b2f86c31cf52.png!webp
  [4]: https://cdn.zggsong.cn/2020/03/21/6994d3d529c53.png!webp
  [5]: https://cdn.zggsong.cn/2020/03/21/93da8c4e5242d.png!webp
  [6]: https://cdn.zggsong.cn/2020/03/21/24e041554d4e0.jpg!webp
  [7]: https://cdn.zggsong.cn/2020/03/21/7e4d087aaae52.jpg!webp
  [8]: https://cdn.zggsong.cn/2020/03/21/741bc9dff84b6.jpg!webp
  [9]: https://cdn.zggsong.cn/2020/03/21/1756f7b869e0b.jpg!webp
  [10]: https://cdn.zggsong.cn/2020/03/21/980de6e7563b8.jpg!webp
  [11]: https://cdn.zggsong.cn/2020/03/21/cf98ee6986ec7.jpg!webp
  [12]: https://cdn.zggsong.cn/2020/03/21/82a902a7956b1.png!webp
  [13]: https://cdn.zggsong.cn/2020/03/21/bc357638aff79.png!webp
  [14]: https://cdn.zggsong.cn/2020/03/21/39abef69e3faf.jpg!webp
  [15]: https://cdn.zggsong.cn/2020/03/21/cbed88e23b927.jpg!webp
