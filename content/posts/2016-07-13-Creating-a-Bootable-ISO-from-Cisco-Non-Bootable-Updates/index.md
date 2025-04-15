+++
title = "Creating a Bootable ISO from Cisco Non-Bootable Updates"
date = 2016-07-13T09:33:00-04:00
author = "bryan"
draft = false
tags = ["cisco"]
+++
There’s plenty of great posts out there on how to make bootable CUCM/Collaboration media with paid GUI software such as [UltraISO](http://htluo.blogspot.com/2010/04/how-to-make-non-bootable-iso-image.html) or free command line utilities like [cdrtools](http://www.ipcommute.co.uk/technical-articles/17--creating-isolinux-boot-dvds-with-free-software-cucm-uccx-cups.html). Inspired by those bloggers, I wanted to share a method to create bootable media that features the best of both worlds: open source utilities with easy to use interfaces, all for the low low price of free.

Software you’ll need:

[7-Zip](http://www.7-zip.org/) / [cdrtfe](http://cdrtfe.sourceforge.net/)

First, find an existing bootable CUCM ISO (a Red Hat or CentOS live cd will do the trick as well) and open it in 7zip.

![](0b7a4a9c560f352c3f47363e1f026df9_MD5.png)

Find the **isolinux** directory, highlight it, and click **Extract**. Choose where to save the files.

![](cee5c3eea68deafc6aa44a2a1bf22f51_MD5.png)

Next, take your non-bootable update dvd from Cisco and extract the contents with 7zip by right clicking the ISO -> 7-Zip -> Extract to….

![](97b9f9ed51d70c99b01db157b06023a1_MD5.png)

Copy the **isolinux** directory you extracted earlier to the newly extracted Cisco ISO folder, overwriting any duplicate files.

Now open **cdrtfe**. Under the **Data Disc** tab click **Options**.

![](1e4fc8cd17e58d7af0a1d0deddb51b30_MD5.png)

Under **ISO image**, select **Use image**, and browse to a location to store the finished ISO. Check **Create image only, do not burn**. Click **Ok**.

![](a4e8b40c2b5bf66d5885f56151bd3f1a_MD5.png)

Next, click the **File System** button. Check the following options: **Create boot disc**, **No disc emulation**, and **Create boot info table**. Under the **Boot image** field, browse to the copied **isolinux** directory of your extracted ISO folder and select **isolinux.bin**. Click **Ok**.

![](d25bc6cb924714331ebcaed91bd9cc64_MD5.png)

In file explorer, browse to your extracted ISO folder and copy over all the files and folders into the cdrfte window.

![](8eeabe74e3b8d659a1efdc6d9043e0e4_MD5.png)

Click **Start** to create your ISO. Once completed you should have a fully bootable ISO!

![](9ceabf758bb5523a58905ecb681f87a6_MD5.png)
