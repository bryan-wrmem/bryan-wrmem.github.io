+++
title = "Zero Downtime ASA Upgrade - CLI"
date = 2016-04-30T09:27:02-04:00
author = "bryan"
draft = false
tags = ["cisco","asa"]
+++
So you have [stateful failover](https://wrmem.net/index.php/2016/04/27/configuring-stateful-failover-on-a-cisco-asa-ha-pair/) configured on your pair of Cisco ASAs and need to upgrade ASDM or the os? Maybe a [critical security vulnerability](https://tools.cisco.com/security/center/content/CiscoSecurityAdvisory/cisco-sa-20160210-asa-ike) was discovered with the software and you need to upgrade them ASAP. With stateful failover, we can perform a zero downtime upgrade on our ASAs to minimize end user disruption. Below are the steps I used to upgrade a pair of ASA 5525-X’s using the command line interface. You can find Cisco’s documentation for upgrading an Active/Standby Failover Configuration [here](http://www.cisco.com/c/en/us/td/docs/security/asa/asa84/configuration/guide/asa_84_cli_config/admin_swconfig.html#wp1380398).

- First, back up your current configuration!

```
ciscoasas# copy running-config tftp://10.1.1.10/ciscoasa-config.txt

Source filename [running-config]?

Address or name of remote host [10.1.1.10]?

Destination filename [ciscoasa-config.txt]?
Cryptochecksum: 495070f8 a3de0875 adfc16d6 18c04464
!!
4996 bytes copied in 0.660 secs
```

- Download your updated ASDM and ASA software from [Cisco.com](https://software.cisco.com/download/navigator.html?i=!mmd)
- Copy new ASA operating system to active and standby units.

```
ciscoasa# copy tftp://10.1.1.10/asa952-smp-k8.bin disk0:/asa952-smp-k8.bin

Address or name of remote host [10.1.1.10]?

Source filename [asa952-smp-k8.bin]?

Destination filename [asa952-smp-k8.bin]?

Accessing tftp://10.1.1.10/asa952-smp-k8.bin...
!!!!!!!
Writing file disk0:/asa952-smp-k8.bin...
!!!!!!!
82593792 bytes copied in 211.300 secs (391439 bytes/sec)

```

```
ciscoasa# failover exec mate copy /noconfirm tftp://10.1.1.10/asa952-smp-k8.$

Accessing tftp://10.1.1.10/asa952-smp-k8.bin...
!!!!!!!
Writing file disk0:/asa952-smp-k8.bin...
!!!!!!!
82593792 bytes copied in 192.570 secs (430176 bytes/sec)
```

- Copy new ASDM software to active and standby units.

```
ciscoasa# copy tftp://10.1.1.10/asdm-752.bin disk0:/asdm-752.bin

Address or name of remote host [10.1.1.10]?

Source filename [asdm-752.bin]?

Destination filename [asdm-752.bin]?

Accessing tftp://10.1.1.10/asdm-752.bin...
!!!!!!!
Writing file disk0:/asdm-752.bin...
!!!!!!!
25627616 bytes copied in 57.570 secs (449607 bytes/sec)
```

```
ciscoasa# failover exec mate copy /noconfirm tftp://10.1.1.10/asdm-752.bin d$

Accessing tftp://10.1.1.10/asdm-752.bin...
!!!!!!!
Writing file disk0:/asdm-752.bin...
!!!!!!!
25627616 bytes copied in 58.190 secs (441855 bytes/sec)
```

- Verify the current boot images, making note of the boot order. Remove the current image and set the config to boot from the newly uploaded image followed by the old image as a back up.

```
ciscoasa# sh running-config boot system
boot system disk0:/asa922-4-smp-k8.bin
ciscoasa# conf t
ciscoasa(config)# no boot system disk0:/asa922-4-smp-k8.bin
ciscoasa(config)# boot system disk0:/asa952-smp-k8.bin
ciscoasa(config)# boot system disk0:/asa922-4-smp-k8.bin
ciscoasa(config)# end
ciscoasa# sh running-config boot system
boot system disk0:/asa952-smp-k8.bin
boot system disk0:/asa922-4-smp-k8.bin
```

- Configure the ASA to use the new ASDM image

```
ciscoasa(config)# asdm image disk0:/asdm-752.bin
```

- Save your configuration and reload the secondary standby unit.

```
ciscoasa# wr
Building configuration...
Cryptochecksum: c4c58b75 ea6ca884 235cc59e 05d6f114

5030 bytes copied in 0.670 secs
[OK]
ciscoasa# failover reload-standby
ciscoasa#
************WARNING****WARNING****WARNING********************************
   Mate version 9.5(2) is not identical with ours 9.2(2)4
************WARNING****WARNING****WARNING********************************
Beginning configuration replication: Sending to mate.
End Configuration Replication to mate
```

- Verify the standby unit is back up and running the new software version.

```
ciscoasa# sh failover
Failover On
Failover unit Primary
Failover LAN Interface: FO GigabitEthernet0/7 (up)
Unit Poll frequency 1 seconds, holdtime 15 seconds
Interface Poll frequency 5 seconds, holdtime 25 seconds
Interface Policy 1
Monitored Interfaces 2 of 216 maximum
MAC Address Move Notification Interval not set
failover replication http
Version: Ours 9.2(2)4, Mate 9.5(2)
Last Failover at: 03:14:15 EDT Apr 22 2016
        This host: Primary - Active
                Active time: 605706 (sec)
                slot 0: ASA5525 hw/sw rev (1.0/9.2(2)4) status (Up Sys)
                  Interface Inside (10.1.1.1): Normal (Monitored)
                  Interface management (192.168.1.1): No Link (Waiting)
                slot 1: SFR5525 hw/sw rev (N/A/5.3.1-152) status (Up/Up)
                  ASA FirePOWER, 5.3.1-152, Up
        Other host: Secondary - Standby Ready
                Active time: 0 (sec)
                slot 0: ASA5525 hw/sw rev (1.0/9.5(2)) status (Up Sys)
                  Interface Inside (10.1.1.2): Normal (Monitored)
                  Interface management (0.0.0.0): No Link (Waiting)
                slot 1: SFR5525 hw/sw rev (N/A/) status (Init/Up)
```

- Make the active primary unit the new standy unit

```
ciscoasa# no failover active
```

- You may need to re-establish your SSH connection. Log back into the ASA and verify that the secondary unit is now the active unit. Reload the primary/standby unit, wait a few minutes and verify that both units are now running identical code.

```
ciscoasa# failover reload-standby
ciscoasa# Beginning configuration replication: Sending to mate.
End Configuration Replication to mate
ciscoasa# sh failover
Failover On
Failover unit Secondary
Failover LAN Interface: FO GigabitEthernet0/7 (up)
Reconnect timeout 0:00:00
Unit Poll frequency 1 seconds, holdtime 15 seconds
Interface Poll frequency 5 seconds, holdtime 25 seconds
Interface Policy 1
Monitored Interfaces 2 of 216 maximum
MAC Address Move Notification Interval not set
failover replication http
Version: Ours 9.5(2), Mate 9.5(2)
Last Failover at: 10:34:53 EDT Apr 29 2016
        This host: Secondary - Active
                Active time: 429 (sec)
                slot 0: ASA5525 hw/sw rev (1.0/9.5(2)) status (Up Sys)
                  Interface Inside (10.1.1.1): Normal (Monitored)
                  Interface management (192.168.1.1): No Link (Waiting)
                slot 1: SFR5525 hw/sw rev (N/A/5.3.1-152) status (Up/Up)
                  ASA FirePOWER, 5.3.1-152, Up, (Monitored)
        Other host: Primary - Standby Ready
                Active time: 0 (sec)
                slot 0: ASA5525 hw/sw rev (1.0/9.5(2)) status (Up Sys)
                  Interface Inside (10.1.1.2): Normal (Monitored)
                  Interface management (0.0.0.0): No Link (Waiting)
                slot 1: SFR5525 hw/sw rev (N/A/) status (Init/Up)

Stateful Failover Logical Update Statistics
        Link : FO GigabitEthernet0/7 (up)
        Stateful Obj    xmit       xerr       rcv        rerr
        General         101        0          97         0
        sys cmd         93         0          92         0
        up time         0          0          0          0
        RPC services    0          0          0          0
        TCP conn        1          0          0          0
        UDP conn        0          0          0          0
        ARP tbl         5          0          4          0
        Xlate_Timeout   0          0          0          0
        IPv6 ND tbl     0          0          0          0
        VPN IKEv1 SA    0          0          0          0
        VPN IKEv1 P2    0          0          0          0
        VPN IKEv2 SA    0          0          0          0
        VPN IKEv2 P2    0          0          0          0
        VPN CTCP upd    0          0          0          0
        VPN SDI upd     0          0          0          0
        VPN DHCP upd    0          0          0          0
        SIP Session     0          0          0          0
        SIP Tx  0          0          0          0
        SIP Pinhole     0          0          0          0
        Route Session   1          0          0          0
        Router ID       0          0          0          0
        User-Identity   1          0          1          0
        CTS SGTNAME     0          0          0          0
        CTS PAC         0          0          0          0
        TrustSec-SXP    0          0          0          0
        IPv6 Route      0          0          0          0
        STS Table       0          0          0          0

        Logical Update Queue Information
                        Cur     Max     Total
        Recv Q:         0       12      336
        Xmit Q:         0       29      300
ciscoasa#
```

And there you have it. If you’d like, after monitoring and verification, you can remove the old ASA and ASDM images from the boot order commands as well as the disk to keep things clean.

```
ciscoasa# delete /replicate disk0:/asa922-4-smp-k8.bin

Delete filename [asa922-4-smp-k8.bin]?

Delete disk0:/asa922-4-smp-k8.bin? [confirm]

ciscoasa# delete /replicate disk0:/asdm-7221.bin

Delete filename [asdm-7221.bin]?

Delete disk0:/asdm-7221.bin? [confirm]
ciscoasa# sh run boot
boot system disk0:/asa952-smp-k8.bin
boot system disk0:/asa922-4-smp-k8.bin
ciscoasa# conf t
ciscoasa(config)# no boot system disk0:/asa922-4-smp-k8.bin
ciscoasa(config)# end
ciscoasa# sh run boot
boot system disk0:/asa952-smp-k8.bin
ciscoasa#
```
