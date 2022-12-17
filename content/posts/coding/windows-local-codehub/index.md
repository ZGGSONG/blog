---
title: "Windows 创建本地仓库"
date: 2021-12-17T14:26:55+08:00
description: "简单记录一下Windows搭建本地仓库"
featuredImage: ""
categories:
- coding
tags:
- git
- windows
---

## git 创建本地仓库

```shell
mkdir demo.git && cd demo.git

git init --bare --shared
```

> 主要针对 windows， linux 创建完直接使用即可

## 创建共享目录

![local_git_sharedir](https://cdn.zggsong.cn/2022/12/17/aa92bb42a9a2d.png)

## 添加至本地仓库

```shell

git remote add origin file://127.0.0.1/demo.git

or  

git remote add origin file:///C:/xxx/demo.git

git push

```

## 克隆仓库

```
git clone file://xx.xx.xx.xx/demo.git

```

> **如果局域网中有的设备无法克隆代码，则只需要先连接一次局域网中的主机即可**

- win + R

- 输入 `\\xx.xx.xx.xx`(局域网中主机 `ip`)

- 输入用户名密码即可

- git clone file://xx. xx. xx. xx/demo. git

随后就是正常的 git 使用逻辑了
