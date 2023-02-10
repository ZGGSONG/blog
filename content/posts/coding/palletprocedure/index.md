---
title: 实现托盘程序 | Go
date: 2022-08-08T18:38:00+08:00
lastmod: 2022-08-19T16:34:42+08:00
description: ""
featuredImage: ""
categories:
- coding
tags:
- golang
---

## 前言

因工作需要，使用 go 实现一些小功能，黑窗的形式有过于呆板，且容易误关闭，此前也尝试过讲 go 打包成 dll，随后通过 C#编写界面及托盘的方式来运行 | [相关代码](https://github.com/ZGGSONG/CSharpGoDemo)

## 托盘

这里使用的托盘库是蓝灯维护的一个项目 [https://github.com/getlantern/systray](https://github.com/getlantern/systray)，没错就是你知道的那个蓝灯

> 官方定义为：在通知区域中放置图标和菜单的跨平台库

简单介绍一下使用以及关于托盘图标避坑

- 使用还是非常简单的，仓库 readme 中有代码

```go
func main() {
	systray.Run(onReady, onExit)
}

func onReady() {
	systray.SetIcon(icon.Data)
	systray.SetTitle("Awesome App")
	systray.SetTooltip("Pretty awesome 超级棒")
	mQuit := systray.AddMenuItem("Quit", "Quit the whole app")

	// Sets the icon of a menu item. Only available on Mac and Windows.
	mQuit.SetIcon(icon.Data)
}

func onExit() {
	// clean up here
}
```
- 关于托盘图标`systray.SetIcon()`, 中的图标的二进制数据可使用第三方库将自己的图片来转换出来-[https://github.com/cratonica/2goarray](https://github.com/cratonica/2goarray)

使用步骤也很简单

```shell
go install github.com/cratonica/2goarray@latest
2goarray ImgData resources < icon.ico > resources.go
```
随后调用此生成的`[]byte`即可

> **注意：** 在 macos 下 png, ico 转换出来的 []byte 都可以使用，而在 windows 下只能使用 ico 转换出来的 []byte, 详情查看 [issue](https://github.com/getlantern/systray/issues/148)

## 拓展

- 为 go 打包的应用程序 (windows) 添加图标，也可以查看 [别人的实现方式](https://blog.csdn.net/FlushHip/article/details/84978556)

```shell
echo IDI_ICON1 ICON "icon.ico" > .\resources\icon.rc
windres -o icon.syso .\resources\icon.rc
```

- go 在打包的时候避免生成调试的命令行窗口，更多`go build`[命令详解](https://zhuanlan.zhihu.com/p/375530785)

```shell
go build -ldflags="-H windowsgui"
```

- 防止程序多开，[详情](https://xhat.org/posts/golang-gui-singleton/)

```go
func checkSingleton() (windows.Handle, error) {
	path, err := os.Executable()
	if err != nil {
		return 0, err
	}
	hashName := md5.Sum([]byte(path))
	name, err := syscall.UTF16PtrFromString("Local\\" + hex.EncodeToString(hashName[:]))
	if err != nil {
		return 0, err
	}
	return windows.CreateMutex(nil, false, name)
}
```
