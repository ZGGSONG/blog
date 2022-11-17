---
title: 好用的终端复用神器 Tmux
date: 2020-06-27T19:42:00+08:00
lastmod: 2022-07-01T11:53:28+08:00
description: ""
featuredImage: ""
categories:
- toss
tags:
- tmux
---

## 前言

许久之前就听说过`tmux`，但是一直没上手，直到最近需要一直在 linux 下完成一些任务，我才切实感受到了`tmux`的优点：任意分屏、保存工作

就单单这两点，就足够实用了。分屏，曾今还十分痴迷`i3wm`和`dwm`这样的窗口管理工具，尤其是`dwm`的操作逻辑，大大提升`linux`工作效率。其他详情可以查看 [阮一峰大佬的文章](http://www.ruanyifeng.com/blog/2019/10/tmux.html) 非常的通俗易懂且详细

## 效果

![分屏](https://cdn.zggsong.cn/2020/06/28/e788a9d863b3f.gif)

![保存工作区](https://cdn.zggsong.cn/2020/06/28/c3bb07f43d184.gif)

## 安装
```
brew install tmux  # mac

sudo apt install tmux  # Debian/Ubuntu

sudo yum install tmux  # CentOS

```

## 美化

有一说一，原生的的确不怎么好看，一个离不开 ohmyzsh 的人怎么能忍，ohmytmux 了解一下

```shell
cd

git clone https://github.com/gpakosz/.tmux.git

ln -s -f .tmux/.tmux.conf

cp .tmux/.tmux.conf.local .

#开启鼠标
set-option -g mouse on
```
---

![](https://cdn.zggsong.cn/2020/06/28/5547af52c7a8c.png!webp)

关于字体 若实现如上效果，最好使用`nerd fonts`这里推荐一款我一直在使用的 
[Fira Code Regular Nerd Font](https://github.com/ZGGSONG/vim/raw/master/Fira%20Code%20Regular%20Nerd%20Font%20Complete.ttf")

同时修改`~/.tmux.conf.local`153-156 行，删除前面的`#`
```
tmux_conf_theme_left_separator_main='\uE0B0'  # /!\ you don't need to install Powerline
tmux_conf_theme_left_separator_sub='\uE0B1'   #   you only need fonts patched with
tmux_conf_theme_right_separator_main='\uE0B2' #   Powerline symbols or the standalone
tmux_conf_theme_right_separator_sub='\uE0B3'  #   PowerlineSymbols.otf font, see README.md
```

## 使用

tmux 采用 C/S 模型构建，输入 tmux 命令就相当于开启了一个服务器，此时默认将新建一个会话，然后会话中默认新建一个窗口，窗口中默认新建一个面板。会话、窗口、面板之间的联系如下：
一个 tmux `session`（会话）可以包含多个`window`（窗口），窗口默认充满会话界面，因此这些窗口中可以运行相关性不大的任务。
一个`window`又可以包含多个`pane`（面板），窗口下的面板，都处于同一界面下，这些面板适合运行相关性高的任务，以便同时观察到它们的运行情况。

### Tmux 会话命令
```
#新建会话
tmux # 新建一个无名称的会话
tmux new -s demo # 新建一个名称为 demo 的会话

#断开当前会话
tmux detach # 断开当前会话，会话在后台运行

#进入之前的会话
tmux a # 默认进入第一个会话
tmux a -t demo # 进入到名称为 demo 的会话

#关闭会话
tmux kill-session -t demo # 关闭 demo 会话
tmux kill-server # 关闭服务器，所有的会话都将关闭

#查看所有会话
tmux list-session # 查看所有会话
tmux ls # 查看所有会话，提倡使用简写形式
```
切换会话也可以使用`ctrl b` + `w`/`s`查看会话列表，按上下键 (⬆︎⬇︎) 或者鼠标滚轮，可选中目标会话，按左右键（⬅➡）可收起或展开会话的窗口，选中目标会话或窗口后，按回车键即可完成切换。

![](https://cdn.zggsong.cn/2020/06/28/11604eef85606.png!webp)

### Tmux 快捷指令

1. 常用指令

```shell
tmux new -s "name"          #创建 session
tmux attach -t "name"       #重新连接 session
tmux switch -t "name"       #切换会话
tmux kill-session -t "name" #杀死会话
ctrl b %   #横向分屏
ctrl b "   #纵向分屏
ctrl b o   #选择窗格
ctrl d     #关闭
ctrl b d   #隐藏所有 session
ctrl b s/w #选择 session
ctrl b m   #打开/关闭鼠标控制
```

2. 系统指令

| 前缀 | 指令 | 描述 |
|  :--- |  :--- |  :--- |
Ctrl+b | ? | 显示快捷键帮助文档
Ctrl+b | d | 断开当前会话
Ctrl+b | D | 选择要断开的会话
Ctrl+b | Ctrl+z | 挂起当前会话
Ctrl+b | r | 强制重载当前会话
Ctrl+b | s | 显示会话列表用于选择并切换
Ctrl+b | : | 进入命令行模式，此时可直接输入 ls 等命令
Ctrl+b | [ | 进入复制模式，按 q 退出
Ctrl+b | ] | 粘贴复制模式中复制的文本
Ctrl+b | ~ | 列出提示信息缓存

3. 窗口 (window) 指令

|前缀	|   指令	|   描述|
|  :--- |  :--- |  :--- |
Ctrl+b	|   c	|   新建窗口
Ctrl+b	|   &	|   关闭当前窗口（关闭前需输入 y or n 确认）
Ctrl+b	|   0~9	|   切换到指定窗口
Ctrl+b	|   p	|   切换到上一窗口
Ctrl+b	|   n	|   切换到下一窗口
Ctrl+b	|   w	|   打开窗口列表，用于且切换窗口
Ctrl+b	|   ,	|   重命名当前窗口
Ctrl+b	|   .	|   修改当前窗口编号（适用于窗口重新排序）
Ctrl+b	|   f	|   快速定位到窗口（输入关键字匹配窗口名称

4. 面板 (pane) 指令

|前缀	|   指令	|   描述|
|  :--- |  :--- |  :--- |
Ctrl+b	|   "	|   当前面板上下一分为二，下侧新建面板
Ctrl+b	|   %	|   当前面板左右一分为二，右侧新建面板
Ctrl+b	|   x	|   关闭当前面板（关闭前需输入 y or n 确认）
Ctrl+b	|   z	|   最大化当前面板，再重复一次按键后恢复正常（v1.8 版本新增）
Ctrl+b	|   !	|   将当前面板移动到新的窗口打开（原窗口中存在两个及以上面板有效）
Ctrl+b	|   ;	|   切换到最后一次使用的面板
Ctrl+b	|   q	|   显示面板编号，在编号消失前输入对应的数字可切换到相应的面板
Ctrl+b	|   {	|   向前置换当前面板
Ctrl+b	|   }	|   向后置换当前面板
Ctrl+b	|   Ctrl+o	|   顺时针旋转当前窗口中的所有面板
Ctrl+b	|   方向|   键	移动光标切换面板
Ctrl+b	|   o	|   选择下一面板
Ctrl+b	|   空格|   键	在自带的面板布局中循环切换
Ctrl+b	|   Alt+方向键	|   以 5 个单元格为单位调整当前面板边缘
Ctrl+b	|   Ctrl+方向键	|   以 1 个单元格为单位调整当前面板边缘（Mac 下被系统快捷键覆盖）
Ctrl+b	|   t	|   显示时钟

## 参考

- [「Tmux 使用手册」](http://louiszhai.github.io/2017/09/30/tmux/)
