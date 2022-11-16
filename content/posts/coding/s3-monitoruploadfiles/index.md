---
title: 自动监控文件并上传 S3 对象存储服务器 | Go
date: 2022-05-13T16:35:00+08:00
lastmod: 2022-08-25T17:09:23+08:00
description: ""
featuredImage: ""
categories:
- coding
tags:
- golang
- s3
- file
---

## 前言

需求：

- 监控目录下文件变动
- 上传文件至 S3 服务器

本地平台：Windows 10 专业版 21H2 (19044.1826)、开发语言：go1.18.3 windows/amd64

监控目录下文件变动使用 [github.com/fsnotify/fsnotify](github.com/fsnotify/fsnotify) 上传测试服务器使用 [Minio](https://hub.docker.com/layers/minio/minio/minio/RELEASE.2021-06-17T00-10-46Z/images/sha256-e6755f3359281f3a3032c26cdfa450945a5d88bdbce5f68a05bf96d900bf222e?context=explore) 进行测试

## 实现

### 监控文件生成

根据 [仓库](https://github.com/fsnotify/fsnotify) 中的示例代码也可以实现

```go
package main

import (
    "log"

    "github.com/fsnotify/fsnotify"
)

func main() {
    // Create new watcher.
    watcher, err := fsnotify.NewWatcher()
    if err != nil {
        log.Fatal(err)
    }
    defer watcher.Close()

    // Start listening for events.
    go func() {
        for {
            select {
            case event, ok := <-watcher.Events:
                if !ok {
                    return
                }
                log.Println("event:", event)
                if event.Op == fsnotify.Write {
                    log.Println("modified file:", event.Name)
                }
            case err, ok := <-watcher.Errors:
                if !ok {
                    return
                }
                log.Println("error:", err)
            }
        }
    }()

    // Add a path.
    err = watcher.Add("/tmp")
    if err != nil {
        log.Fatal(err)
    }

    // Block main goroutine forever.
    <-make(chan struct{})
}
```

监控文件生成时有两种监测结果 (winodws)：

- 复制文件是触发一次`Create`事件，两次`Write`事件
- 直接创建文件 (cmd&代码）触发一次`Create`事件，一次`Write`事件

经过测试 Windows 上是这样，Linux 就比较一致，不论怎么创建文件，都是一次 Create 事件+一次 Write 事件

这个示例只能解决监控当前目录下的内容，子目录下的内容无法监控

解决：在监控到创建了目录以后，把新创建的目录加入到监控目录中去

```go

func StartWatch(dir string) {
	watch, _ := fsnotify.NewWatcher()
	w := Watch{
		watch: watch,
	}
	w.watchEx(dir)
	log.Println("开始监控目录：", dir, "...")
	select {}
}

func (w *Watch) watchEx(dir string) {
	//通过 Walk 来遍历目录下的所有子目录
	err := filepath.Walk(dir, func(path string, info os.FileInfo, err error) error {
		//这里判断是否为目录，只需监控目录即可 || 目录下的文件也在监控范围内，不需要我们一个一个加
		if info.IsDir() {
			path, err := filepath.Abs(path)
			if err != nil {
				return err
			}
			err = w.watch.Add(path)
			if err != nil {
				return err
			}
		}
		return nil
	})
	if err != nil {
		log.Println("监控失败 : ", err.Error())
		return
	}
	go w.watchExec()
}

func (w *Watch) watchExec() {
	for {
		select {
		case ev := <-w.watch.Events:
			{
				if ev.Op&fsnotify.Create == fsnotify.Create {
					fmt.Println("创建文件 : ", ev.Name)
					//获取新创建文件的信息，如果是目录，则加入监控中
					file, err := os.Stat(ev.Name)
					if err == nil && file.IsDir() {
						w.watch.Add(ev.Name)
						fmt.Println("添加监控 : ", ev.Name)
					}
				}
				if ev.Op&fsnotify.Write == fsnotify.Write {
					fmt.Println("写入文件 : ", ev.Name)
				}
				if ev.Op&fsnotify.Remove == fsnotify.Remove {
					fmt.Println("删除文件 : ", ev.Name)
					//如果删除文件是目录，则移除监控
					fi, err := os.Stat(ev.Name)
					if err == nil && fi.IsDir() {
						w.watch.Remove(ev.Name)
						fmt.Println("删除监控 : ", ev.Name)
					}
				}
				if ev.Op&fsnotify.Rename == fsnotify.Rename {
					//如果重命名文件是目录，则移除监控 , 注意这里无法使用 os.Stat 来判断是否是目录了
					//因为重命名后，go 已经无法找到原文件来获取信息了，所以简单粗爆直接 remove
					fmt.Println("重命名文件 : ", ev.Name)
					w.watch.Remove(ev.Name)
				}
				if ev.Op&fsnotify.Chmod == fsnotify.Chmod {
					fmt.Println("修改权限 : ", ev.Name)
				}
			}
		case err := <-w.watch.Errors:
			{
				log.Errorln("监控目录出错：", err)
				return
			}
		}
	}
}

type Watch struct {
	watch *fsnotify.Watcher
}
```

### 上传 S3 服务器

1. 上传之前得先有一台 S3 对象存储服务器，这里我直接就使用 `Minio` 镜像进行搭建 

> `Minio`新版和旧版还是有出入的，搭建以及后续维护和旧版差别比较大，而且网上各类教程主要针对旧版，方便后续排错，我是直接安装旧版  

```shell
docker pull minio/minio:RELEASE.2021-06-17T00-10-46Z

docker run -d -p 9000:9000 --restart=always --name minioDemo\
  -e "MINIO_ACCESS_KEY=admin" \
  -e "MINIO_SECRET_KEY=admin123." \
  -v D:/docker/minio/data:/data \
  -v D:/docker/minio/config:/root/.minio \
  minio/minio:RELEASE.2021-06-17T00-10-46Z server /data
```

2. 我这边使用的是 [https://github.com/aws/aws-sdk-go-v2](https://github.com/aws/aws-sdk-go-v2) 的 SDK，基本参照 [官方文档](https://aws.github.io/aws-sdk-go-v2/docs/) 进行开发，整体难度也不高，主要是在创建私有 S3 服务的`Client` 创建上有点坑，我也记录写下来过 [详情查看](https://www.zggsong.cn/archives/gostudy_s3_object_url.html) 

> 过了段时间再看，发现官方的文档整理了，主要的代码 Github 也没有了 [另一个官方文档](https://docs.aws.amazon.com/sdk-for-go/api/service/s3/#S3.PutObject)

如下代码片段仅供参考（仅实现了上传对象及 Tag)

```go
func uploadHandler(ctx context.Context, path, keyName, tags string) error {
	endpoint := global.GLO_CONF.S3EndPoint
	accessKey := global.GLO_CONF.S3AccessKey
	secretKey := global.GLO_CONF.S3SecretKey
	bucket := global.GLO_CONF.S3Bucket

	client, err := util.GetClient(ctx, endpoint, accessKey, secretKey)
	if err != nil {
		return errors.New("创建 S3 连接请求失败，" + err.Error())
	}

	if err = util.UPutObject(ctx, client, path, bucket, keyName); err != nil {
		return err
	}
	if err = util.UPutTag(ctx, client, bucket, keyName, tags); err != nil {
		return err
	}
	return nil
}
```

```go
func GetClient(ctx context.Context, endpoint, accessKey, secretKey string) (*s3.Client, error) {
	cfg, err := config.LoadDefaultConfig(
		ctx,
		config.WithCredentialsProvider(credentials.NewStaticCredentialsProvider(accessKey, secretKey, "")),
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

type S3PutObjectAPI interface {
	PutObject(ctx context.Context,
		params *s3.PutObjectInput,
		optFns ...func(*s3.Options)) (*s3.PutObjectOutput, error)
}

func PutFile(c context.Context, api S3PutObjectAPI, input *s3.PutObjectInput) (*s3.PutObjectOutput, error) {
	return api.PutObject(c, input)
}

type S3PutTaggingAPI interface {
	PutObjectTagging(ctx context.Context,
		params *s3.PutObjectTaggingInput,
		optFns ...func(*s3.Options)) (*s3.PutObjectTaggingOutput, error)
}

func putTag(c context.Context, api S3PutTaggingAPI, input *s3.PutObjectTaggingInput) (*s3.PutObjectTaggingOutput, error) {
	return api.PutObjectTagging(c, input)
}

// UPutObject 上传对象
func UPutObject(ctx context.Context, client *s3.Client, path, bucket, key string) error {
	file, err := os.Open(path)
	if err != nil {
		return errors.New("上传对象时打开文件失败，" + err.Error())
	}
	defer file.Close()

	input := &s3.PutObjectInput{
		Bucket: aws.String(bucket),
		Key:    aws.String(key),
		Body:   file,
	}
	_, err = PutFile(ctx, client, input)
	if err != nil {
		return errors.New("上传对象时发生错误，" + err.Error())
	}
	return nil
}

// UPutTag 上传标签
func UPutTag(ctx context.Context, client *s3.Client, bucket, key string, tags map[string]string) error {
	input := &s3.PutObjectTaggingInput{
		Bucket: aws.String(bucket),
		Key:    aws.String(key),
	}
	index := 0
	for k, v := range tags {
		input.Tagging.TagSet[index] = types.Tag{Key: aws.String(k), Value: aws.String(v)}
		index++
	}
	_, err := putTag(ctx, client, input)
	if err != nil {
		return errors.New("上传标签时发生错误，" + err.Error())
	}
	return nil
}
```

## 后续

在实际实现过程中发现大文件在生成是比较耗时，而且创建文件的方式多种多样，一方面可以从监控的角度去解决，另一方面（我的方式），通过数据库的方式，这样刚好解决了上传超时或失败后的重传问题，多个协程之间通过 `channel` 来解决通信问题，最后代码主体结构变成了如下：

```go
//init
...
  
//开启文件监控
go util.InitWatch(listeningPath)

//每隔 INTERVAL 自动检测上传给对象存储服务器
go func() {
  for {
    timer := time.NewTimer(time.Second * time.Duration(global.GLO_CONF.Interval))
    <-timer.C
    core.UploadServe(global.GLO_DB)
  }
}()

//每隔 INTERVAL 转发完成信息
go func() {
  for {
    timer := time.NewTimer(time.Second * time.Duration(global.GLO_CONF.Interval))
    <-timer.C
    core.ScadaMesServe(global.GLO_DB)
  }
}()

//删除数据库废弃数据，超时时间：30 天
go func() {
  for {
    // 检查频率：24 小时
    timer := time.NewTimer(time.Hour * time.Duration(24))
    <-timer.C
    core.CleanServe(global.GLO_DB)
  }
}()

//阻塞等待文件变化并保存至数据库
for {
  select {
    case path := <-global.GLO_CH_FILE:
    go core.Add2Cache(path, kmap)
    case reqByte := <-global.GLO_CH_REQ:
    go core.UpdateCache(reqByte)
  }
}
```

当然也有别的方式，只是我没想到

## 参考

- [Golang 通过 fsnotify 监控多级目录文件](https://blog.csdn.net/finghting321/article/details/102852746)
- [全网最细 Docker 安装 Minio，填满最新版大坑](https://blog.csdn.net/qq_41107231/article/details/119042855)
