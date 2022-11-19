---
title: 浅尝 Zerotier
date: 2022-04-26T00:31:00+08:00
lastmod: 2022-04-29T00:00:34+08:00
description: ""
featuredImage: ""
categories:
- network
tags:
- zerotier
---

## 前言

在上 [一篇文章](../../life/synology-ds220+/) 中说到我已经做了 frp 和反向代理把内网部分端口映射到公网，也都添加了 ssl 证书。但是始终有一个很让人头疼的事情——我的小水管只有 5M，这个限制有时候就比较头疼（应证了那句：我可以不用，但不能没有）  
很早之前就了解到 Zerotier  

需求：公网环境中访问内网服务  

解决方案:

1.申请公网 IP+DDNS+端口映射直接访问  
优点：啥都好  
缺点：基本申请不到  

2.frp 等类似工具通过一台有公网 ip 的 VPS 转发所有的流量  
优点：基于自己的水管稳不稳  
缺点：小水管不是速度受限就是流量限制，把内网服务映射到公网，还得添加 ssl 证书，不过也可以给添加泛域名证书  

3.Zerotier 可以将不同设备添加到一个同一个虚拟局域网里  
优点：速度基本不受限，服务也没有映射到公网，不用每次开一个服务都需要去解析，做反向代理  
缺点：每台设备都要装 Zerotier 软件  

## 安装

