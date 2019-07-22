# buildroot_raspberrypi3
Building Raspberry Pi 3 System with Buildroot

We are using Virtual Box with Ubuntu LTS Version 16.04 for compiling buildroot

Buildroot Long Term Stable Version is = 2019.02.4

Introduction
------------

In this project we shall do the following
  1. Build linux systems for Raspberry Pi 3 using Buildroot
  2. Connect Raspberry Pi 3 using USB to TTL cable on Windows 10 (3.3 driver version, 3.8 doesn't work)
  3. Configure network interface to connect via RJ45 cable

Download Buildroot and depedencies
----------------------------------

  1. Read buildroot manual and install all dependencies in Ubuntu
  2. Download buildroot LTS version from

https://buildroot.org/downloads/buildroot-2019.02.4.tar.gz

  3. Download Windows Prolific serial console driver version 3.3 and install it as below
  
     a. 3.8 version that comes with Windows is BUGGY
     
     b. It will show Yellow exclamation mark and serial console shall not be detected
     
<p align="center">
  <img src="https://github.com/nbhatia911/buildroot_raspberrypi3/blob/master/Downloads/01_device_manager.png?raw=true" width="320"/>
  <img src="https://github.com/nbhatia911/buildroot_raspberrypi3/blob/master/Downloads/02_device_manager.png?raw=true" width="320"/>
  <img src="https://github.com/nbhatia911/buildroot_raspberrypi3/blob/master/Downloads/03_device_manager.png?raw=true" width="320"/>
  <img src="https://github.com/nbhatia911/buildroot_raspberrypi3/blob/master/Downloads/04_device_manager.png?raw=true" width="320"/>
  <img src="https://github.com/nbhatia911/buildroot_raspberrypi3/blob/master/Downloads/05_device_manager.png?raw=true" width="320"/>
  <img src="https://github.com/nbhatia911/buildroot_raspberrypi3/blob/master/Downloads/raspberrypi3_gpio_diagram.jpg?raw=true" width="320"/>
  <img src="https://github.com/nbhatia911/buildroot_raspberrypi3/blob/master/Downloads/raspberrypi3_usb_ttl.jpg?raw=true" width="640"/>
</p>
  4. Download PUTTY to connect to PI
  5. Download HDDRawCopy1.10Portable from hddguru.com to flash image to PI sdcard
  6. USB to TTL serial cable.

Configure Buildroot
------------------

  1. Untar buildroot-2019.02.4.tar.gz. Below are the folders you should see
  
<pre>
utils
toolchain
system
support
package
linux
fs
configs - Contains Raspberry Pi configs raspberrypi3_64_defconfig
boot
board
arch
docs
</pre>
  
  2. Run below cmd to load RPI config.

<pre>
  $ make raspberrypi3_64_defconfig #loads rpi config
  $ make menuconfig #select modules to install and save
</pre>
  
  3. .config file shall be generated, verify BR2_ROOTFS_POST_BUILD_SCRIPT is set. If not then image for flashing shall not be generated
  
  4. Download all sources first and run make. This will download everything needed for offline compilation to dl/ folder
<pre> 
  $ make source 
  $ make
</pre>


  
Result of the build
-------------------

After building, images shall be created in output folder as below. 
<pre> 
output/images/
output/images/bcm2710-rpi-3-b.dtb
output/images/boot.vfat
output/images/bcm2710-rpi-3-b-plus.dtb
output/images/sdcard.img
output/images/rpi-firmware
output/images/rpi-firmware/overlays
output/images/rpi-firmware/start.elf
output/images/rpi-firmware/fixup.dat
output/images/rpi-firmware/cmdline.txt
output/images/rpi-firmware/bootcode.bin
output/images/rpi-firmware/config.txt
output/images/bcm2837-rpi-3-b.dtb
output/images/rootfs.ext4
output/images/Image
output/images/rootfs.ext2
</pre>

  
 
How to write the SD card
------------------------

Once the build process is finished you will have an image called "sdcard.img" in the output/images/ directory.

Use sdcard.img to flash using HDD tool
On Ubuntu copy the bootable "sdcard.img" onto an SD card with "dd":

<pre>
  $ sudo dd if=output/images/sdcard.img of=/dev/sdX
</pre>

Insert the SDcard into your Raspberry Pi, and power it up. Your new system
should come up now and start two consoles: one on the serial port on
the P1 header, one on the HDMI output where you can login using a USB
keyboard.

How to connect serial cable
---------------------------

Serial cable will have Red, Black, Green and White

Standary connectivity is as below. Red is free, DO NOT connect it.
<pre>
PIN NO     CABLE COLOR  
6      -   Ground Black
8      -   TX White
10     -   RX Green
</pre>


It is possible that White and Green are reversed, so try reversing if you see no output on PUTTY

Linux Boot Output from Serial Console
-------------------------------------
<pre>
[    0.000000] Booting Linux on physical CPU 0x0
[    0.000000] Linux version 4.14.95-v8 (root@tpi-ubuntu16-04-5) (gcc version 7.4.0 (Buildroot 2019.02.4)) #1 SMP PREEMPT Sun Jul 21 16:11:39 IST 2019
[    0.000000] Boot CPU: AArch64 Processor [410fd034]
[    0.000000] Machine model: Raspberry Pi 3 Model B Rev 1.2
[    0.000000] CPU features: enabling workaround for ARM erratum 845719
[    0.000000] Built 1 zonelists, mobility grouping on.  Total pages: 232848
[    0.000000] Kernel command line: 8250.nr_uarts=1 bcm2708_fb.fbwidth=720 bcm2708_fb.fbheight=480 bcm2708_fb.fbswap=1 smsc95xx.macaddr=B8:27:EB:79:F5:8B vc_mem.mem_base=0x3ec00000 vc_mem.mem_size=0x40000000  root=/dev/mmcblk0p2 rootwait console=tty1 console=ttyS0,115200
</pre>





OUTPUT FROM RASPBERRY PI 3 SHELL
--------------------------------

<pre>

~] cat /etc/*rel*
NAME=Buildroot
VERSION=2019.02.4
ID=buildroot
VERSION_ID=2019.02.4
PRETTY_NAME="Buildroot 2019.02.4"

~] uname -a
Linux buildroot 4.14.95-v8 #1 SMP PREEMPT Sun Jul 21 16:11:39 IST 2019 aarch64 GNU/Linux

~] cat /proc/cpuinfo
processor       : 0
BogoMIPS        : 38.40
Features        : fp asimd evtstrm crc32 cpuid
CPU implementer : 0x41
CPU architecture: 8
CPU variant     : 0x0
CPU part        : 0xd03
CPU revision    : 4

processor       : 1
BogoMIPS        : 38.40
Features        : fp asimd evtstrm crc32 cpuid
CPU implementer : 0x41
CPU architecture: 8
CPU variant     : 0x0
CPU part        : 0xd03
CPU revision    : 4

processor       : 2
BogoMIPS        : 38.40
Features        : fp asimd evtstrm crc32 cpuid
CPU implementer : 0x41
CPU architecture: 8
CPU variant     : 0x0
CPU part        : 0xd03
CPU revision    : 4

processor       : 3
BogoMIPS        : 38.40
Features        : fp asimd evtstrm crc32 cpuid
CPU implementer : 0x41
CPU architecture: 8
CPU variant     : 0x0
CPU part        : 0xd03
CPU revision    : 4
</pre>

