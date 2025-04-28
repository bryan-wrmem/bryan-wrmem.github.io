+++
title = "Firepower Booting into ROMMON"
date = 2025-04-28T19:11:28-04:00
author = "bryan"
draft = false
tags = ["cisco","firepower"]
+++
After recently deploying a new Firepower 3105 HA pair for a client, they decided to move the firewalls to a different rack. Upon powering up the devices, the secondary firewall wasn't coming online and syncing with the primary.

We plugged a console cable into the device and were greeted with a ROMMON prompt:

```
rommon 1>
```

This lines up pretty well with the following BugID:

https://quickview.cloudapps.cisco.com/quickview/bug/CSCvp57772

While the workaround mentions doing a complete reimage of the device, there is a much simpler fix. 

Simply change the configuration register back to 0x1, the default value that tells the FTD to boot from the local image and startup configuration. You can issue the reboot command to reboot the device and verify that the device loads as expected.

```
rommon 2> confreg 0x1
rommon 3> reboot
```

