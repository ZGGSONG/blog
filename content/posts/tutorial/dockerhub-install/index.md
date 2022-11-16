---
title: 宝塔 Docker 部署 RSShub
date: 2020-08-02T20:42:00+08:00
lastmod: 2020-08-02T22:48:29+08:00
description: ""
featuredImage: ""
categories:
- tutorial
tags:
- docker
- rsshub
---

> 上帝视角：不建议自建在国内服务器上，不少博客网站都被 Q 了，目前个人做法是直接通过 Cloudflare Workers 代理 dockerhub 官方提供的服务，每个月免费额度完全够用，免去维护成本

## 前言

首先，什么是 RSS，很多人还是不清楚的。RSS（全称：Really Simple Syndication，中文译名：**简易信息聚合**)，是一种消息来源格式规范，用以聚合经常发布更新数据的网站，例如博客文章、新闻、音频或视频的网摘。RSS 文件（或称做摘要、网络摘要、或频更新，提供到频道）包含全文或是节录的文字，再加上发布者所订阅之网摘数据和授权的元数据。简单来说 RSS 能够让用户订阅个人网站，当订阅的网站有新文章是能够通过它而得知更新。简单来说，RSS 就相当于我们微信已经设置好了权限的朋友圈，喜欢的内容更新即可看到。只是这是针对各个网站而言，喜欢的网站由内容更新，就不需要经常点进网站查看更新，只需进入 RSS 阅读器进行查看。更加简单的理解可以查看 [阮一峰大佬的文章](https://www.ruanyifeng.com/blog/2006/01/rss.html)  

## RSSHub
像绝大多数使用框架搭建的个人博客网站一般都支持 RSS 订阅，但是有很多目前常见的网站却不支持：知乎、bilibili、youtube 等等如果要看喜欢的内容都需要进入 app 或者网站查看动态，RSSHub 就是这样的一个工具，更很多平台内容生成 RSS 订阅源。[RSSHub 文档](https://docs.rsshub.app) 好像被 qiang 了，可以去 Github[仓库](https://github.com/DIYgod/RSSHub) 看相关信息。本文将讲述如何使用宝塔 Docker 部署 RSSHub

## 部署
- 首先安装 Docker：软件商店一键安装
- 搜索镜像：`diygod/rsshub`

![Docker RSSHub](https://cdn.zggsong.cn/2020/08/02/8efcf3e49e9c8.png)

- 创建容器：端口、目录映射和 CPU 权重

![new docker](https://cdn.zggsong.cn/2020/08/02/523f65bc847dd.png)

- 随后新建一个网站：目录要和上面设置的保持一致

![site](https://cdn.zggsong.cn/2020/08/02/fbc3cf3e988d4.png)

- 删除`/www/wwwroot/rsshub/`根目录内容后下载 RSSHub 源码 (**注意解压且放置在 rsshub 根目录放置**)[点击下载](https://github.com/DIYgod/RSSHub/archive/master.zip)
- 然后设置反向代理即可

![方向代理](https://cdn.zggsong.cn/2020/08/02/b93013cf91d9b.png)

- 最后再添加上 SSL 证书即可

## 使用

可以配合浏览器扩展 [RSSHub Radar](https://github.com/DIYgod/RSSHub-Radar) 食用，更多详情还需参看 [RSSHub 文档](https://docs.rsshub.app)

## 参考
- [https://www.ruanyifeng.com/blog/2006/01/rss.html](https://www.ruanyifeng.com/blog/2006/01/rss.html)
- [https://en.wikipedia.org/wiki/RSS](https://en.wikipedia.org/wiki/RSS)
- [https://blog.dlzhang.com/posts/49/](https://blog.dlzhang.com/posts/49/)
