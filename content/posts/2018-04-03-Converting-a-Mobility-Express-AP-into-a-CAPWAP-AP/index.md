+++
title = "Converting a Mobility Express AP into a CAPWAP AP"
date = 2025-04-15T10:03:24-04:00
author = "bryan"
draft = false
tags = ["cisco"]
+++
I was recently installing some Cisco 2802 APs and came across an issue where one of the APs  would grab a DHCP address, be reachable for a minute, and then drop off the network.

It turns out the AP having an issue actually had the Mobility Express image installed and needed to be converted to CAPWAP, even though we purchased the APs specifically with the CAPWAP SKU. Here are the troubleshooting steps I went through to convert the AP to CAPWAP.

After rebooting the AP a number of times to see if it would work, I threw a console cable on the device and saw some interesting output.

`[*04/12/2017 00:08:21.4871]` 
`[*04/12/2017 00:08:21.7248] waiting for POE negotiation to complete`
`[*04/12/2017 00:08:21.7248]` 
`[*04/12/2017 00:08:26.4876] waiting for POE negotiation to complete`
`[*04/12/2017 00:08:26.4876]` 
`[*04/12/2017 00:08:26.7252] waiting for POE negotiation to complete`
`[*04/12/2017 00:08:26.7252]` 
`[*04/12/2017 00:08:27.8015]` 
`[*04/12/2017 00:08:27.8015] !!!!! {/usr/bin/capwap_brain} failed writing /click/fromdevs_check_gw/color len 1 data: "0"`
`[*04/12/2017 00:08:27.8016] ethernet_port wired0, ip 192.168.1.2, netmask 255.255.255.255, gw 192.168.1.2, mtu 1500, bcast 192.168.1.255, dns1 0.0.0.0, dns2 8.8.8.8, domain sascs.org`
`[*04/12/2017 00:08:27.8123] !!!!! {/usr/bin/capwap_brain} failed writing /click/gw_h/nat/firewall_ssh_sw/sw len 1 data: "0"`
`[*04/12/2017 00:08:27.9885] chatter: tohost_virtual :: ToHost: device 'virtual' went down`

I rebooted the device again and came to this screen indicating that it was indeed in Mobility Express mode.

`(Cisco Controller)` 

`Cisco Aironet 2800 Series Mobility Express`
`Welcome to the Cisco Wizard Configuration Tool`
`Use the '-' character to backup`

To reset the AP into CAPWAP mode you’ll need to enter enable mode on the CLI and enter the “ap-type capwap” command.

`XXXX#ap-type capwap`
`AP is the Master AP, system will need a reboot when ap type is changed to CAPWAP`
`. Do you want to proceed? (y/N)`
`y`
`[*04/12/2017 00:17:01.1466] Cleaning ME configf any..`
`[*04/12/2017 00:17:01.1685] Cleaning config files..`
`[*04/12/2017 00:17:01.1765] AP Type changed: ME to CAPWAP. AP Mode changed to local mode. AP Rebooting...`
`[*04/12/2017 00:17:01.1819] AP Rebooting: Reset Request from Controller(AP Type Changed from ME to CAPWAP)`

The AP then rebooted, got its DHCP address, and successfully connected to the controller.

Documentation from Cisco to convert an AP from Mobility Express to CAPWAP can be found here:

[https://www.cisco.com/c/en/us/td/docs/wireless/controller/technotes/8-2/b_Mobility_Express_Deployment_guide/b_Mobility_Express_Deployment_guide_chapter_01100.html#task_CD04E8319602439D973B7D7ACE23111D](https://www.cisco.com/c/en/us/td/docs/wireless/controller/technotes/8-2/b_Mobility_Express_Deployment_guide/b_Mobility_Express_Deployment_guide_chapter_01100.html#task_CD04E8319602439D973B7D7ACE23111D)