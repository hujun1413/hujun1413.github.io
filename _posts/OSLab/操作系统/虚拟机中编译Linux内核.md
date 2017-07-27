---
title:  虚拟机中编译Linux内核
date: 2016-11-13 20:35:26 # 文章生成时间，一般不改
categories:   # 文章分类目录，参数可省略
    - OSLab
    - 操作系统
tags:   # 文章标签，参数可省略

---
### 参考资料
http://blog.csdn.net/zhangnn5/article/details/6766693
<!--more-->
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
cp /boot/config-'uname -r'-generic ./.config （'uname -r'-generic指现版本内核）
make menuconfig（对内核选项进行配置，不需要配置，直接enter）
#若make menuconfig失败，执行apt-get install libncurses5-dev
make clean （删除配置时留下的一些不用的文件）
编译内核（需要很长时间，我用了有两小时，请耐心等待）
make bzImage -j4
make modules -j4 （编译模块）
```
#### 安装新内核
```bash
make modules_install   （安装模块）
make install
```

#### 启动新内核
```bash
/etc/default/grub
/boot/grub/grub.cfg

制作启动盘
sudo mkinitramfs 3.19.3 -o /boot/initrd.img-3.19.3

grep menuentry /boot/grub/grub.cfg
根据内核的顺序选择第K个，赋值给/etc/default/grub中的GRUB_DEFAULT=K-1

更新grub2
sudo update-grub2

更改grub启动项
‘GRUB_SAVEDEFAULT’
If this option is set to ‘true’, then, when an entry is selected, save it as a new default entry for use by future runs of GRUB. This is only useful if ‘GRUB_DEFAULT=saved’; it is a separate option because ‘GRUB_DEFAULT=saved’ is useful without this option, in conjunction with grub2-set-default. Unset by default. This option relies on the environment block, which may not be available in all situations (see Environment block).

‘GRUB_DEFAULT’
If you set this to ‘saved’, then the default menu entry will be that saved by ‘GRUB_SAVEDEFAULT’ or grub2-set-default. This relies on the environment block, which may not be available in all situations (see Environment block). 

或者直接GRUB_DEFAULT=“Ubuntu, Linux 3.1.1”
```

### 将自己写的驱动编译进内核
1. 将自己写的Monitor文件夹添加到/usr/src/linux-3.1.1/driver/net/
2. 修改/driver/net/目录下的Kconfig文件，在E1000项的下面增加：
```
config MONITOR
    tristate "OSLab(R) MyMonitor driver"
    default m
```
3. 修改/driver/net/Makefile为:
```
obj-$(CONFIG_MONITOR) += Monitor/
```
4. 修改/driver/net/Monitor/Makefile为:
```
obj-$(CONFIG_MONITOR) += MyMonitor.o
```
执行make menuconfig时找到MONITOR对应的描述“OSLab(R) MyMonitor driver”，选择y即可编译进内核，选m即可动态加载进内核

总结：
* make menuconfig读取Kconfig的内容然后让用户选择编译方式，用户选择完毕后生成linux-3.1.1/.config文件
* linux-3.1.1/.config文件里面定义了如“CONFIG_SKY2=m”这样的行。
* /driver/net/Makefile文件里面定义了如"obj-$(CONFIG_SKY2) += sky2.o"这样的行。

### 文件介绍
1. vmlinuz是可引导的、压缩的内核

2. initrd.img的作用：initrd.img是Linux启 动过程中很重要的一个文件，如果你编译内核时将一部分功能编译为可加载模块。如果系统的一些设备的驱动编译为可加载模，那么启动时如果没有指定 INITRD=/path_to_initrd.img，那么系统启动或者会失败，或者启动后会有设备无法使用（像网卡或者其它设备）。如果没指定initrd.img或者指定的initrd.img中并没有包含正确的驱动模块，则系统启动时会挂起，并报告"kernel panic: VFS: Unable to mount root fs on 08:06"的错误。

3. make modules_install将把编译之后的模块放到/lib/modules/
4. 
### [系统启动自动加载模块](http://blog.csdn.net/hunanchenxingyu/article/details/38867931)
vim /etc/modules，加入新的一行，写要自动加载的模块名
内核模块参考资料：
[浅谈linux中的模块加载](http://blog.chinaunix.net/uid-20568790-id-1632313.html)
[Kernel modules (简体中文)](https://wiki.archlinux.org/index.php/Kernel_modules_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87))