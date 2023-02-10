---
title: "实现程序开机自启动 | C#"
date: 2022-12-27T17:03:58+08:00
description: ""
featuredImage: ""
categories:
- coding
tags:
- csharp
---

## 前言

在创建开机自启时一般常见的有两种方式，一种是通过创建二进制文件的快捷方式放到 Windows 开机启动目录下，另外一种则是把自启动信息写入注册表，但是这种方式需要管理员权限，所以我选择第一种方式

而在第一种方式中也存在两种情况，一种是仅对当前用户生效的开机启动，一种是对所有用户生效的开机启动分别对应的目录为

```txt
#当前用户
C:\Users\{{username}}\AppData\Roaming\Microsoft\Windows\Start Menu\Programs\Startup

#所有用户
C:\ProgramData\Microsoft\Windows\Start Menu\Programs\Startup
```

分别对应的快捷启动命令是 (win + r)

```shell
#当前用户
shell:startup

#所有用户
shell:common startup
```

## 操作

逻辑上本质也就是获取当前程序生成的二进制文件路径，并为它生成一个快捷方式到目标路径中去，当然我的需求是开机自启，也就不是桌面快捷方式，而是上面提到的（通用 or 用户）开机启动目录了

```C#
/// <summary>
/// 为本程序创建一个开机启动快捷方式
/// </summary>
public static bool ShortCutCreate()
{
	bool Result = false;
	try
	{
		var shellType = Type.GetTypeFromProgID("WScript.Shell");
		dynamic shell = Activator.CreateInstance(shellType);
		var shortcut = shell.CreateShortcut(Path.Combine(Environment.GetFolderPath(Environment.SpecialFolder.Startup), Path.GetFileNameWithoutExtension(Assembly.GetEntryAssembly().Location) + ".lnk"));
		shortcut.TargetPath = Assembly.GetEntryAssembly().Location;
		shortcut.Arguments = string.Empty;
		shortcut.WorkingDirectory = AppDomain.CurrentDomain.SetupInformation.ApplicationBase;
		shortcut.Save();
		Result = true;
	}
	catch
	{
		Result = false;
	}
	return Result;
}
```

另外需要一个检测当前源文件快捷方式是否已经存在的方法，检测逻辑则为目标目录（开机启动目录）下所有的快捷方式的源文件绝对路径是否与当前程序二进制文件绝对路径相等，若想等则当前程序已经是开机启动，否则开机不启动，主要代码如下  
> 其中，关于获取快捷方式的目标可执行文件的绝对路径，需要引入一个 COM 中的 `Windows Script Host Object Model`

```C#
public static bool ShortCutExist(string path, string target)
{
	bool Result = false;
	List<string> list = GetDirectoryFileList(target);//获取指定文件夹下的所有快捷方式（不包括子文件夹）
	foreach (var item in list)
	{
		if (path == GetAppPathViaShortCut(item))//获取快捷方式中的目标（可执行文件的绝对路径）
		{
			Result = true;
		}
	}
	return Result;
}

public static string GetAppPathViaShortCut(string shortCutPath)
{
	try
	{
		WshShell shell = new WshShell();
		IWshShortcut shortct = (IWshShortcut)shell.CreateShortcut(shortCutPath);
		return shortct.TargetPath;
	}
	catch
	{
		return null;
	}
}
```

## 完整代码

完整代码已经封装好，可直接引入使用

