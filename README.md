Install Picuntu on Intenal Nand 
===============================  

<b>(I have now installed debian wheezy via debootstrap. The below still applies and I needed Picuntu to do the debootstrap. The instructions below are intentionally vague !.
In a nutshell what I have done is add the rk30xxnand_ko.ko to initramfs.cpio and insert it so that the kernel can find root on the mtdblock. I will write all this up properly on my blog when I get time. 
)</b>

First of all this is in a state of flux and may be broken at any time. <br>
I would have liked to fork repos and pull from here and there but do not yet know much about git so I'm uploading this as a whole. <br>
Credits to AndrewDB , Omegamoon, alok & finless bob <br>

The kernel tree is from <br>
https://github.com/aloksinha2001/picuntu-3.0.8-aloksinha2001 <br>
The toolchain is taken from Omegamoons tree <br>
https://github.com/omegamoon/rockchip-rk30xx-mk808 <br>
The original initramfs.cpio is from <br>
https://github.com/Galland/rk30_linux_initramfs.github <br>
the mkbootimg is from <br>
https://github.com/Galland/rk30_linux_initramfs.git <br>

The config is based on Picuntu 0.9 RC2 as RC3 was throwing up hung process errors <br>

I started this as my intention is to run a mail relay on one MK808 on my home FTTC connection.  <br>
I don't need Android on the device at all and it seems a waste to leave the internal nand almost unused. <br>

First I wanted to get a functional Picuntu on the internal Nand, this is done. <br>
Next I will be optimizing the kernel for headless running. Anything not needed will be disposed of. <br>
I'm still waiting for a Network switch and once I have it wifi will be going from my build as well( but I'll leave configs and code in place) <br>

Only tested on MK808. I do have a MK808B as well so may try this later (no wifi or BT) but other models YOU ARE ON YOUR OWN <br>

This will stop Android working on your device (though you can flash it back) <br>
If you don't understand why I suggest you stop here. <br>

 <br>
Doing it !   (Don't blame me)
======================= 

Install Picuntu 0.9 RC2 and make sure its working fully on the sdcard. <br>

The kernel build tree is self contained and I think you should be able to build without to many extra packages <br>
Of the top of my head I had to install SHA something and LZO something (I'm on arch) <br>
Look at the compilation errors if any and you'll work out what's missing <br>

 <br>

You need to have your MTD blocks / partitions laid out as I have set up in the configs Kernel CONFIG_CMDLINE <br>

The parameter file is in the root directory "parameter1gb" <br>

Flash Finless bobs 2.0 MK808 rom with the  parameter1gb and you get the right layout <br>
http://www.freaktab.com/showthread.php?4248-NEW-MK808-JB-4-2-2-Finless-ROM-2-0-BETA <br>

Copy ./modules/rk30xxnand_ko.ko.3.0.8+ to the /lib/modules/kernel/rk30xxnand_ko.ko on your sdcard and on the MK808 run depmod -a <br>

run ./build_rk3066_standard <br>

and in BUILT you will find sdcard_boot.img. <br>

Flash it to boot  <br>

On picuntu <br>
--------- <br>

modprobe rk30xxnand_ko.ko <br>

cat /proc/mtd  <br>

should show the mtd partitions. If not its not working , figure it out. <br>

mkfs.ext4 /dev/mtdblock9 <br>

mount /dev/mtdblock9 /mnt <br>

rsync -avx / /mnt <br>

umount /mnt <br>

On PC <br>
---- <br>

run ./build_rk3066_mtd <br>
and in BUILT you will find mtdboot.img. <br>
Flash to boot <br>

Everything works and you are happy. <br>

As I say this is stiil a work in progress <br>
The modules these compilations produced did not work (missing symbols) but the set already on my picuntu install did. <br>
I will investigate. <b>Fixed</> Needed CFLAGS_MODULE=-fno-pic to compile the modules <br> 

The rk30xxnand_ko.ko I took from the MK808 rom worked on Picuntu but  were odd on my PC. (cp cannot stat, seemed to disappear !) <br>
So I took one from here <br>
https://github.com/jin-eld/cm_rk3066_ventos97/blob/master/ramdisk/rk30xxnand_ko.ko.3.0.8%2B <br>

It might brick your entire life ? <br>


To Do 
===== 
Test jffs2 & ubifs & yaffs2 (yaffs2 doesn't compile atm)<br>
Benchmark internel nand vs class 10 SD (simple test of reading and writing a 125M file to and from ramdisk show preformance almost the same , SD very slightly won.<br>
Try to implement raid1 between nand & SD <br>
Change partion layout to waste less space. <br>
Make headless version of kernel <br>
Find any improved code in other repo's <br>
Try to debbootstrap debian <br>

 
 <br>

 <br>
picuntu-3.0.8-alok 
================== 

rk3066-kernel - derived from mix of andrew source, omegamoon, 0.3a kernel, driver sources <br>

Integrated kernel for Picuntu 3.0.8-alok+ 
=============

Linux/Android kernel for the Rockchip RK3066 SoC <br>

Changelog - Kernel - 3.0.8-alok+ <br>
- Cleaned the previous kernel <br>
- Removed unwanted TV Tuner cards <br>
- Bluetooth support retained <br>
- All cpufreq governors now available - Try userspace, and hav fun.. (Read warning below) <br>
- Default governor Ondemand <br>
- Added Serial modules - FTD-SIO, pl2303, Garmin - for serial devices, <br>
- Added Phone modems - For connecting Data card dongles <br>
- Added UVC Camera support - Many users have been asking for this <br>
- Added oprofiler support <br>
- Added iptables <br>
- Added NAT/Firewall (inc GRE) support - Now you can make your PicUntu a complete firewall <br>
- Added USB 1.0 support - remember how your old USB hubs were not being used for bootup, well they _should_ work now. <br>
- DW_OTG_ debug report suppressed <br>
- Added USB SCSI adapter - I have lots of SCSI harddisk lying around, and USB adapter for them, but they seemed not to work earlier. <br>
- Added Pegasus network support <br>
- FB set - all options enabled - you should have better control for fb setting. <br>
- Added auxilary Video - If you have a USB LED,LCD screen lying around - try connecting to PIcUntu, should work. (in addition to HDMI) - required for in-car applications. <br>
- Modules for USB Printer - now you should be able to connect USB printers to PicUntu <br>
- Serial Modem drivers - Qualcom, NAVMAN, <br>
- Added Joliet/UDF support - You should be able to connect USB CDROM <br>
- NFS Client, NFS Server support for V3 , V4 added <br>
- Ofcourse CIFS continues <br>
- Integrated BCM40181, rtl8188eu support - so that you no longer need to separately look for modules for basic wifi <br>
- Three graphic driver kernels - 1080p, 720, VGA are released. <br>
- No it does not yet have support for MX1 wifi, RK802III/s - wifi - I am still hunting for the source. - A Kirby from Rikomagic was successful in getting us the source code for MT. (wifi + BT) trying to get that integrated now. <br>

======= 
