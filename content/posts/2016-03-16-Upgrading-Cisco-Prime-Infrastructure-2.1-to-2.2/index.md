+++
title = "Upgrading Cisco Prime Infrastructure 2.1 to 2.2"
date = 2016-03-16T09:08:11-04:00
author = "bryan"
draft = false
tags = ["cisco"]
+++
At the moment we’re running Cisco Prime Infrastructure 2.1 on a Gen1 physical appliance. We’re looking to take the upgrade path from 2.1 all the way up to 3.1 (currently only 3.0.2 is supported on the Gen1 appliance).

First stop, 2.2.

The Gen1 appliance upgrade path isn’t a fun one. It requires that we back up our current application database, wipe our appliance, do a bare-metal install of 2.2, and then restore our application database. Cisco’s documentation for application backup and restore can be found here: [http://www.cisco.com/c/en/us/td/docs/net_mgmt/prime/infrastructure/2-2/administrator/guide/PIAdminBook/backup_restore.html#72460](http://www.cisco.com/c/en/us/td/docs/net_mgmt/prime/infrastructure/2-2/administrator/guide/PIAdminBook/backup_restore.html#72460)

---

**Step one  
**

Back up the PI application database to an FTP repository (I recommend [FileZilla Server](https://filezilla-project.org/download.php?type=server) for hosting a light-weight FTP server on your workstation).

- Create a ftp repository on your Prime Infrastructure server via CLI
    - SSH to PI
    - `conf t`
    - `repository _NAME_`
    - `url ftp://x.x.x.x`
    - `user _username_ password plain _password_`
- Verify your repository configuration
    - `show repository _NAME_`

![pi2-1](9b11af0792304b02cda5b326a1066ad4_MD5.png)

- Backup your Prime Infrastructure application
    - `backup _backup-name_ repository _repository-name_ application NCS`

![](faaaa6ce9c74f4a225f6fdcb7d529df0_MD5.png)

---

**Step two**

Install Prime Infrastructure 2.2

Reboot your appliance from the PI 2.2 installation media and follow the on-screen configuration prompts. For more information follow Cisco’s [Installation Guide](http://www.cisco.com/c/en/us/td/docs/net_mgmt/prime/infrastructure/2-0/install/guide/Cisco_PI_Hardware_Appliance_Installation_Guide/cpi_higmain.html#pgfId-1061175)


![pi2-5](2630b83033557c2c88182fd5bb2a195f_MD5.png)


**Step three**

Restore your application database

- SSH to the Prime Infrastructure server and setup your ftp repository again
    - `conf t`
    - `repository _NAME_`
    - `url ftp://x.x.x.x`
    - `user _username_ password plain _password_`
- Verify your repository configuration, and check that your backup is there
    - `show repository _NAME_`

![pi2-1](9b11af0792304b02cda5b326a1066ad4_MD5.png)

![pi2-3](2afb14d55e205419bb34f83eeb173ab9_MD5.png)
- Run the restore command, taking note of the scary warnings
    - `restore _BACKUP_NAME.tar.gpg_ repository _REPOSITORY_NAME_ application NCS`

![pi2-6](4cde3c97aa7a0d2e6933faf0ee6ad5f9_MD5.png)

![pi2-7](708f63c77dc78109df420969876c64f5_MD5.png)