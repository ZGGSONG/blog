---
title: "WinForm 中嵌套 WPF 添加资源字典"
date: 2022-12-06T13:25:52+08:00
description: ""
featuredImage: ""
categories:
- coding
tags:
- WPF
---

## 前言

C# 写客户端的时候，WinForm 真的很折磨，WPF 越写越顺手，最近的需求，必须要写在 WinForm 里面，没办法只能另辟蹊径，反正之前也嵌套过，只是没找到办法引入资源字典文件、第三方控件库，这次从头开始写，势必要搞定他。

## 问题

在 Visual Studio 创建一个 WPF 项目后，可以在 App.xaml 中直接全局注册想使用的资源文件，但是在 WinForm 中嵌套进 WPF 程序时，没有这个 App.xaml 全局注册资源的地方

## 解决

根据标签内容 <Application.Resources> 很容易明白，App.xaml 本质上是向 Application 对象中写入资源信息，但是在当前嵌套程序中直接往里面写的时候发现 Application 对象是 null，后来发现是 WinForm 默认不含 Application 全局变量，所以写入资源文件时直接手动创建一个即可

1、在 WPF 主窗口初始化前创建 Application 对象

```C#
if (Application.Current == null)
{
    new Application();
}
```

2、随即引入资源文件

```C#
Application.Current.Resources.MergedDictionaries
    .Add((ResourceDictionary)Application
    .LoadComponent(new Uri("/Plugin.Demo;component/styles/Dictionary.xaml", UriKind.Relative)));
```

## 附加

目前发现这样全局注册资源文件的弊病就是在 XAML 文件编辑预览时会出现无法解析资源关键字的提示信息

在针对非全局资源文件时，可以通过在 View 文件的 Resources 标签里添加 ResourcesDictionary 的方式引入资源文件，这样的好处是可以实时预览效果

```XAML
<UserControl.Resources>
    <ResourceDictionary>
        <ResourceDictionary.MergedDictionaries>
            <ResourceDictionary Source="/Plugin.Demo;component/Styles/Generic.xaml" />
        </ResourceDictionary.MergedDictionaries>
    </ResourceDictionary>
</UserControl.Resources>
```

---

## 补充

在写的时候发现引入第三方控件库时出现了报错无法找到 dll 组件的位置，后排查后发现原因在于我得项目结构时 WinForm 程序当作启动项目，WPF 程序作为被动项目引入，在导入第三方库时仅仅在 WPF 项目上引入会报错  
解决办法也很简单：直接在启动项目中引入第三方空间库即可（不需要在启动项目中做其他操作）

## WPF 引入资源的几种方式

### XAML 引入资源

#### 窗口、控件处引入

```XAML
<UserControl.Resources>
     <ResourceDictionary>
        <ResourceDictionary.MergedDictionaries>
            <!--  相对路径引入内部资源  -->
            <ResourceDictionary Source="/styles/Dictionary.xaml"/>
            <!--  相对路径引入外部资源  -->
            <ResourceDictionary Source="/Plugin.Demo;component/styles/Dictionary.xaml"/>
            <!--  绝对路径外部资源  -->
            <ResourceDictionary Source="pack://application:,,,/Plugin.Demo;component/styles/Dictionary.xaml"/>
        </ResourceDictionary.MergedDictionaries>
     </ResourceDictionary>
</UserControl.Resources>
```

#### 全局引入

```XAML
<Application.Resources>
    <ResourceDictionary>
        <ResourceDictionary.MergedDictionaries>
            <ResourceDictionary Source="pack://application:,,,/lugin.Demo;component/styles/Dictionary.xaml"/>
        </ResourceDictionary.MergedDictionaries>
    </ResourceDictionary>
</Application.Resources>
```

### 代码引入

```C#
var app = Application.Current.Resources.MergedDictionaries;

//绝对路径（实测报错）
var rDic = new System.Windows.ResourceDictionary();
rDic.Source = new Uri("pack://application:,,,/HandyControl;component/Themes/Theme.xaml", UriKind.Absolute);
app.Add(rDic);

//相对路径
app.Add((ResourceDictionary)Application.LoadComponent(new Uri("/HandyControl;component/Themes/SkinDefault.xaml", UriKind.Relative)));
```

## 参考

[「WPF 中资源字典（ResourceDictionary）的使用」](https://blog.csdn.net/SQWH_SSGS/article/details/109717719)  
[「关于 c＃：Application.Current 和 App.Current 为 null」](https://www.codenong.com/39644256/)  
[「Winform 中使用 WPF Control（带资源）」](https://www.cnblogs.com/zhaofeng-shu33/p/11204105.html)
[「WPF 引用资源字典的几种方式」](https://blog.csdn.net/vonlycn/article/details/115920929)