```C#
using System;
using System.Collections.Generic;
using System.IO;
using System.Reflection;
using IWshRuntimeLibrary;

namespace DemoApp.Util
{
    public class Util
    {
        #region public method
        /// <summary>
        /// 设置开机自启
        /// </summary>
        public static void SetStartup()
        {
            ShortCutCreate();
        }
        /// <summary>
        /// 检查是否已经设置开机自启
        /// </summary>
        /// <returns>true: 开机自启 false: 非开机自启</returns>
        public static bool IsStartup()
        {
            return ShortCutExist(appPath, StartUpPath);
        }
        /// <summary>
        /// 取消开机自启
        /// </summary>
        public static void UnSetStartup()
        {
            ShortCutDelete(appPath, StartUpPath);
        }
        #endregion

        #region params
        /// <summary>
        /// 开机启动目录
        /// </summary>
        private static readonly string StartUpPath = Environment.GetFolderPath(Environment.SpecialFolder.Startup);

        /// <summary>
        /// 当前程序二进制文件路径
        /// </summary>
        private static readonly string appPath = Assembly.GetEntryAssembly().Location;

        /// <summary>
        /// 组合的开机启动目录中的快捷方式路径
        /// </summary>
        private static readonly string appShortcutPath = Path.Combine(StartUpPath, Path.GetFileNameWithoutExtension(appPath) + ".lnk");
        #endregion

        #region native method
        /// <summary>
        /// 获取快捷方式中的目标（可执行文件的绝对路径）
        /// </summary>
        /// <param name="shortCutPath">快捷方式的绝对路径</param>
        /// <returns></returns>
        /// <remarks>需引入 COM 组件 Windows Script Host Object Model</remarks>
        private static string GetAppPathViaShortCut(string shortCutPath)
        {
            try
            {
                WshShell shell = new WshShell();
                IWshShortcut shortct = (IWshShortcut)shell.CreateShortcut(shortCutPath);
                //快捷方式文件指向的路径。Text = 当前快捷方式文件 IWshShortcut 类。TargetPath;
                //快捷方式文件指向的目标目录。Text = 当前快捷方式文件 IWshShortcut 类。WorkingDirectory;
                return shortct.TargetPath;
            }
            catch
            {
                return null;
            }
        }

        /// <summary>
        /// 获取指定文件夹下的所有快捷方式（不包括子文件夹）
        /// </summary>
        /// <param target="">目标文件夹（绝对路径）</param>
        /// <returns></returns>
        private static List<string> GetDirectoryFileList(string target)
        {
            List<string> list = new List<string>();
            list.Clear();
            string[] files = Directory.GetFiles(target, "*.lnk");
            if (files == null || files.Length == 0)
            {
                return list;
            }
            for (int i = 0; i < files.Length; i++)
            {
                list.Add(files[i]);
            }
            return list;
        }

        /// <summary>
        /// 判断快捷方式是否存在
        /// </summary>
        /// <param name="path">快捷方式目标（可执行文件的绝对路径）</param>
        /// <param target="">目标文件夹（绝对路径）</param>
        /// <returns></returns>
        private static bool ShortCutExist(string path, string target)
        {
            bool Result = false;
            List<string> list = GetDirectoryFileList(target);
            foreach (var item in list)
            {
                if (path == GetAppPathViaShortCut(item))
                {
                    Result = true;
                }
            }
            return Result;
        }

        /// <summary>
        /// 删除快捷方式（通过快捷方式目标进行删除）
        /// </summary>
        /// <param name="path">快捷方式目标（可执行文件的绝对路径）</param>
        /// <param target="">目标文件夹（绝对路径）</param>
        /// <returns></returns>
        private static bool ShortCutDelete(string path, string target)
        {
            bool Result = false;
            List<string> list = GetDirectoryFileList(target);
            foreach (var item in list)
            {
                if (path == GetAppPathViaShortCut(item))
                {
                    System.IO.File.Delete(item);
                    Result = true;
                }
            }
            return Result;
        }
        /// <summary>
        /// 为本程序创建一个开机启动快捷方式
        /// </summary>
        private static bool ShortCutCreate()
        {
            bool Result = false;
            try
            {
                ShortCutDelete(appPath, StartUpPath);

                var shellType = Type.GetTypeFromProgID("WScript.Shell");
                dynamic shell = Activator.CreateInstance(shellType);
                var shortcut = shell.CreateShortcut(appShortcutPath);
                shortcut.TargetPath = Assembly.GetEntryAssembly().Location;
                shortcut.Arguments = string.Empty;
                shortcut.WorkingDirectory = AppDomain.CurrentDomain.SetupInformation.ApplicationBase;
                shortcut.Save();
                Result = true;
            }
            catch
            {
                Result = false;
            }
            return Result;
        }
        #endregion
    }
}

```

## 参考

[「# win10 startup 启动目录路径命令」](https://www.cnblogs.com/wswind/p/10078682.html)  
[「# 使用 C# 代码创建快捷方式文件」](https://blog.walterlv.com/post/create-shortcut-file-using-csharp.html)  
[「# WPF 开发自动开机启动程序」](https://cloud.tencent.com/developer/article/1342887)  
[「# 使用 C# 实现程序开机自启动」](https://blog.csdn.net/capsclock/article/details/128373102)  
[「# 两种版本实现 C#获取快捷方式目标路径的代码」](https://blog.csdn.net/zzsfqiuyigui/article/details/6046742)  
