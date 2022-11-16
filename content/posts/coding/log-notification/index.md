---
title: 日志与通知 | Go
date: 2022-05-13T17:16:00+08:00
lastmod: 2022-08-19T17:35:31+08:00
description: ""
featuredImage: ""
---

## 日志

紧跟 [上一篇文章](https://www.zggsong.cn/archives/listen_file_upload_s3_with_golang.html)，日志是必然需要有的，Go 的 [logrus](https://github.com/Sirupsen/logrus) 日志框架星星最多，简单使用了一下，感觉比老早之前写 C#下的`log4net`好用太多，符合直觉多了。`logrus` `golang` 标准库日志模块完全兼容，直接使用 `log "github.com/sirupsen/logrus" `替换所有日志导入不要太舒服。网上有太多太多关于 `logrus` 的介绍，仅做个人使用记录。[推荐一下](https://mojotv.cn/2018/12/27/golang-logrus-tutorial)

```go
//Log
logger := &lumberjack.Logger{
  Filename:   "./Log/XXXX_Log_" + time.Now().Format("20060102_150405") + ".log",
  MaxSize:    100,  // 日志文件大小，单位是 MB
  MaxBackups: 10,   // 最大过期日志保留个数
  MaxAge:     28,   // 保留过期文件最大时间，单位 天
  Compress:   true, // 是否压缩日志，默认是不压缩。这里设置为 true，压缩日志
  LocalTime:  true, // 是否使用本地时间，默认是使用 UTC 时间
}

log.SetOutput(logger) // logrus 设置日志的输出方式
log.SetFormatter(&log.TextFormatter{
  TimestampFormat: "2006-01-02 15:04:05",
})
```

## Toast 通知

Go 写主要为了方便，没有了 C#的界面，软件长久运行，有效的通知就很有必要，我选择的是 `Windows` 的 `Toast`通知方式  

![](https://cdn.zggsong.cn/2022/08/19/9cd563a2b4c30.png)

这是我使用的是 [https://github.com/go-toast/toast/tree/v1](https://github.com/go-toast/toast/tree/v1) 根据仓库的示例代码即可快速运行起来，如下代码仅做记录

```go
func ToastNormal(msg string) {
	notification := toast.Notification{
		AppID:    "Test App",
		Title:    "This is a test...",
		Message:  msg,
		Duration: toast.Short,
	}
	_ = notification.Push()
}

func ToastError(msg, file string) {
	notification := toast.Notification{
		AppID:    "Test App",
		Title:    "Error Warning",
		Message:  msg,
		Duration: toast.Short,
		Audio:    toast.LoopingAlarm,
		Actions: []toast.Action{
			{"protocol", "OPEN", file},//全局参数拿到日志文件
		},
	}
	_ = notification.Push()
}
```
