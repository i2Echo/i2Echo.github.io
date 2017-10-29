---
title: Ubuntu折腾记
tags: [Linux, ubuntu, 操作系统, 网卡驱动]
date: 2016-09-26 03:28:48
categories: Linux
---
我先声明，我真的不是处女座，但是发现我有时候真的很强迫症，正好用命令行用得顺手，但总是很多命令被报错，装各种软件出问题，归根结底还是window对那些不太友好，心里还是想着用Linux试试看？虽然电脑里头虚拟机装着Ubuntu以及XOS，但是总归不方便，程序一多卡的要死，正好想起有个闲置的老笔记本，干脆就拿她专门做Linux机用；<!--more-->自己比较习惯用Ubuntu，就装Ubuntu好了，考虑到机器的性能和配置比较低，我选择的是Ubuntu12.04LTS版本。

### 制作U盘启动盘
准备工作：

1. 已格式化的U盘一个
2. Ubuntu的ISO文件
3. UltraISO软件

开始制作：

1. 安装并打开UltraISO软件。
2. 选择文件打开![文件选择](/images/upload/written_ISO_1.png "启动写入映像")加载下好的Ubuntu ISO文件![加载ISO文件](/images/upload/written_ISO_2.png "选择要加载ISO文件")
3. 点击启动写入硬盘映像弹出写入对话框
4. 配置项，选择要写入的U盘以及写入方式一般为`USB-HDD+`，然后点便捷启动，选择写入新的驱动器引导扇区，选择Syslinux，最后点击写入等待写入完成就OK了。![配置页面](/images/upload/written_ISO_4.png "配置页面")

### 安装系统
准备工作：

1. 一台待安装的电脑
2. Ubuntu的U盘启动盘

开始安装：

1. 开机狂按F12进入BIOS进行设置(不同机器进入方式不一样，不过多解释)，设置启动方式，默认是硬盘启动，我们需要改为U盘启动。
2. 设置完成之后插上U盘重启电脑，稍等片刻你就可以看到Ubuntu的启动界面了。
3. 按照提示步骤安装就好(这步不会的网上自行搜索)。
4. 等待20~30分钟安装完成即可。

