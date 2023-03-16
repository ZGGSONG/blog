---
title: "nginx 反向代理 location 问题"
date: 2022-03-16T15:09:34+08:00
description: "总是分不清反向代理后的 location 与 proxy_pass 后加不加/的问题，网上查出来有很多人写的都有问题，怕不是没测试过，记录一下，方便查询"
featuredImage: ""
categories:
- network
tags:
- nginx
---

## 一、location 匹配路径末尾没有 /

### proxy_pass 后面的路径没有 /

```auto
location /dev
{
   proxy_redirect off;
   proxy_set_header        Host $host;
   proxy_set_header        X-Real-IP $remote_addr;
   proxy_set_header        X-Forwarded-For $proxy_add_x_forwarded_for;
   proxy_pass http://localhost:8080/tt;
}
```

访问：http://romotehost/dev/aaa.html
相当于访问：http://localhost:8080/tt/aaa.html

```auto
location /dev
{
   proxy_redirect off;
   proxy_set_header        Host $host;
   proxy_set_header        X-Real-IP $remote_addr;
   proxy_set_header        X-Forwarded-For $proxy_add_x_forwarded_for;
   proxy_pass http://localhost:8080;
}
```

访问：http://romotehost/dev/aaa.html
相当于访问：http://localhost:8080/dev/aaa.html

### proxy_pass 后面的路径有 /

```auto
location /dev
{
   proxy_redirect off;
   proxy_set_header        Host $host;
   proxy_set_header        X-Real-IP $remote_addr;
   proxy_set_header        X-Forwarded-For $proxy_add_x_forwarded_for;
   proxy_pass http://localhost:8080/;
}
```

访问：http://romotehost/dev/aaa.html
相当于访问：http://localhost:8080//aaa.html

```auto
location /dev
{
   proxy_redirect off;
   proxy_set_header        Host $host;
   proxy_set_header        X-Real-IP $remote_addr;
   proxy_set_header        X-Forwarded-For $proxy_add_x_forwarded_for;
   proxy_pass http://localhost:8080/tt/;
}
```

访问：http://romotehost/dev/aaa.html
相当于访问：http://localhost:8080/tt//aaa.html

## 二、location 匹配路径末尾有 /

### proxy_pass 后面的路径无 /

```auto
location /dev/
{
   proxy_redirect off;
   proxy_set_header        Host $host;
   proxy_set_header        X-Real-IP $remote_addr;
   proxy_set_header        X-Forwarded-For $proxy_add_x_forwarded_for;
   proxy_pass http://localhost:8080;
}
```

访问：http://romotehost/dev/aaa.html
相当于访问：http://localhost:8080/dev/aaa.html

```auto
location /dev/
{
   proxy_redirect off;
   proxy_set_header        Host $host;
   proxy_set_header        X-Real-IP $remote_addr;
   proxy_set_header        X-Forwarded-For $proxy_add_x_forwarded_for;
   proxy_pass http://localhost:8080/test;
}
```

访问：http://romotehost/dev/aaa.html
相当于访问：http://localhost:8080/testaaa.html

### proxy_pass 后面的路径有 /

```auto
location /dev/
{
   proxy_redirect off;
   proxy_set_header        Host $host;
   proxy_set_header        X-Real-IP $remote_addr;
   proxy_set_header        X-Forwarded-For $proxy_add_x_forwarded_for;
   proxy_pass http://localhost:8080/;
}
```

访问：http://romotehost/dev/aaa.html
相当于访问：http://localhost:8080/aaa.html

```auto
location /dev/
{
   proxy_redirect off;
   proxy_set_header        Host $host;
   proxy_set_header        X-Real-IP $remote_addr;
   proxy_set_header        X-Forwarded-For $proxy_add_x_forwarded_for;
   proxy_pass http://localhost:8080/test/;
}
```

访问：http://romotehost/dev/aaa.html
相当于访问：http://localhost:8080/test/aaa.html
