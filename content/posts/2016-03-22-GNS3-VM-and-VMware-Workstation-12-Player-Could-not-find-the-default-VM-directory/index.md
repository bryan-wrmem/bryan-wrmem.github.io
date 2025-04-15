+++
title = "GNS3 VM and VMware Workstation 12 Player Could not find the default VM directory"
date = 2016-03-22T09:17:29-04:00
author = "bryan"
draft = false
tags = ["lab"]
+++
While setting up the new GNS3 1.4 Virtual Machine with VMware Workstation 12 Player, I ran into an interesting error that was preventing me from completing the installation.

![gns3-vm01](6f589d9b912a08598bfcae4022a694e7_MD5.png)
Thankfully the fix is fairly straight forward and requires that we edit the VMware Workstation preferences file.

- Open **preferences.ini** in your text editor of choice
    - `%Appdata%\VMware\preferences.ini`
- Add or edit the following line, changing the path to where your virtual machines are stored
    - `prefvmx.defaultVMPath = "C:\Path\To\My\VMs"`
- And that’s it. Save the ini and restart the GNS3 Setup Wizard.

![gns3-vm02](ed8802476bd774ae98acca296034565e_MD5.png)

![gns3-vm03](1cbbef92d584853b6eb2fc948d21d9b4_MD5.png)
Credit to the VMware support forums: [https://communities.vmware.com/thread/245114?start=0&tstart=0](https://communities.vmware.com/thread/245114?start=0&tstart=0)