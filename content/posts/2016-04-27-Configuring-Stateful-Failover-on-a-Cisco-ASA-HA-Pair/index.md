+++
title = "Configuring Stateful Failover on a Cisco ASA HA Pair"
date = 2016-04-27T09:21:52-04:00
author = "bryan"
draft = false
tags = ["cisco","asa"]
+++
The ASA, Cisco’s Adaptive Security Appliance, has been around for over 15 years and has since become an ubiquitous network security solution, securing networks the world over.

Because it is such a critical device in our networks, it has become best practice to deploy these security appliances in a resilient and highly available configuration.

Currently, Cisco supports **Active/Active** as well as **Active/Standby** failover. This article contains a simple example of how to configure **Active/Standby** stateful high availability on a pair of Cisco ASAs, where one unit acts as the primary ASA and a standby unit becomes active once a failover has occurred. When stateful failover is enabled, connection states are continuously passed between the active and standby units keeping session information available to the new active unit. _Please note that both ASAs must be running identical hardware and software versions._

> Detailed instructions from Cisco on how to configure Failover can be found here: [http://www.cisco.com/c/en/us/td/docs/security/asa/asa91/configuration/general/asa_91_general_config/ha_failover.html](http://www.cisco.com/c/en/us/td/docs/security/asa/asa91/configuration/general/asa_91_general_config/ha_failover.html)

- Each interface on the primary ASA will need an additional “standby” IP address, for example:

interface GigabitEthernet0/0
description Inside Interface
nameif Inside
security-level 100
ip address 10.1.1.1 255.255.255.0 standby 10.1.1.2

- Specify failover interface on the primary ASA
    
    `ciscoasa(config)# failover lan interface failover GigabitEthernet0/7`
    
- Configure failover link IP address

`ciscoasa(config)# failover interface ip failover 192.168.1.1 255.255.255.0 standby 192.168.1.2`

- Configure shared failover key

`ciscoasa(config)# failover key PASSWORD123`

- Configure ASA as primary

`ciscoasa(config)# failover lan unit primary`

- Enable stateful failover

`ciscoasa(config)# failover link failover GigabitEthernet0/7`

- Enable failover

`ciscoasa(config)# failover`

- On the secondary ASA we’ll need to do some similar configuration:
```

    ciscoasa(config)# failover
    ciscoasa(config)# failover lan unit secondary
    ciscoasa(config)# failover lan interface failover GigabitEthernet0/7
    INFO: Non-failover interface config is cleared on GigabitEthernet0/7 and its sub-interfaces
    ciscoasa(config)# failover link failover GigabitEthernet0/7
    ciscoasa(config)# failover interface ip failover 192.168.1.1 255.255.255.0 standby 192.168.1.2
    ciscoasa(config)# failover key PASSWORD123
    

```

If everything is configured properly you should see some console output regarding configuration replication.

`Detected an Active mate`
`Beginning configuration replication from mate.`
`End Configuration Replication to mate`

Now verify the status of failover with a show failover:

```

ciscoasa# show failover
Failover On
Failover unit Primary
Failover LAN Interface: failover GigabitEthernet0/7 (up)
Unit Poll frequency 1 seconds, holdtime 15 seconds
Interface Poll frequency 5 seconds, holdtime 25 seconds
Interface Policy 1
Monitored Interfaces 2 of 216 maximum
MAC Address Move Notification Interval not set
failover replication http
Version: Ours 9.2(2)4, Mate 9.2(2)4
Last Failover at: 03:14:15 EDT Apr 22 2016
 This host: Primary - Active
 Active time: 469208 (sec)
 slot 0: ASA5525 hw/sw rev (1.0/9.2(2)4) status (Up Sys)
 Interface Inside (10.88.31.3): Normal (Monitored)
 Interface management (192.168.1.1): No Link (Waiting)
 slot 1: SFR5525 hw/sw rev (N/A/5.3.1-152) status (Up/Up)
 ASA FirePOWER, 5.3.1-152, Up
 Other host: Secondary - Standby Ready
 Active time: 2808 (sec)
 slot 0: ASA5525 hw/sw rev (1.0/9.2(2)4) status (Up Sys)
 Interface Inside (10.88.31.4): Normal (Monitored)
 Interface management (0.0.0.0): No Link (Waiting)
 slot 1: SFR5525 hw/sw rev (N/A/5.3.1-152) status (Up/Up)
 ASA FirePOWER, 5.3.1-152, Up

Stateful Failover Logical Update Statistics
 Link : FO GigabitEthernet0/7 (up)
 Stateful Obj xmit xerr rcv rerr
 General 119141 0 62582 0
 sys cmd 62578 0 62578 0
 up time 0 0 0 0
 RPC services 0 0 0 0
 TCP conn 7 0 0 0
 UDP conn 0 0 0 0
 ARP tbl 56548 0 3 0
 Xlate_Timeout 0 0 0 0
 IPv6 ND tbl 0 0 0 0
 VPN IKEv1 SA 0 0 0 0
 VPN IKEv1 P2 0 0 0 0
 VPN IKEv2 SA 0 0 0 0
 VPN IKEv2 P2 0 0 0 0
 VPN CTCP upd 0 0 0 0
 VPN SDI upd 0 0 0 0
 VPN DHCP upd 0 0 0 0
 SIP Session 0 0 0 0
 Route Session 8 0 0 0
 Router ID 0 0 0 0
 User-Identity 0 0 1 0
 CTS SGTNAME 0 0 0 0
 CTS PAC 0 0 0 0
 TrustSec-SXP 0 0 0 0
 IPv6 Route 0 0 0 0
 STS Table 0 0 0 0

 Logical Update Queue Information
 Cur Max Total
 Recv Q: 0 10 62681
 Xmit Q: 0 1 373300
```
