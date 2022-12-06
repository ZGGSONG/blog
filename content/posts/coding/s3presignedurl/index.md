---
title: S3 对象存储获取预签名 URL | Go
date: 2022-05-11T18:25:00+08:00
lastmod: 2022-06-15T10:06:58+08:00
description: ""
featuredImage: ""
categories:
- coding
tags:
- Golang
- S3
---

## 前言

最近学习使用对象存储，自然要学习一下 `Amazon S3`，同时最近学了一下 Golang，简单记录一下学习使用 [AWS SDK for Go V2](https://aws.github.io/aws-sdk-go-v2/docs/code-examples/s3/generatepresignedurl/) 生成文件预签名 URL，

> 预签名：有些时候需要给别人访问对象存储中的对象，又不想给对方桶的权限来访问，就可以通过生成预签名 URL 给别人临时访问对象。官方目前是有两个签名版本 SigV2-带参数`&Expires`-1 年 和 SigV4 带参数-`&X-Amz-Expires`-7 天，考虑到安全性和使用效率等因素，目前官方已停止 SigV2 版本的支持。

## 实操

首先创建 `S3 Client` 对象，在写代码的过程中，我发现 Golang 的 SDK V2 版本和其他语言包括 Go 的 V1 版本在创建 client 对象的时候都有不小的区别，我写的仅作参考  

> 针对自建的对象存储服务器，使用`endpoint`时注意`UsePathStyle `为`true`，不然会出现`http://{bucket}.{endpoint}`错误

```Golang
func GetClient(accesskey, secretkey, endpoint string) (*s3.Client, error) {
	cfg, err := config.LoadDefaultConfig(
		context.TODO(),
		config.WithCredentialsProvider(credentials.NewStaticCredentialsProvider(accesskey, secretkey, "")),
		// 已弃用
		// config.WithEndpointResolver(
			// 	aws.EndpointResolverFunc(func(service, region string) (aws.Endpoint, error) {
				// 		return aws.Endpoint{URL: endpoint}, nil
				// 	})),
		config.WithEndpointResolverWithOptions(
			aws.EndpointResolverWithOptionsFunc(func(service, region string, options ...interface{}) (aws.Endpoint, error) {
				return aws.Endpoint{URL: endpoint}, nil
			})),
		config.WithRegion("us-east-1"),
	)
	if err != nil {
		return nil, err
	}

	client := s3.NewFromConfig(cfg, func(o *s3.Options) {
		o.UsePathStyle = true
		o.EndpointOptions.DisableHTTPS = true
	})
	return client, nil
}
```

如下是主要主要代码

```Golang
type S3PresignGetObjectAPI interface {
	PresignGetObject(
		ctx context.Context,
		params *s3.GetObjectInput,
		optFns ...func(*s3.PresignOptions)) (*v4.PresignedHTTPRequest, error)
}

func GetPresignedURL(c context.Context, api S3PresignGetObjectAPI, input *s3.GetObjectInput) (*v4.PresignedHTTPRequest, error) {
	return api.PresignGetObject(c, input)
}

// 获取预签名的 url
func GetObjectUrl(client *s3.Client, bucket string, key string) string {
	input := &s3.GetObjectInput{
		Bucket: aws.String(bucket),
		Key:    aws.String(key),
	}

	psClient := s3.NewPresignClient(client)

	resp, err := GetPresignedURL(context.TODO(), psClient, input)
	if err != nil {
		return ("get url err: " + err.Error())
	}
	return resp.URL
}
```

## 参考
- [C# 通过 S3 上传文件到私有云存储](https://blog.csdn.net/tw_tangliang/article/details/118669099)
- [https://github.com/aws/aws-sdk-go-v2/issues/1295](https://github.com/aws/aws-sdk-go-v2/issues/1295)
- [ceph 相关 s3 预签名 url（presign）](https://www.cnblogs.com/luxf0/p/14145592.html)
