Install Picuntu on Intenal Nand 
===============================  

<b>(I have now installed debian wheezy via debootstrap. The below still applies and I needed Picuntu to do the debootstrap. The instructions below are intentionally vague !.
In a nutshell what I have done is add the rk30xxnand_ko.ko to initramfs.cpio and insert it so that the kernel can find root on the mtdblock. I will write all this up properly on my blog when I get time. 
)</b>

First of all this is in a state of flux and may be broken at any time. <br>
I would have liked to fork repos and pull from here and there but do not yet know much about git so I'm uploading this as a whole. <br>
Credits to AndrewDB , Omegamoon, alok & finless bob <br>

The kernel tree is from <br>
https://github.com/aloksinha2001/picuntu-3.0.8-alok <br>
The toolchain is taken from Omegamoons tree <br>
https://github.com/omegamoon/rockchip-rk30xx-mk808 <br>
The original initramfs.cpio is from <br>
https://github.com/Galland/rk30_linux_initramfs <br>
the mkbootimg is from <br>
https://github.com/jin-eld/rk3066-mkbootimg <br>

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

The parameter file is in the root directory "parameter1_custom" <br>

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




To Do 
===== 
Test jffs2 & ubifs & yaffs2 (yaffs2 doesn't compile atm)  <b> No go, the Rockchip Nand module (rk30xxnand_ko.ko.3.0.8+) is only for mtdblock access it seems</b><br>
Benchmark internel nand vs class 10 SD (simple test of reading and writing a 125M file to and from ramdisk show preformance almost the same , SD very slightly won.<br>
Try to implement raid1 between nand & SD <br> <b>not worth it , rsync copy instead</b>
Change partion layout to waste less space. <b>Done,/b><br>
Make headless version of kernel <br>
Find any improved code in other repo's <br>
Try to debbootstrap debian. <b>Done,/b><br> 

 
