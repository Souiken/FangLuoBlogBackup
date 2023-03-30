---
title: 拯救者r9000p的ArchLinux调教指南
tags:
  - ArchLinux
  - 教程
  - 拯救者
  - r9000p
category: 奇奇怪怪的教程
date: 2023-02-03 13:40:59
---

本文用来记录我在使用联想拯救者r9000p时遇到的各种小问题和调教方案😋

## 双显卡混合模式没有165Hz

### 方法1 （仅Xorg）

解决思路：
独显直连启动，通过 `xvidtune -show` 获取165Hz下的Modeline，添加到 `xorg.conf` 中。

获取到的Modeline如下

```Modeline
"2560x1600"   777.41   2560 2608 2640 2720   1600 1603 1609 1732 -hsync -vsync
```

于是在 `/etc/X11/xorg.conf.d/` 目录下创建文件 `10-monitor.conf` 写入以下内容

```conf
Section "Monitor"
    Identifier     "eDP"
    ModeLine "2560x1600_165.00"   777.41   2560 2608 2640 2720   1600 1603 1609 1732 -hsync -vsync
    Option "PreferredMode" "2560x1600_165.00"
EndSection
```

重启即可。

### 方法2

首先，使用独显直连模式启动系统，然后获取现在的显示器edid并放置到firmware中

```shell
sudo mkdir /usr/lib/firmware/edid/
sudo cp /sys/class/drm/card0-eDP-1/edid /usr/lib/firmware/edid/edid.bin
```

然后修改内核启动参数，加入

```shell
drm.edid_firmware=eDP:edid/edid.bin
```

## 使用Fn+R切换刷新率

*注：混合显卡模式下可能只支持方法2添加的刷新率！*

首先安装`xbindkeys`

```shell
sudo pacman -S xbindkeys
```

然后编辑`~/.xbindkeysrc`（如果没有就新建一个），增加以下内容

```shell
#Set refresh rate
"if xrandr | grep '60.01\*'; then xrandr -r 165; else xrandr -r 60; fi"
    m:0x10 + c:248
    Mod2 + NoSymbol
```

最后，需要让xbindkeys开机启动，编辑`/etc/xprofile`，加入一行

```shell
xbindkeys
```

## 调整触控板滚动速度

可以使用使用`xinput`修改触控板滚动速度

```shell
xinput set-prop 'MSFT0001:00 04F3:31AD Touchpad' 'libinput Scrolling Pixel Distance' {int}
```

其中`{int}`部分是一个整数，范围`10~50`，数字越大滚动越慢。请将`{int}`替换成一个你认为合适的数值。

调整到合适的速度后将这行命令写入`/etc/xprofile`即可自动生效。

## 未完待续
