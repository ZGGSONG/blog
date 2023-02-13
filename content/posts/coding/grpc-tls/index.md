---
title: "gRpc 建立 TLS 安全连接"
date: 2023-02-13T10:24:23+08:00
description: ""
featuredImage: ""
categories:
- coding
tags:
- grpc
- protobuf
---

## 前言

在通常情况下，如果不启用 tls 证书认证时，gRPC 服务和客户端进行的是明文通信，信息面临被任何第三方监听的风险。为了保证 gRPC 通信不被第三方监听、篡改或伪造，可以对服务器启动 TLS 加密特性。gRPC 具有 SSL/TLS 集成，并推广使用 SSL/TLS 对服务器进行身份验证，并加密客户端和服务器之间交换的所有数据。客户端可以使用可选机制来提供相互身份验证的证书。

> 关于 openssl 自签名证书 请查看 | [上一篇文章](/posts/network/openssl-cert-self/)  
> github | [示例代码](https://github.com/ZGGSONG/grpc-code)

## gRPC 加密类型

1. 不实用 TLS 加密
2. 仅服务端进行 TLS 加密
3. 客户端、服务端都进行 TLS 加密

> 注意: 在用 go 1.15 版本以上，用 gRPC 通过 TLS 建立安全连接时，会出现证书报错问题  
> `rpc error: code = Unavailable desc = connection error: desc = “transport: authentication handshake failed: x509: certificate relies on legacy Common Name field, use SANs instead”`  

## 服务端加密

### 流程

1. 制作证书(服务端证书、CA 证书)
2. 服务端启动时加载证书
3. 客户端连接时使用 CA 证书校验服务端证书有效性

### 操作

#### ca 密钥根证书

1. 写入如下配置进 ca.conf 并修改`自定义`部分 (在随后生成操作中即可无需输入其他配置)

```conf
[ req ]
default_bits       = 4096
distinguished_name = req_distinguished_name

[ req_distinguished_name ]
countryName                 = Country Name (2 letter code)
countryName_default         = CN
stateOrProvinceName         = State or Province Name (full name)
stateOrProvinceName_default = JiangSu
localityName                = Locality Name (eg, city)
localityName_default        = NanJing
organizationName            = Organization Name (eg, company)
organizationName_default    = Step
commonName                  = CommonName (e.g. server FQDN or YOUR name)
commonName_max              = 64
commonName_default          = XXX(自定义)
```

2. 生成 ca 私钥得到 ca.key

```shell
openssl genrsa -out ca.key 4096
```

3. 生成 ca 证书签发请求得到 ca.csr

```shell
openssl req -new -sha256 -out ca.csr -key ca.key -config ca.conf
```

4. 生成 ca 根证书

```shell
openssl x509 -req -days 3650 -in ca.csr -signkey ca.key -out ca.crt
```

#### 终端用户密钥证书

1. 写入如下配置进 server.conf 并修改`自定义`部分

```conf
[ req ]
default_bits       = 2048
distinguished_name = req_distinguished_name

[ req_distinguished_name ]
countryName                 = Country Name (2 letter code)
countryName_default         = CN
stateOrProvinceName         = State or Province Name (full name)
stateOrProvinceName_default = JiangSu
localityName                = Locality Name (eg, city)
localityName_default        = NanJing
organizationName            = Organization Name (eg, company)
organizationName_default    = Step
commonName                  = CommonName (e.g. server FQDN or YOUR name)
commonName_max              = 64
commonName_default          = XXX(自定义,客户端需要此字段做匹配)
[ req_ext ]
subjectAltName = @alt_names
[alt_names]
DNS.1   = XXX(自定义)
IP      = 127.0.0.1
```

2. 生成私钥得到 server.key

```shell
openssl genrsa -out server.key 2048
```

3. 生成证书签发请求得到 server.csr

```shell
openssl req -new -sha256 -out ca.csr -key ca.key -config ca.conf
```

4. 使用 CA 证书签发用户证书

```shell
openssl x509 -req -days 3650 -CA ca.crt -CAkey ca.key -CAcreateserial -in server.csr -out server.pem -extensions req_ext -extfile server.conf
```

#### Server

```go
//创建 Listen，监听 TCP 端口
lis, err := net.Listen("tcp", "127.0.0.1:50051")
if err != nil {
	log.Fatalf("credentials.NewServerTLSFromFile err: %v", err)
}
// 根据服务端输入的证书文件和密钥构造 TLS 凭证
creds, err := credentials.NewServerTLSFromFile("server.pem", "server.key")
if err != nil {
	log.Fatalf("credentials.NewServerTLSFromFile err: %v", err)
}
// 返回一个 ServerOption，用于设置服务器连接的凭据。
// 用于 grpc.NewServer(opt ...ServerOption) 为 gRPC Server 设置连接选项
s := grpc.NewServer(grpc.Creds(creds))

pb.RegisterGreeterServer(s, &HelloServer{})

if err := s.Serve(lis); err != nil {
   log.Fatalf("failed to serve: %v", err)
}
```

#### Client

```go
// 用于 grpc.Dial(target string, opts ...DialOption) 设置连接选项
conn, err := grpc.Dial("127.0.0.1:50051", grpc.WithTransportCredentials(c))
if err != nil {
   log.Fatalf("grpc.Dial err: %v", err)
}
defer conn.Close()

// 根据客户端输入的证书文件和密钥构造 TLS 凭证 第二个参数 serverNameOverride 为服务名称。
c, err := credentials.NewClientTLSFromFile("server.pem", "zggsong.com")
if err != nil {
   log.Fatalf("credentials.NewClientTLSFromFile err: %v", err)
}

client := pb.NewGreeterClient(conn)

resp, err := client.SayHello(context.Background(), &pb.HelloRequest{
   Name: "gRPC",
})
if err != nil {
   log.Fatalf("client.Search err: %v", err)
}

log.Printf("resp: %s", resp.Message)
```

## 双向加密

> 使用上一步相同的 ca、server 配置

### CA 证书

```shell
// 1. 生成 ca 私钥得到 ca.key
openssl genrsa -out ca.key 4096

// 2. 生成 ca 证书签发请求得到 ca.csr
openssl req -new -sha256 -out ca.csr -key ca.key -config ca.conf

// 3. 生成 ca 根证书
openssl x509 -req -days 3650 -in ca.csr -signkey ca.key -out ca.crt
```

### 服务端证书

```shell
// 1. 生成私钥，得到server.key
openssl genrsa -out server.key 2048

// 2. 生成证书签发请求，得到server.csr
openssl req -new -sha256 -out server.csr -key server.key -config server.conf

// 3. 用CA证书生成终端用户证书，得到server.crt
openssl x509 -req -sha256 -CA ca.crt -CAkey ca.key -CAcreateserial -days 365 -in server.csr -out se
rver.crt -extensions req_ext -extfile server.conf
```

### 客户端证书

```shell
// 1. 生成私钥，得到client.key
openssl genrsa -out client.key 2048

//2. 生成证书签发请求，得到client.csr
openssl req -new -key client.key -out client.csr


//3. 用CA证书生成客户端证书，得到client.crt
openssl x509 -req -sha256 -CA ca.crt -CAkey ca.key -CAcreateserial -days 365 -in client.csr -out client.crt
```

### Server

```go
// 公钥中读取和解析公钥/私钥对
cert, err := tls.LoadX509KeyPair("server.crt", ".server.key")
if err != nil {
   fmt.Println("LoadX509KeyPair error", err)
   return
}
// 创建一组根证书
certPool := x509.NewCertPool()
ca, err := os.ReadFile("ca.crt")
if err != nil {
   fmt.Println("read ca pem error ", err)
   return
}
// 解析证书
if ok := certPool.AppendCertsFromPEM(ca); !ok {
   fmt.Println("AppendCertsFromPEM error ")
   return
}

c := credentials.NewTLS(&tls.Config{
   //设置证书链，允许包含一个或多个
   Certificates: []tls.Certificate{cert},
   //要求必须校验客户端的证书
   ClientAuth: tls.RequireAndVerifyClientCert,
   //设置根证书的集合，校验方式使用ClientAuth设定的模式
   ClientCAs: certPool,
})
s := grpc.NewServer(grpc.Creds(c))
lis, err := net.Listen("tcp", "127.0.0.1:50051") //创建 Listen，监听 TCP 端口
if err != nil {
   log.Fatalf("credentials.NewServerTLSFromFile err: %v", err)
}
//将 SearchService（其包含需要被调用的服务端接口）注册到 gRPC Server 的内部注册中心。
//这样可以在接受到请求时，通过内部的服务发现，发现该服务端接口并转接进行逻辑处理
pb.RegisterGreeterServer(s, &HelloServer{})

//gRPC Server 开始 lis.Accept，直到 Stop 或 GracefulStop
if err := s.Serve(lis); err != nil {
   log.Fatalf("failed to serve: %v", err)
}
```

### Client

```go
// 公钥中读取和解析公钥/私钥对
cert, err := tls.LoadX509KeyPair("client.crt", "client.key")
if err != nil {
   fmt.Println("LoadX509KeyPair error ", err)
   return
}
// 创建一组根证书
certPool := x509.NewCertPool()
ca, err := os.ReadFile("ca.crt")
if err != nil {
   fmt.Println("ReadFile ca.crt error ", err)
   return
}
// 解析证书
if ok := certPool.AppendCertsFromPEM(ca); !ok {
   fmt.Println("certPool.AppendCertsFromPEM error ")
   return
}

c := credentials.NewTLS(&tls.Config{
   Certificates: []tls.Certificate{cert},
   RootCAs:      certPool,
   //InsecureSkipVerify: true,
   ServerName: "zggsong.com",
})

conn, err := grpc.Dial("127.0.0.1:50051", grpc.WithTransportCredentials(c))
if err != nil {
   log.Fatalf("grpc.Dial err: %v", err)
}
defer conn.Close()

client := pb.NewGreeterClient(conn)
resp, err := client.SayHello(context.Background(), &pb.HelloRequest{
   Name: "gRPC",
})
if err != nil {
   log.Fatalf("client.Search err: %v", err)
}

log.Printf("resp: %s", resp.Message)
```

## 参考

[「# gRPC 通过 TLS 建立安全连接」](https://linzyblog.netlify.app/2022/11/03/grpc-tls)  
[「# grpc 中 TLS 认证证书问题」](https://blog.csdn.net/qq_28356505/article/details/120828080)
