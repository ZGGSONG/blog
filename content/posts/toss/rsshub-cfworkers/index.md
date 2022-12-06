---
title: "RSSHub With Cf Workers"
date: 2022-11-28T16:56:17+08:00
description: "使用 cf workers 反向代理 rsshub.app"
featuredImage: ""
draft: true
categories:
- toss
tags:
- rsshub
- cloudflare
- reflare
---

## 前言

前些年刚接触 RSS 的时候了解到 [DIYgod](https://diygod.me/) 的项目 [RSSHub](https://github.com/DIYgod/RSSHub)，使用起来不要太香绝大多数平台都可以订阅起来，不用再接受平台精准推送的垃圾信息了。也支持自建服务，当时还 [水了一篇文章](../../tutorial/dockerhub-install/) 但是实际使用起来发现国外的众多网站，或者说被墙了的网站还是没法订阅起来，后来迁移 RSSHub 到国外服务器上解决了这个问题，上次阿里云老用户优惠冲了几年的，国外服务器就没再续，毕竟也不便宜，梯子开一个封一个，也不想搞了，还是白嫖比较香，当然白嫖得花点时间，所以下面我将简单介绍一下白嫖的步骤。

## cf workers

> 无服务器代码，简单理解成免费的云代理脚本

CloudFlare Workers 每日免费请求次数有 10w 完全够用，只是因为好多用梯子挂了就用 cf 转发流量，默认添加服务给的域名 `*.workers.dev` 也早就进入了 GFW 名单，所以白嫖还得有个解析到 cf 的域名。cf 解析生效很快，所以我很早就使用 cf 的 dns 服务器了，没有域名的话也可以去 [freenom](https://www.freenom.com) 在注册时需要使用美国 IP ，否则会注册失败

## reflare

> [Reflare](https://reflare.js.org/) 是为 Cloudflare Workers 构建的轻量级且可扩展的反向代理和负载平衡库

当初在网上找了好多别人写好的，多多少少都有点问题，后来找到这个开源项目，稍稍配置一下即可生成代理代码 [官方文档](https://reflare.js.org/deploy/)

## 准备

> 1、CloudFlare 账号  
> 2、域名  
> 3、Reflare

首先本地安装 wrangler，官方介绍 wrangler 是专为有兴趣使用 Cloudflare Workers 的人们设计的 CLI 工具。

```shell
npm install -g @cloudflare/wrangler
wrangler login

#生成一个新项目 从 reflare-template 并安装依赖项。
wrangler generate reflare-app https://github.com/xiaoyang-sde/reflare-template
cd reflare-app
npm install

```