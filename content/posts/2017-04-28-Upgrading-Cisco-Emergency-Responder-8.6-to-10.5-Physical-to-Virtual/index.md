+++
title = "Upgrading Cisco Emergency Responder 8.6 to 10.5 - Physical to Virtual"
date = 2017-04-28T09:46:32-04:00
author = "bryan"
draft = false
tags = ["cisco"]
+++
I was recently tasked with upgrading Cisco Emergency Responder 8.6 to 10.5. The 8.6 install just so happened to be on a physical IBM MCS server so I thought I’d document the steps of upgrading a physical install of CER 8.6 to a virtual CER 10.5 install, along with the software used to complete the upgrade.

- Log in to **Disaster Recovery System** by selecting it from the drop down box located on the top right of the CER login page and clicking **Go**.

![](22dfaa3ace743a0f8ef0703cf6f02faf_MD5.png)

- Select the **Backup Menu** and then navigate to **Backup Device**.

![](43862e08b0d9e618fea1839da98dfc12_MD5.png)

DRS uses SFTP to securely transport the backup records. If you have a SFTP server already setup in your environment you can use that to create a manual up-to-date back up. For this upgrade, a SFTP server was unavailable so I had to use my laptop as the back up device. The software I used to accomplish this task was [freeSSHd](http://www.freesshd.com/?ctt=overview). These next steps are an overview on how to configure freeSSHd as a backup device for DRS.

- Open freeSSHd and click the **Users** tab and then click **Add** to configure a backup user.

![](28a5a90aebed457f576eac36c0e69022_MD5.png)
- Click the SFTP tab and set the SFTP home path, the directory where the backup files will be stored.

![](b19f02853c2a50b1c2acfd4f295938cc_MD5.png)
Now that freeSSHd is configured we can go back to DRS and configure the Backup Device.

- Give the Backup device a name, IP, path, and the username and password you configured in freeSSHd.

![](6226675a66c3fa3980c495e3bd7ff1e7_MD5.png)
- Once the backup device is configured click the **Backup** menu and select **Manual Backup**.

![](249a85ee3926c319b796d1b6eec9f1bd_MD5.png)

- Select the device you configured and click CER to back up all registered CER components.

![](9d5ca7475ce1abd4fd7944a8f546b37a_MD5.png)

- Click **Start Backup**, if everything is configured successfully you should see the progress bar advance and a number of tar archives will now be in your SFTP directory.

![](6dff126a5a24cc4735172949de7b27e4_MD5.png)

With a backup of the existing physical 8.6 install in place, we can now migrate to a virtual install of CER 8.6. The vmware ova and install media for CER 8.6 are unfortunately not on the Cisco downloads page. However, if you open a TAC case for assistance with a P2V migration, Cisco will make the download available to you.

After you import the 8.6 ova you may also want to adjust the virtual machine properties so that CER 10.5 will be supported without issues. Increasing the memory to 4 GB, increasing the CPU resources, etc.

![](c9a8f4d3a3eb6ce498316eefcd457d68_MD5.png)


![](a651c6e1c6b430251d318fcdabcb7004_MD5.png)
Follow the on screen instructions to complete the basic CER install (if you want re-IP CER, now would be the time to do that, or if you want to keep the same IP address please be sure that the virtual CER is on an isolated network). Once completed head to the web interface and select **Disaster Recovery System**.

- Create your backup device on the by going to **Backup** > **Backup Device**.

![](43862e08b0d9e618fea1839da98dfc12_MD5.png)

![](6226675a66c3fa3980c495e3bd7ff1e7_MD5.png)

- Go to the **Restore** menu and select **Restore Wizard**.

![](900ae312eefbffaf3f8a2c2bdfee72c3_MD5.png)

- Select your **Backup Device** and click next.

![](7e9573b7f470eb8a4047437cf937cd71_MD5.png)

- Select your **Backup Archive** and click next.

![](d8bd1f24f8635bbe0399edd49a657c75_MD5.png)

- Select the features you’d like to restore and click next.

![](dd6664155ca1c2cbd29a57f95d83f351_MD5.png)

- Select **File integrity check** and the servers to be restored then click **Restore**.

- ![](b5ef3e1b726b8d5d539e8c45d6f70a21_MD5.png)
- Wait for the restore process to finish. Once completed you will need to reboot the virtual machine by SSH’ing into the server and issuing the **utils system restart** command.

![](18888b09b34ccfd15aed6d3a2578e61f_MD5.png)

- Next we can begin the upgrade process. Select **Cisco Unified OS Administration** from the navigation drop down menu and click Go. Select the **Software Upgrades** menu and click **Install/Upgrade**.

![](4d8136b2bf2d62599106faea8b4499f8_MD5.png)

- First we need to install ciscocm.cer_refresh_upgrade_v1.1.cop.sgn, a Cisco Options Package that is necessary to upgrade from 8.6 to 10.5. The file can be downloaded from Cisco and will need to be placed in your SFTP directory.
- Select **Remote Filesystem** as the software source.
- Configure the **Software Location** to point to your PC running freeSSHd then click next.

![](2c8d6e2c200a50a0df92e66c0613a7b4_MD5.png)

- Under **Software Location** select ciscocm.cer_refresh_upgrade_v1.1.cop.sgn and click next.

![](30ec5e538e3723e8699d4eeb83186e71_MD5.png)

- You should then see the install progress.

![](a71041d362f885c88bf3726170ac6703_MD5.png)

![](216e85a2e9c8db8bf7d524621ca8246a_MD5.png)

![](91ec38e31f48f7466c8251504d93b19a_MD5.png)

![](657c3fa5105d3cbe78718285282dc974_MD5.png)

![](e11a1988538e2804f1adbc19e49fa059_MD5.png)

- Reboot the system.

![](83b522b58e529d046f57d28695d413e6_MD5.png)

- Next, mount the 10.5 install media on the virtual machine. Select **Connected** and **Connected at power on**.

![](22f71cb5785b257f36ef0a3243d062db_MD5.png)

- Select **Cisco Unified OS Administration** from the navigation drop down menu and click **Go**. Select the **Software Upgrades** menu and click **Install/Upgrade**.
- Select **DVD/CD** as the software source and click next.

![](04e92e8c10459bfc90ef7b2a81004c47_MD5.png)

- Be sure the correct software version is displayed and click next.

![](7da72a4340adfb76342502afffe7ff9c_MD5.png)

- Select your **Switch** options and click next.

![](7ca9d16eb23860edab46eda3ac0a460e_MD5.png)

![](089ebd067990fab201db5040e1bf1d5a_MD5.png)

- Follow the upgrade process on screen, when the system reboots you can view the install progress from the vmware console.

![](6507e301f6217a537656be21a6393d53_MD5.png)
![](c3a1261e08238549ff8efc602c92f1ba_MD5.png)
![](607009005ca01e15d6e189d1a2a95def_MD5.png)
![](aff9812ac893b08a93d85a059d3831e0_MD5.png)

And that’s it. Once completed you should now have a fully upgraded version of CER 10.5, complete with migrated data from the old physical installation.