### 网卡驱动问题
总算安装完成了，由于手头并没有网线，就想着想直接用无线网，但是发现无线网的图标是暗着的。![未启用的无线网](/images/upload/disable_network.png "无线未启用")也不能开启自动搜寻；估摸着肯定是无线网卡的驱动没装好；那么要安装驱动肯定得先知道网卡的型号，需要检测硬件信息。
打开Terminal(命令行终端)输入：
``` bash
lspci | grep network 
# lspci是显示所有PCI总线设备的命令
# grep Network是匹配所有包含Network的信息
```
![网卡信息](/images/upload/get_network.png "网卡信息")
当然如果你想查看更详细的信息，也没问题
``` bash
lshw -C network
# lshw是显示硬件信息 network表示网卡相关信息 参数-C是表示详细信息

```
![详细网卡信息](/images/upload/hw_info_network.png "详细网卡信息")
这样就得到网卡相关的信息了，我的是Broadcom(博通)的，型号是BCM4312；到Broadcom的官网找到对应驱动([这里](http://www.broadcom.com/support/802.11)),我的是32位的系统就下载32位的(我下的是hybrid-v35-nodebug-pcoem-6_30_223_271.tar.gz)；下好之后用U盘转到Ubuntu系统的电脑里。

### 网卡驱动安装
根据官网的安装教程说明([传送门](http://www.broadcom.com/docs/linux_sta/README_6.30.223.271.txt))，来一步步来安装。
拿到驱动文件后，解压到你指定的文件下：
```bash
mkdir hybrid_wl
cd hybrid_wl
tar xzf <path>/hybrid-v35-nodebug-pcoem-portsrc.tar.gz #path为你驱动文件所在路径

```
#### 编译
使用make命令编译
```bash
make clean   (optional) #清除前面编译生成的可执行文件，这句命令可选，一般新安装不用
make

```
#### 配置
载入模块
```bash
sudo depmod
sudo modprobe wl
```
因为我是第一次安装，在文档中看到需要删除跟要安装驱动有冲突的模块并加黑名单：
> Fresh installation:
> 1: Remove any other drivers for the Broadcom wireless device.

> There are several other drivers (besides this one) that can drive 
Broadcom 802.11 chips. These include b43, brcmsmac, bcma and ssb. They will
conflict with this driver and need to be uninstalled before this driver
can be installed.  Any previous revisions of the wl driver also need to
be removed.

> Note: On some systems such as Ubuntu 9.10, the ssb module may load during
boot even though it is blacklisted (see note under Common Issues on how to
resolve this. Nevertheless, ssb still must be removed
(by hand or script) before wl is loaded. The wl driver will not function 
properly if ssb the module is loaded.

根据文档说明，需要先移除一些模块，具体操作继续往下看：
```bash
lsmod  | grep "brcmsmac\|b43\|ssb\|bcma\|wl" 
#表示显示匹配右边这几个模块，有的下面就删除

# If any of these are installed, remove them:
rmmod b43
rmmod brcmsmac
rmmod ssb
rmmod bcma
rmmod wl
# 如权限不够都加上sudo

# To blacklist these drivers and prevent them from loading in the future:
echo "blacklist ssb" >> /etc/modprobe.d/blacklist.conf
echo "blacklist bcma" >> /etc/modprobe.d/blacklist.conf
echo "blacklist b43" >> /etc/modprobe.d/blacklist.conf
echo "blacklist brcmsmac" >> /etc/modprobe.d/blacklist.conf

# 如若上面出现权限不够，就算用sudo还是权限不够(我就碰到了)，就改为如下命令
sudo sh -c 'echo "blacklist ssb" >> /etc/modprobe.d/blacklist.conf'
# 这里出现权限不够是因为重定向符号 “>” 和 ">>" 也是 bash 的命令，
# 使用sudo只让echo命令具有root权限，后面的">>"并没有root权限，
# 所以bash拒绝写入目标文件，而使用"sh -c"命令可以让bash将一个字符串作为完整的命令行，
# 从而sudo的权限就扩展到整条命令了
```
#### 安装
``` bash
sudo modprobe lib80211 
# If your using the cfg80211 version of the driver, then cfg80211 needs to be loaded:
sudo modprobe cfg80211 #我的用的这个版本就装了，在前面显示那些驱动模块的时候可以看到

sudo insmod wl.ko
```
执行完了，就发现无线网卡启动了并开始搜寻附近WiFi了。
![网卡启动了](/images/upload/enable_network.png "启动成功")
**如果执行完了，什么也没发生，在文档里也有说明。**
> **If the wl driver loads but doesn't seem to do anything:**
  the ssb module may be the cause.  Sometimes blacklisting ssb may not
  be enough to prevent it from loading and it loads anyway. (This is mostly
  seen on Ubuntu/Debian systems).

> Check to see if ssb, bcma, wl or b43 is loaded:
> ```bash
lsmod | grep "brcmsmac\|ssb\|wl\|b43\|bcma"

# If any of these are installed, remove them:

rmmod brcmsmac
rmmod ssb
rmmod bcma
rmmod wl
insmod wl

# Back up the current boot ramfs and generate a new one:
cp /boot/initrd.img-`uname -r` somewheresafe
update-initramfs -u
reboot
```

#### 设为开机自启动
这一步非常重要，我当时装完了，可以上网，然后一高兴以为可以了，就直接关机睡觉了(太晚了)，第二天一开机，我去，居然又不行了，一看文档原来是没有设为开机自启动，然后又按照上面的配置安装步骤来一遍就好了；然后设置到开机自启动：
```bash
# 注意要先转到前面驱动解压的hybrid_wl文件目录
cp wl.ko /lib/modules/`uname -r`/kernel/drivers/net/wireless 
depmod -a

echo modeprobe wl >> /etc/rc.local
#这里出现权限不够错误时加上sudo sh -c
```
到这里重启电脑，发现无线网卡驱动正确的，ok！perfect！