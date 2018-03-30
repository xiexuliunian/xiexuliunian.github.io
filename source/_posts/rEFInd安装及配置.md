---
title: rEFInd安装及配置
date: 2018-03-30 09:36:39
tags:
---
# <center>rEFInd安装及配置</center>
##  `注意`  rEFInd引导项仅支持EFI引导模式，对传统的BIOS引导不支持
## 1.安装
本机使用的是manjaro archlinux 发行版，在安装rEFInd上比较方便，直接 [源码](https://sourceforge.net/projects/refind/ "这是rEFInd的源码连接")，得到一个`refind-bin-0.11.2.zip`的压缩包，解压后转到该文件夹下。

```
sudo chmod +x redind-install //为安装文件赋予权限
```
```
sudo ./refind-install //安装
```
ok，就这么简单咯，就可以使用`rEFInd`接管丑陋的`grub`开机引导界面了

## 2.主题美化
然而我们发现基本的rEFInd引导界面也不是很好看，为它换个主题吧。看中了这个主题。[github页面](https://github.com/EvanPurkhiser/rEFInd-minimal "这是该主题的github页面")
<center>{% asset_img 1.png 说明 %}</center>

## 用法

> 1.安装好rEFInd后，首先定位到计算机的refind EFI目录，一般在`/boot/EFI/refind（注意进入到该目标需要首先进入管理员身份su才行)目录下`

> 2.在该文件夹下建立`themes`文件夹

> 3.将该主题`clone`到`themes`文件夹,在`themes`文件夹下输入命令
```
git clone https://github.com/EvanPurkhiser/rEFInd-minimal.git
```
> 4.为了使该主题生效，需要将`include themes/rEFInd-minimal/theme.conf`语句添加到配置文件`refind.conf`中去

ok,重启吧。看看新的主题。

## 3. rEFInd 卸载
想卸载rEFInd，没问题，很简单哟。在管理员权限下运行命令
```
rm -r /boot/efi/EFI/refind
```
重启你就会看到久违的grub引导界面
 
