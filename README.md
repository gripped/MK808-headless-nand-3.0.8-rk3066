Install Picuntu on Intenal Nand
===============================

First of all this is in a state of flux and may be broken at any time.
I would have liked to fork repos and pull from here and there but do not yet know much about git so I'm uploading this as a whole.
Credits to AndrewDB , Omegamoon, alok & finless bob

The kernel tree is from
https://github.com/aloksinha2001/picuntu-3.0.8-aloksinha2001
The toolchain is taken from Omegamoons tree
https://github.com/omegamoon/rockchip-rk30xx-mk808
The original initramfs.cpio is from
https://github.com/Galland/rk30_linux_initramfs.github
the mkbootimg is from
https://github.com/Galland/rk30_linux_initramfs.git

The config is based on Picuntu 0.9 RC2 as RC3 was throwing up hung process errors


I started this as my intention is to run a mail relay on my MK808 of my home fibre connection. 
(I have some cheap, but fair powerful for the price, servers where the provider blocks port 25 and I don't want to use their relay if I can avoid it)
I don't need Android on the device at all and it seems a waste to leave the internal nand unused.

First I wanted to get a functional Picuntu on the internal Nand, this is done.
Next I will be optimizing the kernel for headless running. Anything not needed will be disposed of.
I'm still waiting for a Network switch and once I have it wifi will be going from my build as well( but I'll leave configs in place)

Only tested on MK808. I do have a MK808B as well so may try this later (no wifi or BT) but other models YOU ARE ON YOUR OWN

This will stop Android working on your device (though you can flash it back)
If you don't understand why I suggest you stop here.


Doing it !   (Don't blame me)
=======================

Install Picuntu 0.9 RC2 and make sure its working fully on the sdcard.

The kernel build tree is self contained and I think you should be able to build without to much extra packages
Of the top of my head I had to install SHA something and LZO something (I'm on arch)
Look at this compilation errors if any and you'll work out what missing



You need to have your MTD block laid out as I have set up in the configs Kernel CONFIG_CMDLINE

The parameter file is in the root directory "parameter1gb"

Flash Finless bobs 1.7 MK808 kernel and you get the right layout
http://www.freaktab.com/showthread.php?3857-NEW-MK808-quot-all-models-quot-Finless-1-7

Copy ./modules/rk30xxnand_ko.ko.3.0.8+ to the /lib/modules/kernel/rk30xxnand_ko.ko on your sdcard and on the MK808 run depmod -a

run ./build_rk3066_standard

and in BUILT you will find sdcard_boot.img.

Flash it to boot 

On picuntu
---------

modprobe rk30xxnand_ko.ko

cat /proc/mtd 

should show mtd partition. If not its not working , figure it out.

mkfs.ext4 /dev/mtdblock9

mount /dev/mtdblock9 mnt

rsync -avx / /mnt

umount /mnt

On PC
----

run ./build_rk3066_mtd
and in BUILT you will find mtdboot.img.
Flash to boot

Everything works and you are happy.

As I say this is stiil a work in progress
The modules these compilatons produced did not work (missing symbols) but the set already on my picuntu install did.
I will investigate

The rk30xxnand_ko.ko I took from the MK808 rom worked on Picuntu but had were odd on my PC. (cp cannot stat)
So I took one from here
https://github.com/jin-eld/cm_rk3066_ventos97/blob/master/ramdisk/rk30xxnand_ko.ko.3.0.8%2B

It might brick your entire life ?










picuntu-3.0.8-alok
==================

rk3066-kernel - derived from mix of andrew source, omegamoon, 0.3a kernel, driver sources

Integrated kernel for Picuntu 3.0.8-alok+ 
=============

Linux/Android kernel for the Rockchip RK3066 SoC

Changelog - Kernel - 3.0.8-alok+
- Cleaned the previous kernel
- Removed unwanted TV Tuner cards
- Bluetooth support retained
- All cpufreq governors now available - Try userspace, and hav fun.. (Read warning below)
- Default governor Ondemand
- Added Serial modules - FTD-SIO, pl2303, Garmin - for serial devices,
- Added Phone modems - For connecting Data card dongles
- Added UVC Camera support - Many users have been asking for this
- Added oprofiler support
- Added iptables
- Added NAT/Firewall (inc GRE) support - Now you can make your PicUntu a complete firewall
- Added USB 1.0 support - remember how your old USB hubs were not being used for bootup, well they _should_ work now.
- DW_OTG_ debug report suppressed
- Added USB SCSI adapter - I have lots of SCSI harddisk lying around, and USB adapter for them, but they seemed not to work earlier.
- Added Pegasus network support
- FB set - all options enabled - you should have better control for fb setting.
- Added auxilary Video - If you have a USB LED,LCD screen lying around - try connecting to PIcUntu, should work. (in addition to HDMI) - required for in-car applications.
- Modules for USB Printer - now you should be able to connect USB printers to PicUntu
- Serial Modem drivers - Qualcom, NAVMAN,
- Added Joliet/UDF support - You should be able to connect USB CDROM
- NFS Client, NFS Server support for V3 , V4 added
- Ofcourse CIFS continues
- Integrated BCM40181, rtl8188eu support - so that you no longer need to separately look for modules for basic wifi
- Three graphic driver kernels - 1080p, 720, VGA are released.
- No it does not yet have support for MX1 wifi, RK802III/s - wifi - I am still hunting for the source. - A Kirby from Rikomagic was successful in getting us the source code for MT. (wifi + BT) trying to get that integrated now.

=======

