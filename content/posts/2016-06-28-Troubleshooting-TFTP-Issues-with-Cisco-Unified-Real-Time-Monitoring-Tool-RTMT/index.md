+++
title = "Troubleshooting TFTP Issues with Cisco Unified Real Time Monitoring Tool - RTMT"
date = 2016-06-28T09:27:41-04:00
author = "bryan"
draft = false
tags = ["cisco"]
+++
I’ve recently began studying for my CCNA Collaboration exam and to help with my studies I’ve built a small collaboration lab. Nothing too fancy, a 2811 with PVDMs and FXO/FXS cards, 2 3750 PoE switches, and 2 Cisco 7960 VOIP phones. My lab server has a dedicated NIC which connects to the lab network and hosts a domain controller, workstation, and CUCM 11.5 virtual machines.

All this was working well until I configured the switches to have a separate dedicated voice VLAN. The phones started having issues contacting CUCM and downloading new configuration files or firmware. If I moved the phones back to the same VLAN as CUCM the phones would work properly. Sounded like a TFTP issue to me, and here are the steps I followed to resolve the issue.

[TLDR; ip tftp source-interface](http://www.firewall.cx/cisco-technical-knowledgebase/cisco-routers/882-cisco-ip-tftp-source-interface.html)

First, I setup a trace to capture TFTP logs from my CUCM server.

- From the CUCM Console: select **Cisco Unified Serviceability** from the Navigation drop down, and click **Go**.

![](a8ba6ac24c874b90e7ad26db1924feaa_MD5.png)

- On the **Cisco Unified Serviceability** page select **Trace** -> **Configuration**

![trace](b4a936c8629ad0dcb947a5db7d4097e8_MD5.png)
- Select your CUCM Server running the TFTP service for **Server** then select **CM Services** under **Service Group**.
- For **Service** select **Cisco Tftp** then click **Go**.
- Be sure **Trace On** is selected then change the **Trace Level** to **Detailed**.
- Click **Save**.

![](c44d67299ea66f2715f391a890cd1f20_MD5.png)

With the detailed TFTP trace enabled, I tried resetting the phones to duplicate the issue. Once I verified the issue was still occurring it was time to grab the trace files.

To download the trace files you’ll need to use the **Cisco Unified Real-Time Monitoring Tool** which comes bundled with CUCM. To download the tool, follow these steps.

- From the CUCM Administration page navigate to **Application** -> **Plugins**.

![](e847155c7951a66a455f63b8a2e27605_MD5.png)

- Near the bottom of the plugins page find the download link for Cisco Unified Real-Time Monitoring tools.
- Download the tool for your appropriate os and install.

![](9e73c0c9e35b8116266b0fd5e28d0b6c_MD5.png)
- When you run the program it will ask you for the IP of the CUCM server and the GUI username/password.

![](e00e0a429f2cc57b1beaf0aaa89106cb_MD5.png)

![](be3c203e61e9229f8ec7277ca84d5ee0_MD5.png)

![](ac41e0f9389a363bbbee9498fe78a920_MD5.png)

- Select **Trace & Log Central**. Then double click **Collect Files**.

![](e6c86d94d81262fab1a4222e6aa65320_MD5.png)

- Scroll down to find **Cisco Tftp** and select either All Servers or an individual server.

![](345623908f9107fcb35278a44b38d7f8_MD5.png)
- Select your download options and click **Finish**.

![](b56b15f21b2870419d34356d9a66f629_MD5.png)
You should now have a wealth of information to dive into to try and troubleshoot your issue. As I was perusing the TFTP trace logs a few lines popped out at me.

![](607811f716d699b8fd060a5f8e50c55e_MD5.png)

It would appear that the phone is contacting the TFTP server, but its IP address was not part of my voice VLAN. In fact, the source IP was the IP of the outside interface of my 2811!

After some quick google’ing I came across [this article](http://www.firewall.cx/cisco-technical-knowledgebase/cisco-routers/882-cisco-ip-tftp-source-interface.html) on the ip tftp source-interface command. Because my router was sourcing TFTP traffic from its outside interface, CUCM was not able to route the traffic back to 7960 phone.

Simply adding the **ip tftp source-interface** command followed by the VLAN my CUCM server resided in resolved the issue and my phones began registering again.
```

COLLAB-SW-01(config)#ip tftp source-interface vlan 103
COLLAB-SW-01(config)#end
COLLAB-SW-01#wr
Building configuration...
[OK]
COLLAB-SW-01#

```
