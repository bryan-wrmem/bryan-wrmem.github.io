+++
title = "Zero Downtime ASA Upgrade - ASDM"
date = 2016-04-29T09:22:42-04:00
author = "bryan"
draft = false
tags = ["cisco","asa"]
+++
Take a look [here](http://www.cisco.com/c/en/us/td/docs/security/asa/asa95/asdm75/general/asdm-75-general-config/admin-swconfig.html#ID-2152-0000019f) for Cisco’s official documentation, which will be the blueprint for our guide.

- First, back up your configuration by going to **Tools** -> **Backup Configurations**

![asdm-update-01](dc5d536676baebfc84ad5c244dd19b7a_MD5.png)


![asdm-update-02](d75de8165a3ecff43a695446227e3ed9_MD5.png)


![asdm-update-03](68ff4955faa7fd4c5f83d30cf91ce8bd_MD5.png)

- Download your updated ASDM and ASA software from [Cisco.com](https://software.cisco.com/download/navigator.html?i=%21mmd)
- Click **Tools** -> **Upgrade Software from Local Computer**

![asdm-update-04](19335c3a94ec2b56f16e09ab62bcacbf_MD5.png)

- We’ll update ASDM first, select **ASDM** from the drop down box and click **Browse Local Files**. Navigate to where you downloaded your images and select the new ASDM image then click **Upload Image**.

![asdm-update-05](46848f366fb696a545bbd3c0d5a880fb_MD5.png)

- You will receive a prompt asking to set this image as the ASDM image. Click **No** for now. If we select Yes you will not be able to use ASDM to connect to the secondary unit and upload the new images in the later steps.

![asdm-update-06](cb0c633555f11094b19a818cc5d4316f_MD5.png)

- Now let’s update the ASA software. Again, click **Tools** -> **Upgrade Software from Local Computer**. Select ASA from the drop down menu and click **Browse Local Files**. Navigate to where you downloaded your images and select the new ASA image then click **Upload Image**. Click yes on the prompt.

![asdm-update-09](00ab3b5bb0aeb3f903708a572b4f20bf_MD5.png)

![asdm-update-10](6063cef563bc365e298be31ccba9c445_MD5.png)

![asdm-update-11](aa44425138411aa5d4e73144b90ca19a_MD5.png)
- Open a new ASDM window and connect to the standby ASA IP address. Follow the same steps as above to upload the new ASDM and ASA images. Close the ASDM window for the secondary unit when finished.
- On the Primary ASDM window click **Configuration** -> **Device Management** -> expand **System Image/Configuration** -> click **Boot Image/Configuration**. Under **ASDM Image File Configuration** click **Browse Flash** and select our new ASDM image we uploaded earlier. Save the configuration.

![asdm-update-12](95f11ffef1e8ae73a32ebb3376e68f79_MD5.png)

![asdm-update-13](c52ea4af1b02266155a92ce761573078_MD5.png)
- We can now reload the secondary unit to start the upgrade process. Click **Monitoring** -> **Properties** -> **Failover** -> **Status**. Click **Reload Standby**.

![asdm-update-15](3e001728423679ffef8ec5479bb08739_MD5.png)

- Refresh the Failover Status page until the Secondary unit moves from a failed state to standby ready. Make note of the sw rev field. It should reflect the new ASA software version.

![asdm-update-16](c257e35113a5eb807cce09be894c57c0_MD5.png)
![asdm-update-18](9187516ce96a7ee44fa7125404279871_MD5.png)

- Force the active unit to failover to standby by clicking the **Make Standby** button on the Failover Status page. Refresh the Failover Status page to verify the Secondary unit is now the active unit. Now click **Reload Standby** to reboot the primary/standby unit and complete the upgrade.

![asdm-update-19](79579d9f0ee7900c4a109f84630bf49e_MD5.png)

- Exit ASDM and relaunch. You should now see the updated ASDM and ASA software versions under Device Information.

![asdm-update-20](0c3ecac1fbd0602d1390e29e0612c780_MD5.png)