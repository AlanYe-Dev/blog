---
title: 记修复 Arch Linux grub + Windows Boot Manager 双系统 UEFI 引导
published: 2024-08-11
draft: false
category: Dev
tags: [Arch, Linux, Windows, Tips]
description: 在 Markdown 等静态文件中，手动设置时间一直是一件极其麻烦的事情。尤其是 Astro，它 Markdown 文件中的时间格式必须是 ISO 8601 格式。在此之前，你可能需要前往在线时间格式转换器来获取 ISO 8601 格式的时间。然而这种方法极其麻烦，而且当你希望更新时间的时候，你甚至还需要再次打开这个网页，重新复制粘贴…
language: zh
---

笔者装有 Nvidia 驱动 + KDE Plasma + Arch Linux 的笔记本电脑从睡眠状态重新唤醒时，有时会遇到黑屏或显示图像错乱的情况，不论在 UEFI 选项中开关 Optimus / Hybrid 设置均会出现类似情况。

为重新进入系统，笔者选择了强制重启以退出黑屏状态。然而，重启后发现电脑不断循环重启，没能进入系统。进入 BIOS 启动项选择后发现 grub 及 Windows Boot Manager 引导选项双双消失。

经检索，这可能是由于...

> 强制断电导致 BIOS 损坏，于是开机后主板自动从备份 BIOS 里恢复，EFI 启动记录就丢失，然后 GRUB 就消失了。
> 
> —— [reuixiy](https://io-oi.me/tech/how-to-reinstall-grub/)

遂决定使用 Arch Linux 安装盘进行抢救。

> [!NOTE]
> 或许，也可以使用其他系统的 Live CD 进行 grub 的引导修复

## 修复 grub 引导

1. 从 Arch Linux 安装盘进行启动

2. 使用 `lsblk` 命令查看磁盘分区状况。以笔者为例，分区状况大致为...

   - `/dev/nvme0n1p7`: EFI 分区
   - `/dev/nvme0n1p8`: Swap 分区
   - `/dev/nvme0n1p9`: Arch Linux 根目录

3. 挂载文件系统

> [!WARNING]
> 注意替换 Volume 名称

```shell
mount /dev/root_partition /mnt
mount --mkdir /dev/efi_system_partition /mnt/boot
swapon /dev/swap_partition
```

4. `chroot` 进入 Arch Linux 系统

```shell
arch-chroot /mnt
```

5. (若此前从未进行过此步骤...) 使用 Pacman 安装 ``grub``, ``efibootmgr`` 与 ``os-prober`` 包

> [!NOTE]
> 请依据 [Arch Linux Wiki](https://wiki.archlinux.org/title/Installation_guide#Connect_to_the_internet) 进行网络配置，此处不再赘述

```shell
sudo pacman -S grub efibootmgr os-prober
```

6. 运行 grub-install

```shell
grub-install --efi-directory=/boot --bootloader-id=grub
```

## 借助 grub 修复 Windows Boot Manager

1. 进入 Linux 终端编辑 `/etc/default/grub`，取消注释或新增 `GRUB_DISABLE_OS_PROBER=false` 一行

2. 生成 `grub.cfg`

   ```shell
   sudo grub-mkconfig -o /boot/grub/grub.cfg
   ```
   一般情况下，此时的 grub 会返回其已发现 Windows Boot Manager 的提示

3. 重启进入 grub 引导，会发现 grub 新增了 `Windows` 之选项

4. 通过该选项进入 Windows 系统后，Windows 会修复自身引导，再次重启后即可在 UEFI 选项中重新看到 grub 与 Windows Boot Manager 的引导选项。

## 参考内容

1. https://wiki.archlinux.org/title/Installation_guide
2. https://io-oi.me/tech/how-to-reinstall-grub/
3. https://www.cnblogs.com/moloom/p/17488829.html