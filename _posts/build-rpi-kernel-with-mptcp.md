---
title: 编译内核及移植MPTCP过程
tags: Linux MPTCP
categories: Linux
---



# 编译内核及移植MPTCP过程

## 编译方式

编译内核有两种方式：本地编译和交叉编译

* 本地编译是指在需要安装内核的设备上直接编译，编译得到的内核也是在本地执行
* 交叉编译是指一个在某个系统平台下可以产生另一个系统平台的可执行文件的编译器。交叉编译在目标系统平台（开发出来的应用程序序所运行的平台）难以或不容易编译时非常有用

## 编译过程

这里我的电脑系统为Ubuntu16.04， 设备为树莓派3B

<!--readmore-->

### 本地编译

由于是给树莓派编译内核，首先要连接到树莓派，这里使用ssh

如果使用以太网线将电脑和树莓派连接的话，键入下面的命令

```bash
ssh pi@raspberrypi.local
```

如果没有网线而树莓派已经接入网络的话

```bash
ssh pi@raspberry-pi's-ip-address
```

连接成功之后就进入了树莓派的bash界面，首先安装编译需要的工具和依赖

```bash
sudo apt-get install git bc
```

在树莓派上下载内核源码

```bash
git clone --depth=1 https://github.com/raspberrypi/linux
```

编译流程

```bash
cd linux
KERNEL=kernel7
make bcm2709_defconfig
make -j4 zImage modules dtbs
```

安装内核

```bash
sudo make modules_install
sudo cp arch/arm/boot/dts/*.dtb /boot/
sudo cp arch/arm/boot/dts/overlays/*.dtb* /boot/overlays/
sudo cp arch/arm/boot/dts/overlays/README /boot/overlays/
sudo cp arch/arm/boot/zImage /boot/$KERNEL.img
```



## 交叉编译

交叉编译相对负载一些，首先要先下载树莓派对应的交叉编译工具

```bash
git clone https://github.com/raspberrypi/tools
```

然后添加环境变量，这里假设将工具下载到了用户主目录

```bash
export PATH=$PATH:$HOME/tools/arm-bcm2708/gcc-linaro-arm-linux-gnueabihf-raspbian/bin
```

在电脑上下载对应内核源码

```bash
git clone --depth=1 https://github.com/raspberrypi/linux
```

进行编译

```bash
cd linux
KERNEL=kernel7
make ARCH=arm CROSS_COMPILE=arm-linux-gnueabihf- bcm2709_defconfig
make ARCH=arm CROSS_COMPILE=arm-linux-gnueabihf- zImage modules dtbs
```

树莓派的系统是安装在SD卡上的，所以我们要先将SD卡挂载到电脑上

插入SD卡，查看SD卡对应的设备文件

```bash
lsblk
```

应该可以看到下面格式的输出（具体内容可能不一样，我这里是sdb）

```
sdb
   sdb1
   sdb2
```

挂载SD卡，由于树莓派系统有两个分区，所以这里也对应的要挂载两个

```bash
mkdir mnt/fat32
mkdir mnt/ext4
sudo mount /dev/sdb1 mnt/fat32
sudo mount /dev/sdb2 mnt/ext4
```

然后安装内核

```bash
sudo make ARCH=arm CROSS_COMPILE=arm-linux-gnueabihf- INSTALL_MOD_PATH=mnt/ext4 modules_install
sudo cp mnt/fat32/$KERNEL.img mnt/fat32/$KERNEL-backup.img
sudo cp arch/arm/boot/zImage mnt/fat32/$KERNEL.img
sudo cp arch/arm/boot/dts/*.dtb mnt/fat32/
sudo cp arch/arm/boot/dts/overlays/*.dtb* mnt/fat32/overlays/
sudo cp arch/arm/boot/dts/overlays/README mnt/fat32/overlays/
```

最后解除挂载

```bash
sudo umount mnt/fat32
sudo umount mnt/ext4
```

## 移植MPTCP过程

### 关于编译方式

编译方式推荐交叉编译，理由如下

* 编译内核需要的时间十分漫长，而树莓派的运算能力远不如电脑，本地编译会浪费大量的时间
* 移植过程中经常会出现内核编译失败或者无法启动的情况。一旦出现，就无法远程连接树莓派，还需要一番折腾

### 更改配置文件

先选用默认的配置文件

```bash
make ARCH=arm CROSS_COMPILE=arm-linux-gnueabihf- bcm2709_defconfig
```

然后打开menuconfig，在对应的界面中启用与MPTCP相关的选项后保存

```bash
make ARCH=arm CROSS_COMPILE=arm-linux-gnueabihf- menuconfig
```

### DEBUG过程

由于有张博士给的代码，移植过程并不是特别漫长，不过由于张博士的代码是Android系统的内核，和树莓派代码有一定出入，所以经历了一段DEBUG的过程

不同的人遇到的BUG也不可能完全相同，因此这里我无法写出明确的DEBUG的路线，只能分享一点我的经验

* 不同的内核之间差异非常大，移植的话最好能够选择两个较为接近的版本，不要盲目求新，这样能节约下很多工作量

* 不要蛮干，对要移植的代码一定要有相应的知识储备，这样在遇到问题时才有可能意识到是哪里出了问题，当遇到Android系统内核和树莓派的内核代码有出入的时候才能理清是不是与MPTCP相关

* 保证细心，改动可能很多，一定要自己明白这段和要移植的功能有关再动手。如果这时候为了省时间偷懒，只会在后来的BEBUG上花费更多的时间

* DEBUG时确保自己没有任何错误再编译，编译内核需要很长时间，如果改一点点就编译，会浪费很多时间在等编译上

* 编译内核时的输出信息十分庞大，想要在这么多信息中找到错误很困难。这时可以将编译信息导出到文本，可以比较方便的查找warning、error等信息，具体操作如下

  ```bash
  make -j4 zImage modules dtbs > kernel.log
  ```

  这样会把编译信息保存到kernel.log文件中