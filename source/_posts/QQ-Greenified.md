---
title: 腾讯官方出品的QQ绿色版，确定不来下载吗！
tags:
  - QQ
  - Windows
date: 2022-03-28 09:00:17
---


众所周知，腾讯在微软商店上架了转置成APPX的`QQ桌面版`，但是只能从商店获取。
![QQ桌面版](/static/QQ/store.png)
但是啊，这可是win32应用，只要提取出来不就可以在没有商店Windows上使用了╰(*°▽°*)╯

话不多说，开始提取！

## 获取APPX安装包

首先，让我们找到QQ桌面版的商店页面
[https://www.microsoft.com/store/productId/9NHLGF0ZWC5S](https://www.microsoft.com/store/productId/9NHLGF0ZWC5S)
然后，我们再打开一个[神奇的网站](https://store.rg-adguard.net/)
这个神奇的网站里有个输入框，把QQ桌面版的商店页面链接放进去，戳那个✔️，然后就会神奇的出现好多链接啦~
![下载](/static/QQ/download_appx.png)

简单观察不难发现，第二个体积最大的appx就是我们想要的QQ桌面版安装包了。

下载QQ桌面版的appx，并用压缩软件打开。
![APPX](/static/QQ/appx.png)
里面的QQ文件夹就是我们需要的啦🤤
把它解压出来。

## 准备运行库

为了演示，这里我打开一个全新安装的Windows10LTSC2019虚拟机👀
让我们把QQ文件夹复制到虚拟机里面，找到`/QQ/bin/QQ.exe`，双击打开……那肯定是打不开的啦🤣
![VM](/static/QQ/vm.png)
毕竟全新安装的虚拟机根本没有运行库呢。
如果你的电脑可以打开，那么就愉快使用吧~
如果打不开，继续往下看~

还是用压缩软件打开先前下载的APPX文件，进入`/VFS/SystemX86/`目录，找到`msvcp100.dll`和`msvcr100.dll`，复制出来，和`QQ.exe`放到一起,现在双击打开=￣ω￣=
![QQ](/static/QQ/QQ.png)
ohhhhhhhhhhh~

## 优势

那么，为什么要用这个版本呢？
这个版本是微软商店上架的QQ，没有恶心的QQProtect进程，占用资源较低，仅仅在首次登录账号加载数据时稍有卡顿，日常使用中CPU占用率较低。
![CPU占用](/static/QQ/cpu_usage.png)
在咱的只开了双核的虚拟机中QQ占用率还是挺低的。（这个版本的LTSC有Bug会导致CPU占用过高和新版输入法不能用，咱这里只是为了测试回到了刚装好系统的快照，没修Bug导致系统CPU占用高，并不是QQ有进程在吃CPU）

希望腾讯别吧难得稍微好用点的东西砍掉了呜呜呜
＞︿＜
辣鸡腾讯呜呜呜
