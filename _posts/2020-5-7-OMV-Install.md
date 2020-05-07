---
layout: post
title: OpenMediaVault Installation (Including unsupported and not recommended)
---

> Procedures to do an OpenMediaVault installation including procedures both unsupported (on a RAID protected filesystem and sharing OS space with data) and not recommended (using and USB flash drive).

![](https://github.com/barondandi/install-omv/blob/master/images/omv_logo.png)

OpenMediaVault is available at  [https://www.openmediavault.org/](https://www.openmediavault.org/) and installation procedure at [https://openmediavault.readthedocs.io/en/5.x/installation/index.html](https://openmediavault.readthedocs.io/en/5.x/installation/index.html).

I will be using version 5.x and only specifying here the steps which are not reflected in the documentation.

1.  [Hardware used](#1.-Hardware-used)
2.  [Some initial recommendations and issues](#2.-some-initial-recommendations-and-issues)
    -   [USB is not recommended, so why?](#USB-is-not-recommended,-so-why?)
    -   [Generic recommendations](#Generic-recommendations)
    -   [USB specific recommendations](#USB-specific-recommendations)
3.  [USB Flash Drive installation](#3.-USB-Flash-Drive-installation)
    -   [Post installation tasks](#Post-installation-tasks)
    -   [Backing up configuration](#Backing-up-configuration)
4.  [RAID installation using an USB flash drive](#4.-RAID-installation-using-an-USB-flash-drive)
5.  [RAID installation using only hard drives](#5.-RAID-installation-using-only-hard-drives)
6.  [References and Credits](#6.-references-and-credits)
7.  [Summary](#7.-summary)


I already went through several NAS builds over the years. From a tailored made FreeBSD kernel to spin FreeNAS, openfiler, Ubuntu Server or a QNAP system.

In the end, I turned to OpenMediaVault, as it saves me a lot of pain for the everyday administration (and I am becoming lazy), and still gives you the full power of a Debian under the hood, that allows you any sort of tailoring, or the option to tinker if anything goes really wrong. This last past is a must for me, as I already had the experience of _**losing all the data**_ in two of my previous NASes (ZFS corruption due to lack of memory and an underpowered Annpurna Labs CPU based QNAP).

You might want to go through this tutorial for some of the following reasons:
-   You have no extra/free SATA ports in your NAS and want to make sure you do a proper installation on a USB drive, wihtout it dying after few weeks or months.
-   You install on a drive with plenty of space. As OMV does not support using OS drive for data storage (as noted in OMV installation guide) you waste a lot of free space on that device.
-   You want to RAID protect your OS to make sure you keep operation even if one of your boot drives fail.


## 1. Hardware used
In case it's of any interest, this is the hardware I will be using for the installation:

-   **Motherboard:** ASRock H370M-ITX/ac Mini ITX LGA1151 Motherboard
    > Mini ITX format and with 6 SATA 6Gb connectors on the board.

-   **CPU:** Intel Pentium Gold G5400 3.7 GHz Dual-Core Processor
    > There is an update of this model in 2019 to Intel Pentium Gold G5420

-   **Memory:** Corsair Vengeance LPX 8 GB (1 x 8 GB) DDR4-2400 Memory
    > One single DIMM as I might upgrade with a second one if I start using the system to spin containers or implement ZFS.
    > I would have preferred Crucial Ballistix but it was not available when ordering at a reasonable price.
    > Make sure to verify that it's supported in the motherboard documentation. Also, check latencies.

-   **Fan:** Noctua NH-L9i
    > Very quiet and low profile.
    > I make it low profile, as when I upgrade the NAS hardware, I can easily reuse and turn the old board into a very small mediacenter.

-   **Case:** Fractal Design Array R2 Mini ITX NAS Case with 300 Watt SFX Power Supply Unit
    > No longer available. Now replaced for the Fractal Design Node 304 Mini ITX Tower Case.

-   **USB Drive:** Samsung Fit Plus 32Gb USB 3.1
    > Avg. Sustained Write Speed 21.8MB/s. Avg. Sequential Write Speed 26.7MB/s ([https://usb.userbenchmark.com/SpeedTest/38059/Samsung-Flash-Drive-FIT](https://usb.userbenchmark.com/SpeedTest/38059/Samsung-Flash-Drive-FIT))

    > NOTE: _If possible, during installation, use a drive with LED activity, to make sure that after finishing installation and specific USB customization, activity is minimal over the drive. Thus, we increase the lifespan of the drive. During installation I used a bigger Sandisk Extreme USB 3.0. Once finished, I reduced the OS partition and cloned it to the final drive (procedure described later)._  

-   **Hard drives #1:** 2 x	SEAGATE 1.5 TB 3.5" 5400RPM
-   **Hard drives #2:** 4 x	Western Digital Red 8 TB 3.5" 5400RPM

## 2. Some initial recommendations and issues

### USB is not recommended, so why?

### Generic recommendations

### USB specific recommendations


-   In motherboards with several Monitor ports, usually only one is enabled initially. Find it and connect the monitor to follow with installation. Later on this can be changed in the BIOS.
    > In my case it was one of the 2 HDMI provided.
-   Get inside the BIOS menu and make sure that:
    -   All the fans are working properly. Proper ventilation of the drives is a must and the main reasons for drives failures are temperature and vibration.
        > In my case, the chassis fan was not working. I had to change the control mode from "Auto" to "PWM Mode".
    -   Disable the hardware that will not be used.
        > I disabled the audio and the WiFi
    -   Make sure S.M.A.R.T. is enabled for the hard drives.


- During installation I was given the choice of using one of the two 1Gb Intel ethernet port, I219V or I211AT. As initially I was going to use one only, I selected the I219-V the other one goes over PCIE (that's what I read at least).
- After automaticaly assigning IP using DNS, I pressed "ESC" and I was given the option to manually configure the network. This is ideal, as I need to set a fixed IP, and I can do that from the begining.
  > I also used this step to specify 3 name servers separating the IPs by spaces: Open DNS (208.67.220.220), Google (8.8.8.8) and a third one for my local Internet provider.

- You get a warning about UEFI mode installation. Force it with no problem.

  > NOTE: If you run into any trouble during installation, you can press "ESC" key and go back to a previous step.  

- If you get a "No root file system" error when partitioning the disks, there are several things you can do:
  - Enter the BIOS menu, and check the USB configuration. In my case I had to change the "Legacy USB Support" to "UEFI Setup Only" for the installation to work.
  - Format the USB Flash drive in EXT4 and make sure you con mount it and write into it. I had to first format it to FAT32 in a Windows system, and then move to a Linux system and overwrite the partition with EXT4 (no idea of the reason, but for me it was the only way to make it work).
- You might get installation giving an error after some percentage during the "Installing the system..." phase. In my case it was due to an USB drive without enough sustained write speed. I had to update to a faster one. You can check USB drives speed tests here: https://usb.userbenchmark.com/. Also, make sure you are connecting it to an USB 3.x connector on the motherboard.

- After the first reboot IPs are not properly configured. Please reboot again, this second time, the IP shoild have been set to the fixed IP specified during installation.

From here, you can login with root/<root_password> and type the command
```shell
omv-firstaid
```
You get into a menu where you can configure the network interface or change the GUI "admin" user password among other things.

Now you can shutdown the array to follow with the procedure:
```shell
shutdown -h now
```
Now we plug the USB drive onto a linux system and use GParted over it to reduce the OS partition size. I will be leaving it at 8Gb, as it is more than enough and leave space to expand afterwards. This way the OS partition to backup will be smaller and take less time and space.

![GParted: Original Layout](https://github.com/barondandi/install-omv/blob/master/images/gparted_1.png)

-   We right click and Unmount the /dev/sdx2 partition.
-   We can now resize it to the desired amount (I used 8192GiB)

![GParted: Resize](https://github.com/barondandi/install-omv/blob/master/images/gparted_2.png)

-   And apply the changes before leaving GParted

![](https://github.com/barondandi/install-omv/blob/master/images/gparted_4.png)
![GParted: Apply changes](https://github.com/barondandi/install-omv/blob/master/images/gparted_5.png)

-   We eject the USB drive and plug it back on the NAS. We boot again and see what happens. If there are no issues, we log on to the GUI and check that the filesystems are fine:

![GParted: Final Layout](https://github.com/barondandi/install-omv/blob/master/images/gparted_6.png)

-   First we go to System \ Update Management and we apply the available updates.
-   Then we go to System \ Date and Time and enable NTP service after specifiying the timezone.
> NOTE: Don't forget to SAVE the changes and then APPLY de configuration in the menu that appears. Othenwise changes will be lost.

- To make it easier to power down the system, go to System \ Power button, and change it to "Shutdown" so that we can power down the NAS without any monitor and keyboard.

![Power button](https://github.com/barondandi/install-omv/blob/master/images/power-button.png)


## 3. USB Flash Drive installation

### Post installation tasks

### Backing up configuration

## 4. RAID installation using an USB flash drive


## 5. RAID installation using only hard drives

> Be careful to not use “sfdisk” command to recover partition table from the healthy disk, sfdisk does not support GPT.
Use “sgdisk” instead.
If new disk is /dev/sdb and healthy disk is /dev/sda, then do
> ```shell sgdisk -R=/dev/sdb /dev/sda ```
> to replicate partition table from sda to sdb.
Finally, use OMV GUI to recover RAID mirror.

## 6. References and Credits

This document is based on the following resources. I really thank the authors for sharing their knowledge and trouble:

- Installing OpenMediaVault on RAID-1 array - https://lazic.info/josip/post/installing-openmediavault-on-raid-device/


## 7. Summary

- __**Objetive:**__ Cover OpenMediaVault unsupported and not recommended installation procedures, and hopefully save you some time!
