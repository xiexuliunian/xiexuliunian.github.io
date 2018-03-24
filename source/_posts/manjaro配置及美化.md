---
title: manjaro配置及美化
date: 2018-03-23 14:52:44
tags:
---
# <center>manjaro(麻将)配置及美化</center>
manjaro是一款基于archlinux的linux发行版，以前主要用`ubuntu`系统的，主要用来使用CUDA的，在ubuntu上的安装过程十分痛苦，第一次使用manjaro,在其上的安装过程让我惊叹，这么好用呀，所以就迷上了这款系统。


## 1. 系统安装
* 在制作启动盘时，使用其他的启动盘制作都会无法进行安装，使用rufus这款软件，以DD模式写入才可以安装。

*  跟在ubuntu上的安装过程十分类似，都是图形界面的，但是有一点，注意要在win10的EFI分区(本机是/dev/sda2 上选择`/boot/efi`标记),其余都是类似的，点下一步就ok了

## 2.系统更新及软件安装
## 更换中国源
```
sudo pacman-mirrors -b testing -c China  //选择中国源并更新  
sudo pacman -Syyu  //更新系统  
```
## 添加源
打开终端输入
```
sudo nano /etc/pacman.conf
```
在文件底部加入

```
[archlinuxcn]
SigLevel = Optional TrustedOnly
Server = https://mirrors.ustc.edu.cn/archlinuxcn/$arch
Server = https://mirrors.tuna.tsinghua.edu.cn/archlinuxcn/$arch
```
然后执行
`sudo pacman -Syy && sudo pacman -S archlinuxcn-keyring`
之后就可以从包管理器中搜索安装各种软件了enjoying，部分软件仍然需要配置，如搜狗输入法
```
sudo pacman -S fcitx-sogoupinyin
sudo pacman -S fcitx-im  #全部安装
suao pacman -S fcitx-configtool  #图形化配置工具
```
需要设置输入法变量
`sudo gedit ~/.profile(有可能是.xprofile)`,在其底部加入
```
export GTK_IM_MODULE=fcitx
export QT_IM_MODULE=fcitx
export XMODIFIERS=”@im=fcitx”
```
另外一个安装是使用AUR源，首先安装yaourt

`sudo pacman -S yaourt`

修改`/etc/yaourtrc`,去掉`# AURURL`的注释，修改为
```
AURURL="https://aur.tuna.tsinghua.edu.cn"
```

这样就可以使用AUR源了哟

## 3. 系统美化
## 来个配件大全

## 主题
Application:`Arc`

Cursor:`Xcursor-breeze`

Icons:`Numix-Circle-Light`

Shell:`Adapta-Nokto-Eta-Maia`

## 下面放美图
<center>{% asset_img 1.png 说明 %}</center>

