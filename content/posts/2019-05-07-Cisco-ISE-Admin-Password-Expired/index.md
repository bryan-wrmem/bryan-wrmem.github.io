+++
title = "Cisco ISE Admin Password Expired"
date = 2019-05-07T10:24:55-04:00
author = "bryan"
draft = false
tags = ["cisco","ise"]
+++
So you’ve installed ISE, configured your policies, got everything up and running. Life is peachy…until you attempt to log in and receive the following error:

![](b101477cc9e64dd5d7828db1353f52c3_MD5.png)

One little gotcha. Out of the box, the GUI admin account is set to expire after 45 days. So just when you thought everything was up and running smoothly you hit this little stumbling block.

Thankfully the fix is fairly straight forward, but if you’ve just received the error above you’ll need to do a little CLI intervention.

SSH into your ISE server with your CLI username/password. At the command prompt enter **“application reset-passwd ise admin”** and follow the on-screen prompts:

![](fcf167e6d9279f0948e6664f8c75f45f_MD5.png)

Now to avoid the headache of resetting your password from the CLI every 45 days you can edit the admin password policy to allow for a more lenient password history or disable expiration completely.

To do so, log into ISE then go to **Administration -> Admin Access**.

Under the Authentication section look for **Password Lifetime**. Here we can edit/enable/disable password expiration settings or even configure email alerts when the password is about to expire.

![](0ad440ad4c816571e411177a4676a5a1_MD5.png)

![](2d1e2a37de8f81a7b213981ab1bbf596_MD5.png)
