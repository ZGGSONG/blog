---
title: 类原生跳过谷歌锁 | 无网关代理
date: 2019-05-27T10:07:00+08:00
lastmod: 2020-04-13T21:08:42+08:00
description: ""
featuredImage: ""
categories:
- tutorial
tags:
- android
---

> 视频教程已上传 [「Youtube」](https://www.youtube.com/watch?v=SEKEvIs1kPU)

刷完类原生安卓无法进入系统的各位小伙伴们，希望这帖子能帮助到你！

![展示][1]

![演示 2.jpg][2]

**刷好系统过后，在开机向导界面会是这样：**

![1.jpg][3]

**然后按住`电源+音量上键`，进入`rec`界面，挂载`system`分区：**

![2.jpg][4]

![3.jpg][5]

进入主页高级选项，打开文件管理，进入`/system/priv-app/SetupWizard/`目录下，把里面的`SetupWizard.apk`移动到其他目录下（得记住，完成过后还得移动回来，不然系统会有各种 BUG)：

![4.jpg][6]

当然如果你进入系统验证完谷歌账号，也可以直接重刷系统~

移动完成就能够重启进入系统了，但是这样会有很多 bug：开发者选项不可用，`home`键失效，快速设置无法下拉（但我们只需要在此验证我们的谷歌账号就行了）：

![5.jpg][7]

连接`wifi`，~~再安装一个科学上软件 [***【点击下载】***][8]~~ （已失效，自寻）, 手机是可以国内网的（虽然 wifi 上有个×），然后使用软件，打开手机的某服务（或者某商店），登录账号（注意这里是登陆你之前未退出的账号，进行验证）：

![6.jpg][9]

验证通过了，然后就再次进入`rec`，挂载`system`分区，进入高级选项，打开文件管理，再把刚刚移动的`SetupWizard.apk`文件移动到`/system/priv-app/SetupWizard/`目录下即可，再次开机即可顺利进入系统（跳过 wifi 连接即可）：

![7.jpg][10]

**最后就顺利进入系统了：**

![8.jpg][11]

  [1]: https://cdn.zggsong.cn/2020/03/20/4642d07a6a6af.png
  [2]: https://cdn.zggsong.cn/2020/03/20/47f5ada07a1c1.png
  [3]: https://cdn.zggsong.cn/2020/03/20/e18f09979d035.png
  [4]: https://cdn.zggsong.cn/2020/03/20/01529e601dc42.png
  [5]: https://cdn.zggsong.cn/2020/03/20/c659c8039124d.png
  [6]: https://cdn.zggsong.cn/2020/03/20/8d5410d16c3d4.png
  [7]: https://cdn.zggsong.cn/2020/03/20/786f3a28ad8dd.png
  [9]: https://cdn.zggsong.cn/2020/03/20/b47cb4d325e4f.png
  [10]: https://cdn.zggsong.cn/2020/03/20/a6ea078756145.png
  [11]: https://cdn.zggsong.cn/2020/03/20/7f31100fa3740.png
