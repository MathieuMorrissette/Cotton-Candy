# Cotton-Candy Research

It uses Samsung Exynos 4210 Soc which is ArmV7-A, It has almost the same specs as the Samsung Origen Board

# Linux


## Compile U-Boot

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

## Compiling Kernel

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


# Android

https://dan.drown.org/android/
https://dan.drown.org/android/howto/ssh-server.html

Create ash as it is required by ssh
```
ln -s /system/bin/sh /system/xbin/ash
```

### Installing Chroot Arch Linux
Use the following image :
https://archlinuxarm.org/platforms/armv7/samsung/odroid-u2

Transfer it to the device with the adb shell 
and extract it using tar.

https://technohackerblog.blogspot.com/2016/07/running-arch-linux-in-chroot-on-android.html

```
chroot /data/chroot /bin/bash

/bin/sh -c "haveged -w 1024; pacman-key --init; pkill haveged
```

### Create Services On Boot

add this service 

```
# setup chroot
service archlinux /system/bin/sh /data/chroot/setupchroot.sh
    user root
    oneshot
    
# p2p ssh
service ssh-p2p /system/bin/sh /data/chroot/setupsshp2p.sh
    user root
    oneshot
    
# dropbear
service dropbear /data/local/bin/dropbear
    user root
    oneshot
    
# sshd 
service sshd /system/bin/sh /data/chroot/setupsshd.sh
   user root
   oneshot
   
# openvpn
server openvpn /system/bin/sh /data/chroot/setupopenvpn.sh
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

setupsshp2p.sh
```
#!/system/bin/sh
/system/bin/chroot /data/chroot/ /data/go/bin/ssh-p2p server -key="key" -dial="127.0.0.1:22"
```
setupsshd.sh
```
#!/system/bin/sh

/system/bin/chroot /data/chroot/ /sbin/sshd
```

setupopenvpn.sh
```
#!/system/bin/sh

/system/bin/chroot /data/chroot/ /sbin/openvpn /home/alarm/VPN-Digital-Ocean/client.ovpn
```

### Start Services on Boot

Just above this in the init.rc file 
```
## Daemon processes to be run by init.
##
```

add the following handler :

```
on property:sys.boot_completed=1
    start archlinux
    start dropbear
    start sshd
    start sshp2p
    start openvpn
```

## OpenVPN Client

We must create the tun interface manually with the following commands (in chroot) :


```
sudo mkdir /dev/net
sudo mknod /dev/net/tun c 10 200
```

# References

## SSH-p2p

https://github.com/MathieuMorrissette/ssh-p2p


### To connect
ssh-p2p client -key=$KEY -listen=127.0.0.1:2222
ssh root@127.0.0.1 -p 2222

## Google Drive
https://drive.google.com/drive/folders/1AnvkNviNJQAPcr22hA5KKuxfPzFRKb2M?usp=sharing

## Source
https://fredericb.info/2018/03/emulating-exynos-4210-bootrom-in-qemu.html

https://lists.gnu.org/archive/html/qemu-devel/2012-09/msg00414.html

https://web.archive.org/web/20190209034644/https://sites.google.com/site/tingox/cotton_candy

https://forum.xda-developers.com/showthread.php?t=1661882&page=2

https://github.com/FXITech

http://s3.amazonaws.com/ubuntu_test/cc_ubuntu_2014_01_28.img.zip

https://web.archive.org/web/20140612054004/http://download.cstick.com:80/categories/linux_releases

http://linux-exynos.org/wiki/Main_Page

# Keywords
Ubuntu-beta-20130309.zip
