如何编译驱动
============

## 先检查自己 raspbian 内核安装包的版本

```
pi@raspberrypi:~ $ apt show raspberrypi-kernel -a
Package: raspberrypi-kernel
Version: 1.20181112-1
Status: install ok installed
Priority: extra
Section: kernel
Source: raspberrypi-firmware
Maintainer: Serge Schneider <serge@raspberrypi.org>
Installed-Size: 118 MB
Provides: linux-image
Breaks: raspberrypi-bootloader (<< 1.20160324-1)
Replaces: raspberrypi-bootloader (<< 1.20160324-1)
Homepage: https://github.com/raspberrypi/firmware
Download-Size: unknown
APT-Manual-Installed: yes
APT-Sources: /var/lib/dpkg/status
Description: Raspberry Pi bootloader
 This package contains the Raspberry Pi Linux kernel.

```

可以看到当前版本号是 `1.20181112-1`.
如果执行过 `sudo apt update`, 那么在安装 `raspberry-kernel-headers` 包时要指定版本号，要与内核包版本号一致。


## [推荐]通过 apt 安装 `raspberrypi-kernel-headers`

```
sudo apt install raspberry-kernel-headers=1.20181112-1
```

## 手动通过源码安装 `raspberrypi-kernel-headers`

```
# 下载对应版本的内核源码 
wget https://github.com/raspberrypi/linux/archive/raspberrypi-kernel_1.20181112-1.tar.gz
tar -zxvf raspberrypi-kernel_1.20181112-1.tar.gz
sudo mv ~/Downloads/linux-raspberrypi-kernel_1.20181112-1 /usr/src/4.14.79-v7+
sudo ln -s /usr/src/4.14.79-v7+ /lib/modules/4.14.79-v7+/build
cd /lib/modules/4.14.79-v7+/build

# 清理编译环境
make mrproper

# 获取当前内核的配置或者通过下面命令来配置
sudo modprobe configs
sudo gzip -dc /proc/config.gz > .config

# Pi 2, Pi 3, Pi 3+, CM3 直接生成配置
#KERNEL=kernel7 make bcm2709_defconfig

# 生成编译内核所需要的文件
make modules_prepare

# 获取对应版本内核编译时生成的内核模块导出符号文件
curl -Ss https://raw.githubusercontent.com/raspberrypi/firmware/1.20181112/extra/Module7.symvers -o Module.symvers
```

## 安装编译驱动需要的工具

```
sudo apt install bc
```

## 回到驱动所在目录编译驱动

```
cd driver/
make 
sudo make install

# Enable module
sudo modprobe -v 8723bu
```

## 系统开启自动载入驱动 

```
# /etc/modules: kernel modules to load at boot time.
#
# This file contains the names of kernel modules that should be loaded
# at boot time, one per line. Lines beginning with "#" are ignored.
8723bu
```

## 对于 CM3/CM3+ 可以在修改 `/boot/config.txt` 修改默认的 GPIO 配置，例如

```
# Enable WiFi when boot. Set GPIO42 to be an output set to 1
gpio=42=op,dh
```

## 添加 WiFi 配置

添加以下内容到 `/etc/wpa_supplicant/wpa_supplicant.conf`

```
network={
    ssid="The_SSID_from_earlier"
    psk="Your_WiFi_password"
}
```