> 记录一下，相关教程网上也很多，更多相关的资料，请登陆 [官方网站](https://docs.zerotier.com/)

### 官网注册

首先去 [官网](https://www.zerotier.com/) 注册登录创建一个`NetWork`就会得到一个`Network ID`，保持`Access Control`为`Private`

### 群晖端安装 Zerotier

> 由于 Synology 的 DSM 7 不允许第三方应用程序以 root 用户身份运行。所以 DSM7 以上的版本将只能使用 docker 的方式来安装 zerotier

首先可以使用下面`lsmod`命令查看`tun`模块是否已载入系统，若没有则进行后面的步骤
```
# 检查 tun.ko 是否存在
lsmod | grep tun
```

```
ssh username@nas-ip

sudo -i

# 创建一个 /usr/local/etc/rc.d/tun.sh 脚本 到 /dev/net/tun 目录下：
echo -e '#!/bin/sh -e \ninsmod /lib/modules/tun.ko' > /usr/local/etc/rc.d/tun.sh

# 给 tun 脚本可执行权限
chmod +x /usr/local/etc/rc.d/tun.sh

# 执行
/usr/local/etc/rc.d/tun.sh

# 检查一下 TUN
ls /dev/net/tun
/dev/net/tun
```
系统加载 TUN 模块以后  

安装镜像创建容器，就直接在终端进行了

```
# 创建宿主机目录
mkdir /volume1/docker/zerotier-one

# 下载 zerotier/zerotier-synology 最新镜像
docker pull zerotier/zerotier-synology:latest

# 创建 zerotier/zerotier-synology 容器
docker run -d           \
  --name zt             \
  --restart=always      \
  --device=/dev/net/tun \
  --net=host            \
  --cap-add=NET_ADMIN   \
  --cap-add=SYS_ADMIN   \
  -v /volume1/docker/zerotier-one:/var/lib/zerotier-one zerotier/zerotier-synology:latest
```

查看节点状态
```shell
docker exec -it zt zerotier-cli status
```

加入您的网络
```shell
docker exec -it zt zerotier-cli join `你的 NetworkID`
```

授权网络上的 NAS 然后查看网络状态
```shell
docker exec -it zt zerotier-cli listnetworks
```

查看节点信息
```shell
docker exec -it zt zerotier-cli listpeers
```

**添加成功以后则要登录 Zerotier 控制台，即可看到网络列表多了一条未授权加入的网络，给个名字区分设备，勾选允许其加入即可**

> 上面安装方法来自 —— [官方群晖安装 Zerotier 文档](https://docs.zerotier.com/devices/synology/)

### windows 端安装

去 [官网下载](https://www.zerotier.com/download/) 对应设备的 Zerotier 客户端
> 不知道是不是最新版的问题，UI 没有加入虚局域网的选项 (2022-04-25)，直接使用`CMD`执行命令即可，更多可查看 [官方文档](https://zerotier.atlassian.net/wiki/spaces/SD/pages/29065282/Command+Line+Interface+zerotier-cli)：

```shell
#查看
zerotier-cli status

#加入虚拟局域网
zerotier-cli jion `你的 NetworkID`

#查看网络状态
zerotier-cli listnetworks
```
**完成以上步骤后，需登录 Zerotier 控制台，允许设备加入**

至此，windows 就可以通过 Zerotier 控制台中群晖设备的虚拟局域网 IP 访问群晖设备。

### 其他设备安装

1.其他设备可去官网下载 [Zerotier 下载](https://www.zerotier.com/download/)  
2.添加虚拟局域网 ID  
3.控制台允许  

## 搭建 Moon 节点

Zerotier Moon 的目的是加速 Zerotier 的连接，让虚拟网络更稳定的运行，主要是为了加速连接，[SMZDM](https://post.smzdm.com/p/adwrepgk) 有介绍：  如果设备间桥接网络延迟超过 30ms 且时断时续就表示设备间之间的 P2P 隧道没有打通，一般移动网络与电信或者联通之间使用 UDP 穿透会失败。这时候就需要自建 Moon 节点进行中专加速。  

先看下有无 moon 节点的前后延迟对比

![moon_compare](https://cdn.zggsong.cn/2022/04/26/6edd7461b644d.png!webp)

我是选择我的国内小水管，同样是选择 [docker 安装](https://hub.docker.com/r/jonnyan404/zerotier-moon)

```shell
#下载镜像
docker pull jonnyan404/zerotier-moon

#创建宿主机目录
mkdir -p /opt/docker/zerotier-moon

#创建容器（最后为云主机的公网 IP，同时开放 9993 端口）
docker run --name zerotier-moon -d --restart always -p 9993:9993 -p 9993:9993/udp -v /opt/docker/zerotier-moon:/var/lib/zerotier-one jonnyan404/zerotier-moon -4 47.98.103.241
```
在`/opt/docker/zerotier-moon`目录下将`moons.d`下载下来，保存在群晖和 Windows 上分别添加 moon 节点

1.群晖上比较简单：将保存的`moons.d`目录放到群晖 Zerotier 宿主机的目录下后重启容器即可  

![moons.d](https://cdn.zggsong.cn/2022/04/25/120625605d6f3.png!webp)

2.windows 添加 moon 节点，首先在 moon 服务器服务器获取`moon_id`，其实就是保存的`moons.d`目录下的文件名去除前 6 个 0 的字符串  
也可以通过下面的命令查看`moon_id`

```shell
docker logs zerotier-moon
```

Cmd 或者 Terminal 中输入
```shell
#添加 moon 节点
zerotier-cli orbit moon_id moon_id

#查看
zerotier-cli listnetpeers
```
![win-zerotier](https://cdn.zggsong.cn/2022/04/25/da5a8ac0457e0.png!webp)

## 总结

简单测试了一下，基本能跑满带宽，不过目前实际使用的不多，问就是隔离居家办公，后续有啥心得体会再来掰扯掰扯。

4G 速度

![4G](https://cdn.zggsong.cn/2022/04/25/79ed213924339.png!webp)

有线连接速度  

![有线连接](https://cdn.zggsong.cn/2022/04/26/3fac8776bbf44.png!webp)

**参考**

- [轻松实现无公网 IP 高速远程访问 NAS！zerotier 从原理到实践](https://post.smzdm.com/p/apzd4mww/) 
- [黑群晖（DSM7）使用 docker 挂载 zerotier one 实现内网穿透](https://blog.csdn.net/cmf1055/article/details/124236282) 
- [docker 一键搭建 zerotier-moon 节点 ](https://www.cnblogs.com/jonnyan/p/12566647.html)
- [远程办公：ZeroTier 异地组网及私有 Moon 转发节点搭建](https://post.smzdm.com/p/adwrepgk)
