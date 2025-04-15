+++
title = "Upgrading a Cisco Wireless LAN Controller"
date = 2018-03-23T10:01:51-04:00
author = "bryan"
draft = false
tags = ["cisco"]
+++
After being recently tasked to upgrade a Cisco Wireless LAN Controller I figured I’d document the process for future reference.

You’ll need either a tftp, ftp, or sftp server as well as the code you’re upgrading the WLC to and the FUS upgrade if necessary.

Some notes on the different requirements:

- Code can be downloaded to the WLC by either TFTP, FTP, or SFTP.
- Configuration can be uploaded from the WLC by either TFTP, FTP, or SFTP.
- Please be aware of the types of access points the version of WLC code supports. Older APs are often unsupported in newer versions of code. For example the WLC i’m upgrading needs to support AIR-LAP1142N-A-K9 access points which are not compatible with release 8.4.X and up, meaning the newest code I could upgrade to is 8.3.140.0. Check the [Cisco Wireless Solutions Software Compatibility Matrix](https://www.cisco.com/c/en/us/td/docs/wireless/compatibility/matrix/compatibility-matrix.html) to find out what version of code is supported for your APs.
- It’s also recommended you check the [TAC Recommended AireOS Builds](https://www.cisco.com/c/en/us/support/docs/wireless/wireless-lan-controller-software/200046-tac-recommended-aireos.html) to see if the version of code you selected is recommended by TAC or if there may be any bugs you might run into. When in doubt grab the gold star release from Cisco.
- When downloading your WLC software update check to see if there is a Field Upgrade Software (FUS) package available. The FUS contains various system-related component upgrades (bootloaders, field recovery images, etc.). More info about FUS can be found [here](https://www.cisco.com/c/en/us/td/docs/wireless/controller/release/notes/fus-rn-20.html).

Before upgrading any software be sure to create a back up of your WLC config.

The following commands configure the upload mode (tftp, ftp, or sftp), what to back up, and where to back it up to.

`(wlc1) >transfer upload mode sftp`
`(wlc1) >transfer upload username sftp`
`(wlc1) >transfer upload password sftp`
`(wlc1) >transfer upload datatype config`
`(wlc1) >transfer upload filename WLC-BACKUP`
`(wlc1) >transfer upload path .`
`(wlc1) >transfer upload serverip  X.X.X.X`
`(wlc1) >transfer upload start`

After making a back up of our configuration we may also want to make note of the APs currently joined to the WLC as well as current version of code running.

`(wlc1) >show AP join stats summary all` 

`Number of APs.................................... 166`

`(wlc1) >show sysinfo` 

`Manufacturer's Name.............................. Cisco Systems Inc.`
`Product Name..................................... Cisco Controller`
`Product Version.................................. 8.0.140.0`
`RTOS Version..................................... 8.0.140.0`
`Bootloader Version............................... 8.0.100.0`
`Emergency Image Version.......................... 8.0.100.0`

Now we can configure the transfer method to download the FUS update to the WLC and then reboot the system to apply the update.

`(wlc1) >transfer download mode sftp`
`(wlc1) >transfer download datatype code`
`(wlc1) >transfer download username sftp`
`(wlc1) >transfer download password sftp`
`(wlc1) >transfer download serverip X.X.X.X`
`(wlc1) >transfer download path .`
`(wlc1) >transfer download filename AIR-CT8500-K9-2-0-0-0-FUS.aes`
`(wlc1) >transfer download start`


`Mode............................................. SFTP`
`Data Type........................................ Code`          
`SFTP Server IP................................... X.X.X.X`
`SFTP Server Port................................. 22`
`SFTP Path........................................ /`
`SFTP Filename.................................... AIR-CT8500-K9-2-0-0-0-FUS.aes`
`SFTP Username.................................... sftp`
`SFTP Password.................................... *********`

`This may take some time.`
`Are you sure you want to start? (y/N) y`

`(wlc1) > reset system`

`The system has unsaved changes.`
`Would you like to save them now? (y/N) y`

The system will reboot and apply the FUS update. Keep in mind that this process may take up to 30 – 60 minutes in total.

After applying the FUS update we can follow the same procedure to upload the WLC code.

`(wlc1) >transfer download mode sftp`
`(wlc1) >transfer download datatype code`
`(wlc1) >transfer download username sftp`
`(wlc1) >transfer download password sftp`
`(wlc1) >transfer download serverip X.X.X.X`
`(wlc1) >transfer download path .`
`(wlc1) >transfer download filename AIR-CT8500-K9-8-3-140-0.aes`
`(wlc1) >transfer download start`


`Mode............................................. SFTP`
`Data Type........................................ Code`          
`SFTP Server IP................................... X.X.X.X`
`SFTP Server Port................................. 22`
`SFTP Path........................................ /`
`SFTP Filename.................................... AIR-CT8500-K9-8-3-140-0.aes`
`SFTP Username.................................... sftp`
`SFTP Password.................................... *********`

`This may take some time.`
`Are you sure you want to start? (y/N) y`

Reset the system to apply the update by issuing the reset system command.

You can also have the APs predownload the new software before rebooting your controller. This is useful in large environments where you want to minimize downtime as there is a maximum amount of controllers that can concurrently connect to the WLC to upgrade their software after rebooting the controller.

`(wlc1) > config ap image predownload primary`

`(wlc1) > show ap image all`

After rebooting your controller do a show sysinfo to verify your software version.

`(wlc1) >show sysinfo` 

`Manufacturer's Name.............................. Cisco Systems Inc.`
`Product Name..................................... Cisco Controller`
`Product Version.................................. 8.3.140.0`
`RTOS Version..................................... 8.3.140.0`
`Bootloader Version............................... 8.1.133.7`
`Emergency Image Version.......................... 8.1.133.7`

