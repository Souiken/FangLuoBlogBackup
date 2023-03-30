---
title: ArchLinux从入门到入土1
date: 2019-03-22 22:26:47
tags: 
  - ArchLinux
  - linux
  - 教程
category: 奇奇怪怪的教程
---
# ArchLinux从入门到入土——安装篇

PS:本教程仅针对UEFI启动的设备。传统bios用户等特更新

## 准备

如果你要把ArchLinux安装到硬盘，那肯定要给ArchLinux腾出空间对不对=￣ω￣=

Windows下可以用自带的磁盘管理，右键某一分区选择`压缩卷`
![压缩卷](/static/HTUAL1-1.jpg)
然后对着未分配的分区右键，选择`新建简单卷`一路下一步即可。
~~（找不到磁盘工具的退群吧(〃＞目＜)）~~

MacOS下……~~没用过下一个（；´д｀）ゞ~~

其他Linux下……~~你都会用别的Linux了总不可能不会分区吧ヾ(￣▽￣)~~

## 下载

首先你要到[这个地方](https://www.archlinux.org/download/)下载ArchLinux的镜像
然后把它写入到U盘（软碟通用户写入方式选择RAW）

## 启动

重启电脑，用U盘启动。
（这一步应该大家都懂吧 ~~什么你不会？退群吧（滑稽）~~）
看到`root@archiso ~ #`就启动完成了。

## 联网

众所周知~~个鬼~~，ArchLinux的安装过程要联网！
所以我们要先连个网 ~~（这不是废话吗(╯‵□′)╯︵┻━┻）~~

如果你要用WiFi，在控制台输入`wifi-menu`并按回车
找到需要的WiFi连上就行了=￣ω￣=

有线网应该会自动连接好
~~（什么？你要拨号？PPPoE不在此教程的说明范围，自己百度去(╯‵□′)╯︵┻━┻）~~

不知道有没有连上网？ping一下本blog的地址不就知道了（滑稽）

>ping fangluo.top -c4

如果你ping通了，恭喜你可以进行下一步操作了\(￣︶￣*\))

使用`timedatectl set-ntp true`更新系统时间。

>为什么必须要更新系统时间呢？因为HTTPS加密需要确保你和服务器的时间差不超过2min

*PS:命令可以输入一部分后按下键盘上的`Tab`键自动补全ヾ(^▽^*)))*

## 安装

下面正式开始安装ArchLinux啦q(≧▽≦q)

### 格式化并挂载分区

（下面就是最让萌新头疼的操作了≧ ﹏ ≦需要大量的命令行，而且对每个人都不太一样=￣ω￣=）

首先用`ls /dev`看一下你的硬盘有没有被认出来

![ls /dev](/static/HTUAL1-2.jpg)

图中圈起来的就是我的第一个硬盘啦ヾ(^▽^*)))

*PS:sata接口的硬盘在Linux中被命名为sda,sdb,sdc等等，而sdx后面的数字就是这个硬盘上的第n个分区,例如sda2就是第一块硬盘的第二个分区*
*PS2:nvme固态会显示为nvme0n1p1,nvme0n1p2,nvme0n1p3等等，第一个分区为nvme0n1p1*

那怎么找到之前给ArchLinux准备的分区呢？用`fdisk`就行ヾ(≧▽≦*)o

```bash
fdisk /dev/sda #有多个硬盘的请把sda换成你要看的硬盘，如sdb,nvme1……
Command (m for help): p
```

![硬盘分区信息](/static/HTUAL1-3.jpg)

看Size就可以大致找出你之前给ArchLinux准备的分区了
记住这个分区最左边Device列的字（如/dev/sda3）
还有`Type`为`EFI System`的分区（通常为sda1）这个分区就是你的EFI引导分区
PS1:传统引导没有这个分区，具体参考引导篇~~（未更新）~~中的详细说明
PS2:对于多硬盘用户，EFI分区通常在安装有系统的硬盘上。
然后输入`q`回车退出`fdisk`

格式化要安装ArchLinux的分区

```bash
mkfs.ext4 /dev/sda3 #把sda2换成你给ArchLinux留下的分区
#ext4是文件系统，如果你想用别的（如btrfs）就把这里换掉（不建议萌新更换文件系统）
```

然后挂载这个分区和EFI分区

```bash
mount /dev/sda3 /mnt #把sda2换成你给ArchLinux留下的分区
mkdir /mnt/boot #建立boot文件夹用来挂载EFI分区
mount  /dev/sda1 /mnt/boot #把sda1换成你的EFI分区
```

### 设置镜像源

如果你不更改镜像源，那么安装系统和软件时下载速度将会**非常的慢**。

编辑文件`/etc/pacman.d/mirrorlist`
具体如下

```bash
vim /etc/pacman.d/mirrorlist #用vim打开这个文件
```

将光标移动到##China这一行下面
![源](/static/HTUAL1-4.jpg)
双击键盘上的`y`复制这一行
然后回到第一行，按键盘上的`p`粘贴
最后输入`:wq`退出vim（注意冒号）
![编辑完成后](/static/HTUAL1-5.jpg)

