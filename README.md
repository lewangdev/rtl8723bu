如何编译驱动
============

```
# 安装编译驱动需要的工具
sudo apt install bc

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

# 回到驱动所在目录编译驱动
make 
sudo make install

# Enable module
sudo modprobe -v 8723bu
```
