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

### 方法2（推荐）

首先，使用独显直连模式启动系统，然后获取现在的显示器edid并放置到firmware中

```shell
sudo mkdir /usr/lib/firmware/edid/
sudo cp /sys/class/drm/card0-eDP-1/edid /usr/lib/firmware/edid/edid.bin
```

然后修改内核启动参数，加入

```shell
drm.edid_firmware=eDP:edid/edid.bin
```

### Early KMS

如果你开启了 [early KMS](https://wiki.archlinux.org/title/Kernel_mode_setting#Early_KMS_start) ，你还需要修改 `/etc/mkinitcpio.conf` 文件。

找到 `FILES=()` 改为 `FILES=(/usr/lib/firmware/edid/edid.bin)` ，随后使用 `sudo mkinitcpio -P` 重新生成initramfs。
*如果你用别的工具生成initramfs，自己查文档去！*

## 调整触控板滚动速度

可以使用使用`xinput`修改触控板滚动速度

```shell
xinput set-prop 'MSFT0001:00 04F3:31AD Touchpad' 'libinput Scrolling Pixel Distance' {int}
```

其中`{int}`部分是一个整数，范围`10~50`，数字越大滚动越慢。请将`{int}`替换成一个你认为合适的数值。

调整到合适的速度后将这行命令写入`/etc/xprofile`即可自动生效。

有时候 `xinput set-prop` 设置的值会被重置，我们可以配置 `xorg.conf` 让它成为默认值。

创建文件 `/etc/X11/xorg.conf.d/10-touchpad.conf` 写入以下内容

```conf
Section "InputClass"
    MatchIsTouchpad "on"
    Identifier "Touchpads"
    Driver "libinput"
    Option "HighResolutionWheelScrolling" "true"
    Option "ScrollPixelDistance" "{int}"
EndSection
```

`{int}`还是替换为先前的整数

保存并重新启动X（例如重新登录）

## 未完待续
