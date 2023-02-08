---
title: "OpenSSL 自签名证书"
date: 2023-02-08T16:26:47+08:00
description: ""
featuredImage: ""
categories:
- coding
tags:
- openssl
---
## 基本概念

| 名词 | 含义                                                                                                                                                               |
| :--- | :----------------------------------------------------------------------------------------------------------------------------------------------------------------- | --- |
| ca   | 电子商务认证中心、电子商务认证授权机构，是负责**发放和管理数字证书的权威机构**，并作为电子商务交易中受信任的第三方，承担公钥体系中公钥的合法性检验的责任           |
| csr  | **证书签名请求** 一般是生成请求以后发送给 CA，然后 CA 会给你签名并发回证书                                                                                         |
| x509 | 密码学里**公钥证书的格式标准**。X.509 证书里含有公钥、身份信息（比如网络主机名，组织的名称或个体名称等）和签名信息（可以是证书签发机构 CA 的签名，也可以是自签名） |
| crt  | 证书文件 ，可以是 DER（二进制）编码的，也可以是 PEM（ ASCII (Base64) ）编码的 ，在类 unix 系统中比较常见                                                           |
| cer  | 也是证书 常见于 Windows 系统 编码类型同样可以是 DER 或者 PEM 的，windows 下有工具可以转换 crt 到 cer                                                               |
| key  | 一般公钥或者密钥都会用这种扩展名，可以是 DER 编码的或者是 PEM 编码                                                                                                 |     |
| p12  | 证书 包含一个 X509 证书和一个被密码保护的私钥                                                                                                                      |

注意：

- x509 有多种常用的拓展名
  - `pem` - 通常是 Base64 格式的
  - `crt`、`cer`、`der`- 通常是 DER 二进制格式的
  - [证书文件扩展名...](https://zh.wikipedia.org/wiki/X.509#%E8%AF%81%E4%B9%A6%E6%96%87%E4%BB%B6%E6%89%A9%E5%B1%95%E5%90%8D)
    
查看公钥的内容命令为：`openssl rsa -noout -text (-inform DER/PEM) -in cakey.key`
    
查看证书的内容命令为：`openssl x509 -noout -text (-inform der) -in cacert.crt`  
    
- PEM 转为 DER：`openssl x509 -in cacert.crt -outform der -out cacert.der`
- DER 转为 PEM：`openssl x509 -in cert.crt -inform der -outform pem -out cacert.pem`

## 自签名证书类型

- 自签名证书
- 自建 CA 签名的证书

自签名的证书无法被吊销，CA 签名的证书可以被吊销，能不能吊销证书的区别在于如果私钥不小心被恶意获取，如果证书不能被吊销那么黑客很有可能伪装成受信任的客户端与用户进行通信。  
如果你的规划需要创建多个客户端证书，那么使用自建 CA 签名证书的方法比较合适，只要给所有的客户端都安装了 CA 根证书，那么以该 CA 根证书签名过的客户端证书都是信任的，不需要重复的安装客户端证书。

### 自签名证书

#### 生成私钥

```shell
openssl genrsa -out server.key 4096
```

#### 生成证书签名请求

```shell
openssl req -new -key server.key -out server.csr
```

> 生成 CSR 的过程中，会提示输入一些信息，其中一个提示是 Common Name (e.g. YOUR name)，这个非常重要，这一项应填入 FQDN(Fully Qualified Domain Name)完全合格域名/全称域名，如果您使用 SSL 加密保护网络服务器和客户端之间的数据流，举例被保护的网站是 `https://test.chinacloudsites.cn`那么此处 Common Name 应输入 `test.chinacloudsites.cn

#### 签发证书

```shell
 openssl x509 -req -days 365 -in server.csr -signkey server.key -out server.crt
```

#### 一步生成私钥证书

```shell
openssl req -new -x509 -newkey rsa:4096 -keyout server.key -out server.crt
```

### 自建 CA 签名的证书

#### 生成根证书私钥

```shell
openssl genrsa -out ca.key 2048
```

#### 生成根证书签名请求

```shell
openssl req -new -in ca.key -out ca.csr
```

#### 生成根证书

```shell
openssl x509 -req -in ca.csr -signkey ca.key -extensions v3_ca -out ca.crt
```

#### 签发客户端证书

1、新建证书私钥

```shell
openssl genrsa -out client.key 2048
```

2、新建证书签名请求

> 与上述生成证书签名请求信息一致

```shell
openssl req -new -in client.key -out client.csr
```

3、CA 签发客户端证书

```shell
openssl x509 -req -in client.csr -CA ca.crt -CAkey ca.key -CAcreateserial -out client.crt
```

4、验证 CA 证书有效性

```shell
openssl verify -CAfile ca.crt client.crt
```

5、吊销证书 [参考资料](https://www.cnblogs.com/zhaobowen/p/13321578.html)

## 参考

[「# OpenSsl 子命令 genrsa, rsa, req, x509」](https://blog.csdn.net/t990423909/article/details/120837032)  
[「# 自签名证书和 CA 证书的区别和制作、使用」](https://www.cnblogs.com/zhaobowen/p/13321578.html)  
[「# 如何用 OpenSSL 创建自签名证书」](https://docs.azure.cn/zh-cn/articles/azure-operations-guide/application-gateway/aog-application-gateway-howto-create-self-signed-cert-via-openssl)  
[「# 秒懂 Https 之 CA 证书与自签名证书漫谈」](https://shusheng007.top/2020/07/25/1-3/)  
