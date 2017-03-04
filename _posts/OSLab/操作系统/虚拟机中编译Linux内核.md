---
title:  虚拟机中编译Linux内核
date: 2016-11-13 20:35:26 # 文章生成时间，一般不改
categories:   # 文章分类目录，参数可省略
    - OSLab
    - 操作系统
tags:   # 文章标签，参数可省略

---
### 下载Linux源码
安装编译内核所需软件包
```bash
apt-get update
apt-get install build-essential kernel-package libncurses5-dev fakeroot 选择Y
```
在官网下载源码包：
http://www.kernel.org/pub/linux/kernel/v3.x/linux-1.1.1.tar.xz

### 配置内核
#### 放到/usr/src文件夹下，解压：
```bash
cd /usr/src
tar -Jxvf linux-3.19.3.tar.xz
```

#### 编译配置内核
```bash
cd linux-3.19.3  (cd命令进入源码目录)
make mrproper （得到源代码后,将其净化）
make menuconfig （对内核选项进行配置，不需要配置，直接enter）
cp /boot/config-'uname -r'-generic  ./.config （'uname -r'-generic指现版本内核）
make clean （删除配置时留下的一些不用的文件）
编译内核（需要很长时间，我用了有两小时，请耐心等待）
make bzImage
make modules  （编译模块）
```
#### 安装新内核
```bash
make modules_install   （安装模块）
make install
```

#### 启动新内核
```bash
制作启动盘
sudo mkinitramfs 3.19.3 -o /boot/initrd.img-3.19.3

grub menuentry /boot/grub/grub.cfg
根据内核的顺序选择第K个，赋值给/etc/default/grub中的GRUB_DEFAULT=K-1

更新grub2
sudo update-grub2
```
