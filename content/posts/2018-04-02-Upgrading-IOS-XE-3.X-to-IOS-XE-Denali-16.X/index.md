+++
title = "Upgrading IOS-XE 3.X to IOS-XE Denali 16.X"
date = 2018-04-02T10:02:29-04:00
author = "bryan"
draft = false
tags = ["cisco","IOS-XE"]
+++
IOS-XE Denali represents Cisco’s effort to bring a single code base across their enterprise line of networking equipment, meaning the same operating system will run Catalyst, ISR, and ASR platforms.

IOS-XE 3.7 will be the “end of the track” for the 3.X train of code on Catalyst 3650/3850… so to speak.

Below are the steps I used to upgrade a 3850 from IOS-XE 03.06.03E to IOS-XE 16.3.5b Denali. The switches were in “install mode” and these steps are specific for that method. To check your mode you can issue a **show version** command and look under the **Mode** column. 3850s should come from the factory in install mode; bundle mode is similar to the older upgrade method where you boot from a monolithic bin file. More info on install mode and bundle mode can be found [here](https://www.cisco.com/c/en/us/td/docs/switches/lan/catalyst3850/software/release/3se/system_management/configuration_guide/b_sm_3se_3850_cg/b_sm_3se_3850_cg_chapter_010100.html).

`Switch Ports Model              SW Version        SW Image              Mode`   
`------ ----- -----              ----------        ----------            ----`   
   `1 56    WS-C3850-48P       03.06.03E         cat3k_caa-universalk9 INSTALL`

If you’re looking to free up some space to copy over your new version you can run a **software clean** command to clean up any unused packages.

`[1]: % flash: requires 526972 KB of free space, but only 124364 KB is available. Operation aborted.`
`[1]: % An internal error was encountered. Operation aborted.`

`3850#software clean`
`Preparing clean operation ...`
`[1]: Cleaning up unnecessary package files`
`[1]: No path specified, will use booted path flash:packages.conf`
`[1]: Cleaning flash:`
`[1]: Preparing packages list to delete ...`
     `In use files, will not delete:`
       `cat3k_caa-base.SPA.03.06.03E.pkg`
       `cat3k_caa-drivers.SPA.03.06.03E.pkg`
       `cat3k_caa-infra.SPA.03.06.03E.pkg`
       `cat3k_caa-iosd-universalk9.SPA.152-2.E3.pkg`
       `cat3k_caa-platform.SPA.03.06.03E.pkg`
       `cat3k_caa-wcm.SPA.10.2.131.0.pkg`
       `packages.conf`
`[1]: Files that will be deleted:`
    `cat3k_caa-base.SPA.03.03.04SE.pkg`
    `cat3k_caa-base.SPA.03.03.05SE.pkg`
    `cat3k_caa-drivers.SPA.03.03.04SE.pkg`
    `cat3k_caa-drivers.SPA.03.03.05SE.pkg`
    `cat3k_caa-infra.SPA.03.03.04SE.pkg`
    `cat3k_caa-infra.SPA.03.03.05SE.pkg`
    `cat3k_caa-iosd-universalk9.SPA.150-1.EZ4.pkg`
    `cat3k_caa-iosd-universalk9.SPA.150-1.EZ5.pkg`
    `cat3k_caa-platform.SPA.03.03.04SE.pkg`
    `cat3k_caa-platform.SPA.03.03.05SE.pkg`
    `cat3k_caa-universalk9.SPA.03.03.05.SE.150-1.EZ5.bin`
    `cat3k_caa-universalk9.SPA.03.06.03.E.152-2.E3.bin`
    `cat3k_caa-wcm.SPA.10.1.140.0.pkg`
    `cat3k_caa-wcm.SPA.10.1.150.0.pkg`
    `packages.conf.00-`
    `packages.conf.01-`
    `packages.conf.02-`

`[1]: Do you want to proceed with the deletion? [yes/no]: yes`
`[1]: Clean up completed`

Copy over your new ios version via any support method (usb, tftp, scp, etc.). We’ll use the **software install** set of commands with the **force** and **new** flags since we’re going from 3.X up to 16.X. Once completed type **yes** to initiate your reload. There is some micro code upgrades that may take some time so you’re looking at about 10+ minutes of down time.

`3850#software install file flash:cat3k_caa-universalk9.16.03.06.SPA.bin force new`
`Preparing install operation ...`
`[1]: Starting install operation` 
`[1]: Expanding bundle usbflash0:cat3k_caa-universalk9.16.03.05b.SPA.bin`
`[1]: Copying package files`
`[1]: Package files copied`
`[1]: Finished expanding bundle usbflash0:cat3k_caa-universalk9.16.03.05b.SPA.bin`
`[1]: Verifying and copying expanded package files to flash:`
`[1]: Verified and copied expanded package files to flash:`
`[1]: Starting compatibility checks`
`[1]: Bypassing peer package compatibility checks due to force command option`
`[1]: Finished compatibility checks`
`[1]: Starting application pre-installation processing`
`[1]: Finished application pre-installation processing`
`[1]: Old files list:`
    `Removed cat3k_caa-base.SPA.03.06.03E.pkg`
    `Removed cat3k_caa-drivers.SPA.03.06.03E.pkg`
    `Removed cat3k_caa-infra.SPA.03.06.03E.pkg`
    `Removed cat3k_caa-iosd-universalk9.SPA.152-2.E3.pkg`
    `Removed cat3k_caa-platform.SPA.03.06.03E.pkg`
    `Removed cat3k_caa-wcm.SPA.10.2.131.0.pkg`
`[1]: New files list:`
    `Added cat3k_caa-guestshell.16.03.05b.SPA.pkg`
    `Added cat3k_caa-rpbase.16.03.05b.SPA.pkg`
    `Added cat3k_caa-rpcore.16.03.05b.SPA.pkg`
    `Added cat3k_caa-srdriver.16.03.05b.SPA.pkg`
    `Added cat3k_caa-wcm.16.03.05b.SPA.pkg`
    `Added cat3k_caa-webui.16.03.05b.SPA.pkg`
`[1]: Creating pending provisioning file`
`[1]: Finished installing software.  New software will load on reboot.`
`[1]: Committing provisioning file`

`[1]: Do you want to proceed with reload? [yes/no]: yes`
`[1]: Reloading`

`<Thu Mar 29 18:09:24 2018> Message from sysmgr: Reason Code:[3] Reset Reason:Reset/Reload requested by [stack-manager]. [User requested reload]`
`umount: /proc/fs/nfsd: not mounted`
`Unmounting ng3k filesystems...`
`Unmounted /dev/sda3...`
`Warning! - some ng3k filesystems may not have unmounted cleanly...`
`Please stand by while rebooting the system...`
`Restarting system.`



`Booting...Initializing and Testing RAM ++++@@@@####...################################++@@++@@++@@++@@++@@++@@++@@++@@++@@++@@++@@++@@++@@++@@++@@++@@done.`
`Memory Test Pass!`

`Base ethernet MAC Address: bc:67:1c:7e:e4:00`

`Interface GE 0 link down***ERROR: PHY link is down`
`Initializing Flash...`

`flashfs[7]: 0 files, 1 directories`
`flashfs[7]: 0 orphaned files, 0 orphaned directories`
`flashfs[7]: Total bytes: 6784000`
`flashfs[7]: Bytes used: 1024`
`flashfs[7]: Bytes available: 6782976`
`flashfs[7]: flashfs fsck took 1 seconds....done Initializing Flash.`

`Interrupt within 5 seconds to abort boot process.`


`Interrupt within 5 seconds to abort boot process.`
`Getting rest of image`
`Reading full image into memory....done`
`Reading full base package into memory...: done = 22301472`
`Nova Bundle Image`
--------------------------------------
`Kernel Address    : 0x6042e384`
`Kernel Size       : 0x34e9e1/3467745`
`Initramfs Address : 0x6077cd65`
`Initramfs Size    : 0x119d5bb/18470331`
`Compression Format: mzip`

`Bootable image at @ ram:0x6042e384`
`Bootable image segment 0 address range [0x81100000, 0x81b8adc0] is in range [0x80180000, 0x90000000].`
`@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@boot_system: 380`
`Loading Linux kernel with entry point 0x816902d0 ...`
`Bootloader: Done loading app on core_mask: 0xf`

 `Launching Linux Kernel (flags = 0x5)`






`%IOSXEBOOT-5c8e9d6656e9d89a8dedeae457871084-new_cksum: (rp/0): 4`
`%IOSXEBOOT-5c8e9d6656e9d89a8dedeae457871084-saved_cksum: (rp/0): 4`
`%IOSXEBOOT-Thu-###: (rp/0): Mar 29 18:14:25 Universal 2018 PLEASE DO NOT POWER CYCLE ### BOOT LOADER UPGRADING 4`
`%IOSXEBOOT-loader-boot: (rp/0): upgrade successful 4`


`Front-end Microcode IMG MGR: found 4 microcode images for 1 device.`
`Image for front-end 0: /tmp/microcode_update/front_end/fe_type_6_0`
`Image for front-end 0: /tmp/microcode_update/front_end/fe_type_6_1`
`Image for front-end 0: /tmp/microcode_update/front_end/fe_type_6_2`
`Image for front-end 0: /tmp/microcode_update/front_end/fe_type_6_3`

`Front-end Microcode IMG MGR: Preparing to program device microcode...`
`Front-end Microcode IMG MGR: Preparing to program device[0]...594412 bytes.... Skipped[0].`
`Front-end Microcode IMG MGR: Preparing to program device[0]...393342 bytes.`
`Front-end Microcode IMG MGR: Programming device 0...rwRrrrrrrw..0%.........................................................................10%........................................................................20%..........................................................................30%........................................................................40%..........................................................................50%........................................................................60%.........................................................................70%..........................................................................80%........................................................................90%..........................................................................100%`
`Front-end Microcode IMG MGR: Preparing to program device[0]...25186 bytes.`
`Front-end Microcode IMG MGR: Programming device 0...rrrrrrw..0%....10%....20%......30%...40%......50%....60%......70%...80%......90%....100%wRr!`
`Front-end Microcode IMG MGR: Microcode programming complete for device 0.`
`Front-end Microcode IMG MGR: Preparing to program device[0]...86370 bytes.... Skipped[3].`
`Front-end Microcode IMG MGR: Microcode programming complete in 246 seconds`

`Both links down, not waiting for other switches`
`Switch number is 1`


              Restricted Rights Legend

`Use, duplication, or disclosure by the Government is`
`subject to restrictions as set forth in subparagraph`
`(c) of the Commercial Computer Software - Restricted`
`Rights clause at FAR sec. 52.227-19 and subparagraph`
`(c) (1) (ii) of the Rights in Technical Data and Computer`
`Software clause at DFARS sec. 252.227-7013.`

           cisco Systems, Inc.
           170 West Tasman Drive
           San Jose, California 95134-1706



`Cisco IOS Software [Denali], Catalyst L3 Switch Software (CAT3K_CAA-UNIVERSALK9-M), Version 16.3.5b, RELEASE SOFTWARE (fc1)`
`Technical Support: http://www.cisco.com/techsupport`
`Copyright (c) 1986-2017 by Cisco Systems, Inc.`
`Compiled Thu 02-Nov-17 11:07 by mcpre`

