---
title: Archlinux 安装配置美化
date: 2018-04-05 17:24:48
tags:
---
# <center>Archlinux 安装配置美化</center>

## 0. 前言
很早之前，大概两三年前吧，第一次听说Archlinux,于是弄了个虚拟机开始了装机之路，折腾了两天，总算在虚拟机第一次装成功了，但是完成之后，记得使用的是gnome的桌面，什么都不会配置，一看这是什么呀，这么丑，哈哈是的仅仅是看看Archlinux完成之后是什么样子，结果大失所望，这比ubuntu的桌面还不如，折腾了两天是图啥呀，于是不再这上面投入时间了。前些天装了个manjaro的linux系统是基于Archlinux的发行版，虽然遇到点困难，但完成后给我真的是一种惊艳的感觉，操作感真的是棒棒哒，但是当我敲出`screenfetch`出现的是类似小米的丑陋图标时，我觉得既然manjaro是基于Archlinux的给人一种耳目一新的感觉，为什么我不从头开始搭建一个Archlinux，然后配置成类似的样子呢，所以一切是为了得到一张牛逼的图片，哈哈。
<center>{% asset_img 1.png 说明 %}</center>

## 1. 系统安装

## 注意：本机为GPT+UEFI类型
一切从下图开始
<center>{% asset_img 2.gif 说明 %}</center>


## 设置键盘布局
ArchISO 默认键盘布局为 US（美式键盘）。
如需修改键盘布局请使用 loadkeys 命令。
如需修改字体请使用 setfont 命令。

## 网络连接
ArchISO 在启动时会尝试连接网络，可通过命令 ping 查看连接是否已建立。
```shell
ping -c 4 www.baidu.com
```
<center>{% asset_img 3.gif 说明 %}</center>

若网络尚未连接，请先接入网络。若使用 WiFi 连接，请使用 wifi-menu 命令。
```shell
wifi-menu
```
## 同步时间
同步时间以确保时间准确无误：
```shell
timedatectl set-ntp true
```
## 选择软件仓库服务器
>该配置不仅会应用到安装环境，也会应用至新系统中。

选择地理位置最为接近的镜像服务器以获得更高的下载速度。pacman 优先使用位置靠前的镜像地址。将选定的镜像地址置于最前以便 pacman 使用。

```shell
nano /etc/pacman.d/mirrorlist
```
1. [F6] 搜索 china
2. [方向键] 移动光标至 Server 行
3. [CTRL+K] 剪切该行
4. [方向键] 移动光标至其他 Server 行前
5. [CTRL+U] 粘贴至此行
6. [CTRL+O] 保存，[回车键] 确定
7. [CTRL+X] 推出
<center>{% asset_img 4.gif 说明 %}</center>

>本文选择zzu镜像
`Server = http://mirrors.zzu.edu.cn/archlinux/$repo/os/$arch`比较快一点

## 分区
## 这是最关键的地方之一
## 分区方案

* Arch Linux 要求至少一个分区分配给根目录 /。

* 在 UEFI 系统上，需要一个 UEFI 系统分区。(由于本文是双系统，Windows上已有该分区在/dev/sda2上)

在本文的安装中，空闲磁盘100GB,分区如下：

|Linux Swap|8GB|/dev/sda9|  
|:-|:-:|-:|
|  /| 42GB|/dev/sda10|
| home | 50GB|/dev/sda11|
使用gdisk很好对系统进行分区。
```
gdisk /dev/sda
```
## 格式化（创建文件系统）
```
mkswap /dev/sda9
mkfs.ext4 /dev/sda10
mkfs.ext4 /dev/sda11
```
## 挂载
```
mount /dev/sda10 /mnt
mkdir /mnt/home
mkdir /mnt/boot/efi
mount /dev/sda2 /mnt/boot/efi
mount /dev/sda11 /mnt/home

swapon /dev/sda9
```
## 安装基础包
```
pacstrap /mnt base base-devel
```
## 生成分区表
```
genfstab -U -p /mnt >> /mnt/etc/fstab
```
## 接着使用 arch-chroot 进入新系统。
```
arch-chroot /mnt /bin/bash
```
## 时区
```
ln -sf /usr/share/zoneinfo/Asia/Shanghai /etc/localtime
```
## 区域设置
进行区域设置以正确显示本地文字、货币、时间与时期格式以及其它本地相关标准。
编辑 /etc/locale.gen，去掉需要的 locale 的注释（行头的字符 #）。

```
nano /etc/locale.gen
```
取消`en_US.UTF-8 UTF-8`上的注释，然后使用 locale-gen 生成 locale。
```
locale-gen
```
接着使用以下命令设置默认 locale。
```
echo LANG=en_US.UTF-8 > /etc/locale.conf
```
## 硬件时间设置
```
hwclock --systohc --utc
```
## 主机名
```
echo zxd > /etc/hostname
```
接着向 /etc/hosts 文件添加 hosts 条目。
```
#<ip-address>	<hostname.domain.org>	<hostname>
127.0.0.1	localhost.localdomain	localhost
::1		localhost.localdomain   localhost
127.0.1.1	zxd.localdomain	        zxd
```
## 随机盘环境初始化

可能会有一些提示信息，安装过显卡驱动就可一了。
```
mkinitcpio -p linux
```
## 为 root 用户设置密码
```
passwd
```
## 设置网络连接
```
pacman -S iw wpa_supplicant networkmanager dialog

systemctl enable NetworkManager
```
## 安装引导程序
本文推荐 GRUB 作为引导程序。
```
pacman -Syu grub efibootmgr
pacman -Syu efibootmgr
grub-mkconfig -o boot/grub/grub.cfg
grub-install /dev/sda

```
## 退出Chroot
```
exit
unount -R /mnt
reboot
```
# ok，到此我们已经得到了命令行版的Archlinux








