# Cotton-Candy Research

It uses Samsung Exynos 4210 Soc which is ArmV7-A

https://web.archive.org/web/20190209034644/https://sites.google.com/site/tingox/cotton_candy

https://forum.xda-developers.com/showthread.php?t=1661882&page=2

https://github.com/FXITech

Ubuntu-beta-20130309.zip

http://linux-exynos.org/wiki/Main_Page

## down
http://s3.amazonaws.com/ubuntu_test/cc_ubuntu_2014_01_28.img.zip

https://web.archive.org/web/20140612054004/http://download.cstick.com:80/categories/linux_releases

https://drive.google.com/drive/folders/1AnvkNviNJQAPcr22hA5KKuxfPzFRKb2M?usp=sharing



# Compile U-Boot with

https://releases.linaro.org/archive/12.04/components/toolchain/binaries/gcc-linaro-arm-linux-gnueabi-2012.04-20120426_linux.tar.bz2 (available in google drive if no longer available)

Use ubuntu 18.04 x86_64

Install the build essentials

```
sudo apt-get install build-essential
```


Since the ubuntu were using is 64 bit and the toolchain is 32 bit

```
sudo dpkg --add-architecture i386
sudo apt-get update
sudo apt-get dist-upgrade
```


To compile this do the following:
```
CROSS_COMPILE=arm-linux-gnueabi- make distclean
CROSS_COMPILE=arm-linux-gnueabi- make smdkv310_config
CROSS_COMPILE=arm-linux-gnueabi- make

cd sd_fuse
sudo ./c210-fusing.sh /dev/sdb // write the boot loader to the sd card
```
