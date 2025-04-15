+++
title = "Installing a CA Signed SSL Certificate in SourceFire Defense Center 6.0"
date = 2016-03-02T08:44:37-04:00
author = "bryan"
draft = false
tags = ["firepower"]
+++
I don’t know about you, but self-signed certificates seem to trigger my IT OCD. When possible, I like to replace self-signed certs with one signed by our Active Directory CA. Here are the steps involved to replace the self-signed certificate on Cisco’s FirePOWER Management Center/SourceFire Defense Center with one signed by your internal Active Directory Certificate Authority.

- In Defense Center, go to System -> Configuration -> HTTPS Certificate
- Click “Generate New CSR”

![sfcsr01](69093ca6bd83c0001a88e81824b01c5a_MD5.png)
- Fill out the Certificate Signing Request information, paying attention to the common name field. The common name should match the address you use to access defense center, e.g., defensecenter.domain.org

![sfcsr02](ba9d0ac3ce48fcf34744da2a46463c6b_MD5.png)
- Click generate, and copy/paste the certificate request output to notepad

![sfcsr03](dd169e21b1ad159fcc6c995e317e1963_MD5.png)
- Next, navigate to your Certificate Services website and click “Request a Certificate”

![sfcsr04](f2af25b845de3841aa3fd3c612d6c599_MD5.png)

- Click “Submit an advanced certificate request”

![sfcsr05](0a8ce7583f979a21402b4a53ad416c73_MD5.png)

- Under “Saved Request,”paste your certificate request output from earlier and select the Web Server certificate template. Click Submit

![sfcsr06](2ffb93303f75c5de61397dd64e11b5a3_MD5.png)

- Download your newly generated certificate (Base64 encoded) and open it with your text editor of choice

![sfcsr07](b3441601b9e0b38cd7d5a14c0182ae34_MD5.png)


![sfcsr08](7a7f5c9b49d90825872f99e53a2df9d8_MD5.png)
- Copy the output of your cert and go back to Defense Center. Navigate to System -> Configuration -> HTTPS Certificate
- Click Import HTTPS Certificate and paste your certificate information into “Server Certificate”

![sfcsr01](69093ca6bd83c0001a88e81824b01c5a_MD5.png)



![sfcsr09](b2ecb728d5febd3e3232a0a6e0e05216_MD5.png)
- Click “Save” and you should now see your new certificate installed.
- Reload Defense Center and you should now trust the web server (assuming of course you trust the root CA)

![sfcsr10](e70f3fc7bb36494e5ba52e5c7447e263_MD5.png)