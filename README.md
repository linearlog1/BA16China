# BA16China
install enviroment:

1. Stucture build enviroment (if you want to use docker please reference next "development install ")
Freescale Yocto Project User's Guide

2. Install SDK (the docker have installed SDK for development):
$ ./poky-glibc-x86_64-meta-toolchain-cortexa9hf-neon-toolchain-2.1.1.sh 
download point1: https://drive.google.com/open?id=0BwsoF62N38ecMk1VNVZ6bmM5S1k
download point2: https://pan.baidu.com/s/1qYB7xE0
Note: we provide docker to avoid restructure development enviroment

Docker development install :
$ sudo docker load --input Yocto2.1.tar
Yocto2.1.tar download
download point1: https://drive.google.com/open?id=0BwsoF62N38ecNmxVaEFqa1E4Zlk
download point2: https://pan.baidu.com/s/1pLycmXP

Docker development start:
$ sudo docker run -t -i -v /media/linearog/SSD21/Docker/Yocto4.1.15:/home/Data project/yocto:B003 /bin/bash
Note:
   /media/linearog/SSD21/Docker/Yocto4.1.15 is local folder
   
Yocto install:
1: dump yocto fsl-arm-yocto
$ mkdir fsl-arm-yocto-bsp
$ cd fsl-arm-yocto-bsp
$ repo init -u https://github.com/linearlog1/BA16China.git -b imx-4.1.15-1.0.0_ga
$ repo sync

2. index to BA16 i.MX6 project 
 a. i.MX6DL
$ DISTRO=poky EULA=1 MACHINE=imx6dl-dms-ba16 source ./fsl-setup-release.sh -b buildmxdl  
$ bitbake fsl-image-multimedia-full   

  a. i.MX6D
$ DISTRO=poky EULA=1 MACHINE=imx6q-dms-ba16 source ./fsl-setup-release.sh -b  build 
$ bitbake fsl-image-multimedia-full   


4.Build U-Boot/Kernel
Kernel source 
$ git clone https://github.com/linearlog1/linux-2.6-imx.git -b imx_4.1.15_1.0.0_ga
 
 ================ Build_Kernel.sh================================================
export ARCH=arm
source /opt/poky/2.1.1/environment-setup-cortexa9hf-neon-poky-linux-gnueabi
LOADADDR=0x10008000 make ARCH=arm CROSS_COMPILE=arm-linux-gnueabi- distclean
LOADADDR=0x10008000 make ARCH=arm CROSS_COMPILE=arm-linux-gnueabi- mx6dl_ba16_defconfig
# LOADADDR=0x10008000 make ARCH=arm CROSS_COMPILE=arm-linux-gnueabi- mx6q_ba16_defconfig
# make image
LOADADDR=0x10008000 make ARCH=arm CROSS_COMPILE=arm-linux-gnueabi- uImage
# make device tree
# LOADADDR=0x10008000 make ARCH=arm CROSS_COMPILE=arm-linux-gnueabi- imx6q-dms-ba16.dtb
 LOADADDR=0x10008000 make ARCH=arm CROSS_COMPILE=arm-linux-gnueabi- imx6dl-dms-ba16.dtb

# copy Kernel:
# cp -r arch/arm/boot/uImage /media/linearog/Boot\ imx6q-/
# umount /media/linearog/Boot\ imx6q-/ 
====================================================================================== 
 
  
uBoot Loader
u-boot source code
$ git clone https://github.com/linearlog1/u-boot.git
 
 ================ Build_u-boot.sh================================================
  export ARCH=arm
source /opt/poky/2.1.1/environment-setup-cortexa9hf-neon-poky-linux-gnueabi
LOADADDR=0x10008000 make  mrproper
LOADADDR=0x10008000 make dms-ba16-mx6dl_defconfig
# LOADADDR=0x10008000 make dms-ba16_defconfig  
# LOADADDR=0x10008000 make dms-ba16-1g_defconfig
LOADADDR=0x10008000 make 
==================================================================================

5. Burn Image
Write all Sdcard:
=====================================================
gunzip -c fsl-image-multimedia-full-imx6dl-dms-ba16.sdcard.gz | sudo dd of=/dev/sde bs=4M && sync
umount /media/linearog/Boot\ imx6q-/

bootLoader write:
=====================================================
sudo dd if=/dev/zero of=/dev/sdj bs=1k seek=384 count=129
sudo dd if=u-boot.imx of=/dev/sdj bs=1k seek=1 conv=fsync

copy Kernel:
====================================================
sudo cp -r arch/arm/boot/uImage /media/linearog/Boot\ imx6q-1/ 
sync
sudo cp -r arch/arm/boot/dts/imx6dl-dms-ba16.dtb /media/linearog/Boot\ imx6q-1/ 
sync
umount /media/linearog/Boot\ imx6q-/
