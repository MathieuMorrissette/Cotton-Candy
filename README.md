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

# Compiling Kernel

```
export ARCH=arm

CROSS_COMPILE=arm-linux-gnueabi- make distclean
CROSS_COMPILE=arm-linux-gnueabi- make fxi_c210_ubuntu_defconfig
CROSS_COMPILE=arm-linux-gnueabi- make
```

if you get perl error such as https://web.archive.org/web/20190210004339/https://lkml.org/lkml/2012/11/18/159
```
Can't use 'defined(@array)' (Maybe you should just omit the defined()?) at kernel/timeconst.pl line 373.
```
Replace defined(@array) by !@val


# Android Installing SSH

https://dan.drown.org/android/
https://dan.drown.org/android/howto/ssh-server.html

ln -s /system/bin/sh /system/xbin/ash

## run ssh at boot
add this to /init.rc

just above

```
## Daemon processes to be run by init.
##
```

add 

```
on property:sys.boot_completed=1
    start dropbear
```

at the end add
```
# dropbear
service dropbear /data/local/bin/dropbear
    user root
    oneshot
```

## install arch linux
take this
https://archlinuxarm.org/platforms/armv7/samsung/odroid-u2


https://technohackerblog.blogspot.com/2016/07/running-arch-linux-in-chroot-on-android.html

```
chroot /data/chroot /bin/bash

/bin/sh -c "haveged -w 1024; pacman-key --init; pkill haveged
```

### setup chroot on boot

add this service 

```
# setup chroot
service archlinux /system/bin/sh /data/chroot/setupchroot.sh
    user root
    oneshot
```

setupchroot.sh
```
#!/system/bin/sh
mount -o bind /dev/ /data/chroot/dev/
mount -t proc proc /data/chroot/proc/
mount -t sysfs sysfs /data/chroot/sys/
mount -t tmpfs tmpfs /data/chroot/tmp/
mount -t devpts devpts /data/chroot/dev/pts/
```


and start it before dropbear and ssh-p2p

## p2p ssh

https://github.com/MathieuMorrissette/ssh-p2p

(run server in cotton candy)

### to start server on boot
init.rc
``` 
# p2p ssh
service ssh-p2p /system/bin/chroot /data/chroot/ ~/go/bin/ssh-p2p server -key="guid" -dial="127.0.0.1:22"
    user root
    oneshot
```

add this to /init.rc

just above

```
## Daemon processes to be run by init.
##
```

add 

```
on property:sys.boot_completed=1
    start dropbear
    start ssh-p2p
```


### to connect
ssh-p2p client -key=$KEY -listen=127.0.0.1:2222
ssh root@127.0.0.1 -p 2222


## Source
https://archlinuxarm.org/platforms/armv7/samsung/odroid-xu4 for the base image
https://fredericb.info/2018/03/emulating-exynos-4210-bootrom-in-qemu.html
https://lists.gnu.org/archive/html/qemu-devel/2012-09/msg00414.html