### 将ArchLinux安装到硬盘

终于开始正式安装啦ヾ(≧▽≦*)o

```bash
pacstrap /mnt base base-devel
```

使用这个命令就可以将ArchLinux安装到硬盘了！

为了方便以后使用，我们不仅安装了包含系统基本功能的`base`软件组，还安装了`base-devel`软件组。~~*如果你不知道这是什么，那就不用管它*~~

如果你看到了图片中的内容，那么恭喜，**ArchLinux**就*安装*完啦ヾ(≧▽≦*)o
![安装完成](/static/HTUAL1-6.jpg)

是不是很想重启进系统了呢(￣y▽,￣)╭ 然而现在重启并不能进入系统（；´д｀）ゞ
为了能够启动到已经安装好的ArchLinux，你还需要安装引导程序,建立*fstab*，为CPU安装微码更新，创建用户，配置语言等等(ಥ _ ಥ)

怎么样是不是很麻烦(o′┏▽┓｀o) 是不是想要弃坑了(ノへ￣、)
~~如果你想要弃坑了，那么ArchLinux不适合你(￢︿̫̿￢☆)~~
毕竟装ArchLinux就是一个折腾的过程
**前排提示：下面的过程将会显得非常枯燥！！！**

## 基本配置

**fstab**
我们要先建立一个`fstab`文件，这样ArchLinux启动的时候才会知道`/`是哪里，`/boot`又是哪里

```bash
genfstab -U /mnt >> /mnt/etc/fstab
```

然后进入到新安装的系统

```bash
arch-chroot /mnt
```

先设置个时区,然后和硬件同步一下时间

```bash
ln -sf /usr/share/zoneinfo/Asia/Shanghai /etc/localtime
hwclock --systohc
```

**设置语言**

```bash
vim /etc/locale.gen
```

向下翻到`#en_US.UTF-8 UTF-8`这里，按`i`进入编辑模式，删去前面的`#`
再向下到`#zh_CN.UTF-8 UTF-8`，删掉`#`
然后按`ESC`键退出编辑模式，输入`:wq`保存并退出

**PS:后文中将使用编辑xx文件来代替`vim xx`然后进入编辑模式，编辑文件以及保存并退出的过程**

运行下面的命令生成语言文件

```bash
locale-gen
```

编辑`/etc/locale.conf`文件
输入`LANG=en_US.UTF-8`并保存

**设置hostname**
编辑`/etc/hostname`文件
输入你想要设置的hostname（主机名），比如`ArchLinux`并保存
*文件中不要写其它任何内容，只留下hostname既可*
*后文将会用`myhostname`代替这里设置的主机名*

然后编辑`/etc/hosts`
内容为

```hosts
127.0.0.1    localhost
::1          localhost
127.0.1.1    myhostname.localdomain myhostname  #myhostname改称你之前设置的主机名
```

**设置root密码**
运行`passwd`，然后输入你的新密码
*密码输入过程中是看不见的，这是Linux特性*
*root密码建议设置较复杂，来确保安全性。这个密码很少会被用到*

**安装CPU微码**
amd用户安装`amd-ucode`

```bash
pacman -S amd-ucode
```

intel用户安装`intel-ucode`

```bash
pacman -S amd-ucode
```

**安装引导程序**
安装过程差不多就要结束了ヾ(≧▽≦*)o

下面介绍我自己最喜欢用的`systemd-boot`的安装方法

先运行下面的命令安装

```shell
bootctl --path=/boot install
```

然后创建ArchLinux的入口

创建文件`/boot/loader/entries/arch.conf`
（直接执行`vim /boot/loader/entries/arch.conf`）

写入如下内容

```conf
title   Arch Linux
linux   /vmlinuz-linux
initrd  /intel-ucode.img #amd用户把intel换成amd
initrd  /initramfs-linux.img
options root=/dev/sda3 rw #这里假设你把ArchLinux安装到了sda3
#把sda3换成你安装了ArchLinux的盘
```

保存上面的文件，然后编辑`/boot/loader/loader.conf`
把`default`后的值改为`arch`（注意`default`和`arch`间的空格）
然后将`timeout`后面的值改为你想要的超时时间（单位秒）

```conf
default  arch
timeout  4
console-mode max
editor   no
```

保存后你的引导就配置好啦！

也许你想问：Windows的引导去哪儿了？
`systemd-boot`会自动寻找Windows的引导文件并显示在列表中

## Ending

现在你的ArchLinux就安装完成啦！~~怎么样是不是很简单~~
重启后理论上你应该能成功启动到ArchLinux了！
输入用户名（暂时只有root）回车，然后输入密码（输入过程看不见）回车就成功登录到新安装的系统了

然而，这时候的系统还不适合正式使用
要让新安装的ArchLinux更加实用，请参考~~下一篇文章~~
下一篇还没写2333那就看[官方wiki](https://wiki.archlinux.org/index.php/General_recommendations)吧！
