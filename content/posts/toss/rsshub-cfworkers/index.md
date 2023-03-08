---
title: "RSSHub With Cloudflare Workers"
date: 2022-11-28T16:56:17+08:00
description: "使用 cf workers 反向代理 rsshub.app"
featuredImage: ""
categories:
- toss
tags:
- rsshub
- cloudflare
- reflare
---

## 前言

前些年刚接触 RSS 的时候了解到 [DIYgod](https://diygod.me/) 的项目 [RSSHub](https://github.com/DIYgod/RSSHub)，使用起来不要太香绝大多数平台都可以订阅起来，不用再接受平台精准推送的垃圾信息了。也支持自建服务，当时还 [水了一篇文章](../../tutorial/dockerhub-install/) 但是实际使用起来发现国外的众多网站，或者说被墙了的网站还是没法订阅起来，后来迁移 RSSHub 到国外服务器上解决了这个问题，上次阿里云老用户优惠冲了几年的，国外服务器就没再续，毕竟也不便宜，梯子开一个封一个，也不想搞了，还是白嫖比较香，当然白嫖得花点时间，所以下面我将简单介绍一下白嫖的步骤。

## CF Workers

> 无服务器代码，简单理解成免费的云代理脚本

CloudFlare Workers 每日免费请求次数有 10w 完全够用，只是因为好多用梯子挂了就用 cf 转发流量，默认添加服务给的域名 `*.workers.dev` 也早就进入了 GFW 名单，所以白嫖还得有个解析到 cf 的域名。cf 解析生效很快，所以我很早就使用 cf 的 dns 服务器了，没有域名的话也可以去 [freenom](https://www.freenom.com) 在注册时需要使用美国 IP ，否则会注册失败

## Reflare

> [Reflare](https://reflare.js.org/) 是为 Cloudflare Workers 构建的轻量级且可扩展的反向代理和负载平衡库

当初在网上找了好多别人写好的，多多少少都有点问题，后来找到这个开源项目，稍稍配置一下即可生成代理代码 [官方文档](https://reflare.js.org/deploy/)

## 准备

> 1、CloudFlare 账号  
> 2、域名  
> 3、Reflare

首先本地安装 wrangler，官方介绍 wrangler 是专为有兴趣使用 Cloudflare Workers 的人们设计的 CLI 工具。

```shell
# 安装 wrangler CLI 和授权 wrangler 有的帐户。
npm install -g @cloudflare/wrangler
wrangler login

# 生成一个新项目 从 reflare-template 并安装依赖项。
wrangler generate reflare-app https://github.com/xiaoyang-sde/reflare-template
cd reflare-app
npm install

#安装的 reflare 包。
npm install reflare
```

编辑 `src/index.ts` 下面举例：

```javascript
import useReflare from 'reflare';

const handleRequest = async (
  request: Request,
): Promise<Response> => {
  const reflare = await useReflare();

  reflare.push({
    path: '/*',
    upstream: {
      domain: 'rsshub.app',
      protocol: 'https',
    },
    cors: {
      origin: '*',
    },
  });

  return reflare.handle(request);
};

addEventListener('fetch', (event) => {
  event.respondWith(handleRequest(event.request));
});
```

- 本地运行
```shell
npm run dev

yarn dev
```

- 构建

```shell
npm run build

yarn build
```

随后只需要将 `dist/worker.js` 内容复制到 cloudflare workers 上即可

> 部署过后会默认分配一个 `***.workers.dev` 但是在国内环境造就屏蔽了这个域名，但是 cloudflare 的服务器在国内还是可以访问的，并且速度并不慢，所以只需要用上面注册域名解析到这个分配的域名即可：在 workers 配置页面添加`自定义域`、选择配置`路由`亦可

## 总结

不仅可以代理 rsshub，理论上可以代理所有国内无法访问的国外公共服务，不过由于 cloudflare 给每个人的免费账户由请求上限，所以最好还是搭建个人服务，每天有 100,000 次请求的量，个人完全够用，公开出来可能被恶意刷流量。

--- 

## 追更

2023-03

最近 openai 推出了 chatgpt-3.5-turbo 速度极快，但是依旧对不对中国大陆地区开放，所以我尝试了一下代理了他的`api`接口，访问速度很顶，有需求的也可以自己尝试尝试