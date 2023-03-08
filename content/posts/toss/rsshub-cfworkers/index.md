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

[官方文档](https://reflare.js.org/deploy/) 有详细的介绍说明，我这边简单介绍一下我感觉用的快速方便的办法

```shell
# 下载模板
git clone https://github.com/xiaoyang-sde/reflare-template.git

# 打开后修改先下载依赖，建议使用 yarn
yarn install
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
yarn build && yarn dev
```

随后只需要将 `dist/worker.js` 内容复制到 cloudflare workers 上即可

> 部署过后会默认分配一个 `***.workers.dev` 但是在国内环境造就屏蔽了这个域名，但是 cloudflare 的服务器在国内还是可以访问的，并且速度并不慢，所以只需要用上面注册域名解析到这个分配的域名即可：在 workers 配置页面添加`自定义域`、选择配置`路由`亦可

## 总结

不仅可以代理 rsshub，理论上可以代理所有国内无法访问的国外公共服务，只需要修改模板中 `src/index.ts` 里面的配置项即可，具体如何修改参考 [官方文档](https://reflare.js.org/deploy/)，不过由于 cloudflare 给每个人的免费账户由请求上限，所以最好还是搭建个人服务，每天有 100,000 次请求的量，个人完全够用，公开出来可能被恶意刷流量。

--- 

## 追更

2023-03

最近 openai 推出了 chatgpt-3.5-turbo 速度极快，但是依旧对不对中国大陆地区开放，所以我尝试了一下代理了他的`api`接口，访问速度很顶，有需求的也可以自己尝试尝试，下面是我生成的针对 `api.openai.com` `path: /*` 的用于 cfworkers 的 js 代码 

```javascript
(()=>{"use strict";const e=async(e,t)=>{await t();const{request:r,response:o,route:s}=e,n=s.cors;if(void 0===n)return;const{origin:a,methods:i,exposedHeaders:c,allowedHeaders:u,credentials:l,maxAge:d}=n,h=r.headers.get("origin");if(null===h||!1===a)return;const f=new Headers(o.headers);if(!0===a?f.set("Access-Control-Allow-Origin",h):Array.isArray(a)?a.includes(h)&&f.set("Access-Control-Allow-Origin",h):"*"===a&&f.set("Access-Control-Allow-Origin","*"),Array.isArray(i))f.set("Access-Control-Allow-Methods",i.join(","));else if("*"===i)f.set("Access-Control-Allow-Methods","*");else{const e=r.headers.get("Access-Control-Request-Method");null!==e&&f.set("Access-Control-Allow-Methods",e)}if(Array.isArray(c)?f.set("Access-Control-Expose-Headers",c.join(",")):"*"===c&&f.set("Access-Control-Expose-Headers","*"),Array.isArray(u))f.set("Access-Control-Allow-Headers",u.join(","));else if("*"===u)f.set("Access-Control-Allow-Headers","*");else{const e=r.headers.get("Access-Control-Request-Headers");null!==e&&f.set("Access-Control-Allow-Headers",e)}!0===l&&f.set("Access-Control-Allow-Credentials","true"),void 0!==d&&Number.isInteger(d)&&f.set("Access-Control-Max-Age",d.toString()),e.response=new Response(o.body,{status:o.status,statusText:o.statusText,headers:f})},t=new Set(["country","continent","asn","ip","hostname","user-agent"]),r=new Set(["equal","not equal","greater","less","in","not in","contain","not contain","match","not match"]),o=({field:e,operator:o,value:s})=>{if(void 0===e||void 0===o||void 0===s)throw new Error("Invalid 'firewall' field in the option object");if(!1===t.has(e))throw new Error("Invalid 'firewall' field in the option object");if(!1===r.has(o))throw new Error("Invalid 'firewall' field in the option object")},s=(e,t)=>{const r=e.cf;switch(t){case"asn":return r?.asn;case"continent":return r?.continent||"";case"country":return r?.country;case"hostname":return e.headers.get("host")||"";case"ip":return e.headers.get("cf-connecting-ip")||"";case"user-agent":return e.headers.get("user-agent")||"";default:return}},n=(e,t)=>{if(!(t instanceof RegExp))throw new Error("You must use 'new RegExp('...')' for 'value' in firewall configuration to use 'match' or 'not match' operator");return t.test(e.toString())},a=(e,t)=>{if("string"!=typeof e||"string"!=typeof t)throw new Error("You must use string for 'value' in firewall configuration to use 'contain' or 'not contain' operator");return e.includes(t)},i=(e,t)=>{if(!Array.isArray(t))throw new Error("You must use an Array for 'value' in firewall configuration to use 'in' or 'not in' operator");return t.some((t=>t===e))},c={match:n,contain:a,equal:(e,t)=>e===t,in:i,greater:(e,t)=>{if("number"!=typeof e||"number"!=typeof t)throw new Error("You must use number for 'value' in firewall configuration to use 'greater' or 'less' operator");return e>t},less:(e,t)=>{if("number"!=typeof e||"number"!=typeof t)throw new Error("You must use number for 'value' in firewall configuration to use 'greater' or 'less' operator");return e<t},"not match":(e,t)=>!n(e,t),"not contain":(e,t)=>!a(e,t),"not equal":(e,t)=>e!==t,"not in":(e,t)=>!i(e,t)},u=async(e,t)=>{const{request:r,route:n}=e;if(void 0!==n.firewall){n.firewall.forEach(o);for(const{field:e,operator:t,value:o}of n.firewall){const n=s(r,e);if(void 0!==n&&c[t](n,o))throw new Error("You don't have permission to access this service.")}await t()}else await t()},l=async(e,t)=>{const{request:r,route:o}=e,s=new Headers(r.headers);if((e=>{e.set("X-Forwarded-Proto","https");const t=e.get("Host");null!==t&&e.set("X-Forwarded-Host",t);const r=e.get("cf-connecting-ip"),o=e.get("X-Forwarded-For");null!==r&&null===o&&e.set("X-Forwarded-For",r)})(s),void 0===o.headers)return e.request=new Request(r.url,{body:r.body,method:r.method,headers:s}),void await t();if(void 0!==o.headers.request)for(const[e,t]of Object.entries(o.headers.request))s.set(e,t);e.request=new Request(r.url,{body:r.body,method:r.method,headers:s}),await t();const{response:n}=e,a=new Headers(n.headers);if(void 0!==o.headers.response)for(const[e,t]of Object.entries(o.headers.response))a.set(e,t);e.response=new Response(n.body,{status:n.status,statusText:n.statusText,headers:a})},d=e=>{if(void 0===e.domain)throw new Error("Invalid 'upstream' field in the option object")},h=e=>{const t=e.map((e=>void 0===e.weight?1:e.weight)),r=t.reduce(((e,r,o)=>{const s=e+r;return t[o]=s,s}));if(0===r)throw new Error("Total weights should be greater than 0.");const o=Math.random()*r;for(const r of t.keys())if(t[r]>=o)return e[r];return e[Math.floor(Math.random()*e.length)]},f={random:h,"ip-hash":(e,t)=>e[(t.headers.get("cf-connecting-ip")||"0.0.0.0").split(".").map(((e,t,r)=>parseInt(e,10)*256**(r.length-t-1))).reduce(((e,t)=>e+t))%e.length]},p=async(e,t)=>{const{request:r,route:o}=e,{upstream:s,loadBalancing:n}=o;if(void 0===s)throw new Error("The required 'upstream' field in the option object is missing");Array.isArray(s)?s.forEach(d):d(s);const a=Array.isArray(s)?s:[s];if(void 0===n)return e.upstream=h(a),void await t();const i=n.policy||"random",c=f[i];e.upstream=c(a,r),await t()},w=async(e,t)=>{const{request:r,upstream:o}=e;if(null===o)return void await t();const s=((e,t)=>{const r=new URL(e),{domain:o,port:s,protocol:n}=t;return r.hostname=o,void 0!==n&&(r.protocol=`${n}:`),r.port=void 0===s?"":s.toString(),r.href})(r.url,o),n=((e,t)=>{const r={body:t.body,method:t.method,headers:t.headers};return new Request(e,r)})(s,r);e.response=await fetch(n),await t()};class m{namespace;constructor(e){this.namespace=e}get=async e=>await this.namespace.get(e,{type:"json",cacheTtl:60});put=async(e,t)=>{await this.namespace.put(e,JSON.stringify(t))};delete=async e=>{await this.namespace.delete(e)}}const g=(e,t)=>new Response(e,{status:t}),y=e=>new URL(e.url).host,A={provider:"static",routeList:[]};addEventListener("fetch",(t=>{t.respondWith((async t=>{const r=await(async(t=A)=>{const r=((...e)=>{const t=[...e];return{push:(...e)=>{t.push(...e)},execute:async e=>{const r=async(o,s)=>{if(s===o)throw new Error("next() called multiple times");if(s>=t.length)return;const n=t[s];await n(e,(async()=>r(s,s+1)))};await r(-1,0)}}})(u,p,l,e,w),o=[];if("static"===t.provider)for(const e of t.routeList)o.push(e);if("kv"===t.provider){const e=new m(t.namespace),r=await e.get("route-list")||[];for(const e of r)o.push(e)}return{handle:async e=>{const t=((e,t)=>{const r=new URL(e.url);for(const o of t)if(void 0===o.methods||o.methods.includes(e.method)){const e=RegExp(`^${o.path.replace(/(\/?)\*/g,"($1.*)?").replace(/\/$/,"").replace(/:(\w+)(\?)?(\.)?/g,"$2(?<$1>[^/]+)$2$3").replace(/\.(?=[\w(])/,"\\.").replace(/\)\.\?\(([^[]+)\[\^/g,"?)\\.?($1(?<=\\.)[^\\.")}/*$`);if(r.pathname.match(e))return o}})(e,o);if(void 0===t)return g("Failed to find a route that matches the path and method of the current request",500);const s={request:e,route:t,hostname:y(e),response:new Response("Unhandled response"),upstream:null};try{await r.execute(s)}catch(e){e instanceof Error&&(s.response=g(e.message,500))}return s.response},unshift:e=>{o.unshift(e)},push:e=>{o.push(e)}}})();return r.push({path:"/*",upstream:{domain:"api.openai.com",protocol:"https"},cors:{origin:"*"}}),r.handle(t)})(t.request))}))})();
```