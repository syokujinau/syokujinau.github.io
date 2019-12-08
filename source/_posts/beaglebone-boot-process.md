---
title: BeagleBone Boot Process
date: 2019-10-08 21:48:41
tags:
- Embedded Linux
- BeagleBone 
- Operating System
- Angstrom Linux
categories:
- [Embedded, Embedded Linux]
thumbnail: https://i.imgur.com/kR0epnW.png
---
> http://processors.wiki.ti.com/index.php/The_Boot_Process

![](https://i.imgur.com/ojSKnU5.png)


![](https://i.imgur.com/RchBfpo.png)

* RBL: **R**OM **B**oot **L**oader
* SPL: **S**econdary **P**rogram **L**oader
* MLO: **M**emory **LO**der
    1. https://stackoverflow.com/questions/27012822/how-mlo-minimal-bootloader-works
    2. https://groups.google.com/forum/#!topic/pandaboard/UIwIjwuDddM
    3. https://witekio.com/blog/writing-mlo-beagleboard-xm/

<!-- more -->

## 1. Test MLO and U-Boot

![](https://i.imgur.com/TR7ymG8.png)

1. Ubuntu16.04 on [VirtualBox](https://www.virtualbox.org/wiki/Downloads)
2. Using [GParted](https://gparted.org/download.php) to partition microSD card.
    * `BOOT` - fat16
    * `ROOTFS` - ext3
    * ![](https://i.imgur.com/9S8PIsE.png)
3. Mark `BOOT` as bootable
    * ![](https://i.imgur.com/w4W7amF.png)
4. Copy [MLO, U-boot.img, etc](https://drive.google.com/drive/folders/1Xh6qrFSIpL9bn_o8GpChu2Y6CExylJVR?usp=sharing) to boot partition
    * [Note] Should contain not only MLO and u-boot.img but all the files, otherwise it cannot read the u-boot correctly.
6. Connect to BBB J1 header pins to PC via USB to TTL
    1. ![](https://i.imgur.com/4dBrvE3.png)
    2. Insert the microSD to the SD card slot of BBB 
    3. Power on the BBB
    4. Press and hold the S2 button (BOOT button)
    5. Press the S3 button (POWER button)
    6. Release the S2 button
7. Correct U-Boot logs 
    * ![](https://i.imgur.com/gdzMNQ2.png)
8. Now, we can use u-boot to boot the kernel.

## 2. Boot Linux Kernel from ROOTFS


* Mount SD card
* `BOOT` copy from [here](https://github.com/syokujinau/Embedded_Linux_Resources/tree/master/Uboot_demo_linux_kernel/AngstromDistribution/BeagleBone_LinuxBuild/AngstromDistribution/Angstrom-v2012.12%20-%202013.06.20)
    1. MLO
    2. u-boot
    3. uEnv
* `ROOTFS`
    * copy all files from [here](https://github.com/syokujinau/Embedded_Linux_Resources/blob/master/Uboot_demo_linux_kernel/Angstrom-systemd-image-eglibc-ipk-v2012.12-beagleboard.rootfs.tar.xz)
    * :warning: Do NOT extract it on Windows which uses a different file system.


![](https://i.imgur.com/kR0epnW.png)


## 3. Get in to Uboot Prompt

* Press RESET button on board
* Keep pressing the `space` key
* ![](https://i.imgur.com/ooHcnrI.png)
    * Enter `help` to list all commands
* Enter `load mmc 0`
    * **MMC0** interface: microSD card  
    * **MMC1** interface: eMMC 
    * :cake: 
        * To load **FAT** based file system in to memory, use `fatload`
        * To load a file from any file system, use `load`










