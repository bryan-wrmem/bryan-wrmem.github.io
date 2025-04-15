+++
title = "Configuring SIP Integration Between CUCM and Unity Connection"
date = 2017-05-09T09:53:48-04:00
author = "bryan"
draft = false
tags = ["cisco"]
+++
Below are the steps to configure SIP integration between CUCM and Unity Connection. This is now the Cisco recommended best practice and replaces the legacy CTI Route Point configuration.

# Create New SIP Trunk Security Profile

- In CUCM, navigate to **System** > **Security** > **SIP Trunk Security Profile**

![](d43d97929a64472e31f0782e01c5856f_MD5.png)

- Click **Add New**

![](067768a05bc4eec644f2d1eb47d62c0b_MD5.png)

- Enter a Profile Name and Description, check **Accept Out-of-Dialog REFER**, **Accept Unsolicited Notification**, **Accept Replaces Header**. Click **Save**.

 ![](4915b5fae9d62fc6d21c1aeeaf59bbb4_MD5.png)
Create a SIP Profile

- In CUCM, navigate to **Device** > **Device Settings** > **SIP Profile**

![](8fd3e74d3ed13aae4644c76627bedc4e_MD5.png)

- To right of **Standard SIP Profile** click **Copy**.

![](9ec4e209a3e3fb70ea2b49f2f4acdb1d_MD5.png)

- Enter a Name and Description for the SIP Profile. I also like to enable **SIP OPTIONS Ping**, this will let you know if the SIP Trunk has been established, and for how long it has been up, on the **Find and List Trunks** page. Click **Save** when completed.

![](f45b347f28142d2d14f0bb1996304f96_MD5.png)
![](72e3a173656ce98c06b96031cebe8062_MD5.png)

Create SIP Trunk

- In CUCM, navigate to **Device** > **Trunk**.

![](e1ab5292c5b3e78feaafde81ab9b3cb0_MD5.png)

- Click **Add New**, select **SIP Trunk** and **SIP** from the drop down menus and click **Next**.

![](eb3d80b0596503062d88743e1c5318df_MD5.png)

![](adab109b5524a31f18a91c48e2b4a95e_MD5.png)

![](97fbcefe78531528acbcde9573c6e246_MD5.png)


- Enter a **Device Name** and **Description**. Select the proper **Device Pool** for the Trunk. Check R**un On All Active Unified CM Nodes**.

![](77a2770a6af3280fc627cbbc7bf9d0a5_MD5.png)

- Under **Inbound Calls** select the CSS for inbound CUXN if you have one and check **Redirecting Diversion Header Delivery – Inbound**.

![](74386a3b2f4ba39e94b69473d41750ee_MD5.png)

- Under **Outbound Calls**, check **Redirecting Diversion Header Delivery – Outbound**
- Be sure to set a **Rerouting Calling Search Space** to ensure the calls can be transferred from Unity back to CUCM.
- Under **SIP Information** enter the **Destination Address** of the Unity Connection Publisher. This can be an IP address or DNS name. Change the **SIP Trunk Security Profile** to the new profile we made earlier. Change the **SIP Profile** to the profile we made earlier. Click **Save**.

 ![](c0e0a3a32f2c25b896abc1b89fe8b45b_MD5.png)
 
 Create Route Group

- In CUCM, navigate to **Call Routing** > **Route/Hunt** > **Route Group**. Click **Add New**.

![](836655dd303505d57c2502d3d44fbc5e_MD5.png)

- Enter a name for the **Route Group**, change **Distribution Algorithm** to **Top Down**. Find the newly created SIP trunk under Find Devices and click **Add to Route Group**. Click **Save**.

 ![](b224e5790454be82ae1370fdae8e3a80_MD5.png)
 Create Route List

- In CUCM, navigate to **Call Routing** > **Route/Hunt** > **Route List**. Click **Add New**.

![](749732490eb98f925fc06a33a892d783_MD5.png)

- Enter a name for the **Route List** and click **Save**.

![](7bfa3720cf9c21d30801091ebd15c44c_MD5.png)
- Click **Add Route Group**. Select the previously configured Route Group from the drop down menu and click **Save**.

![](2e89ec0a91a2439742cac281225605e1_MD5.png)
![](5b04992160a055aec39976a27e71c0a5_MD5.png)

- Confirm that **Enable This Route List** and **Run On All Active Unified CM Nodes** are checked and that the correct Route List is displayed under **Route List Details**. Click **Save**.

 ![](83887fd2274afdef015c229775d3de37_MD5.png)
 Create Route Pattern

- In CUCM, navigate to **Call Routin**g > **Route/Hunt** > **Route Pattern**. Click **Add New**.

![](c6b46be3d0943e7c0a1b1906ebb9853d_MD5.png)

- Enter the voicemail pilot number you’d like to use under **Route Pattern**. Select the name of the Route List we configured earlier under **Gateway/Route List**. Click **Save**.

![](7f124d02444605a1ca5478ee876815ba_MD5.png)
Create Voice Mail Pilot

- In CUCM, navigate to **Advanced Features** > **Voice Mail** > **Voice Mail Pilot**. Click **Add New**.

![](b8c5a9e95be7e3d326af8804da05d5aa_MD5.png)

- Enter the **Voice Mail Pilot** number, this should match the Route Pattern we created earlier. Enter the **Calling Search Space** and **Description**, check **Make this default…** and click **Save**.

![](d853f5cb3a9b021d976b5f6a389ef28f_MD5.png)
Create Voice Mail Profile

- In CUCM, navigate to **Advanced Features** > **Voice Mail** > **Voice Mail Profile**. Click **Add New**.

![](702649d9d5a52669406a8fdc3174fb9b_MD5.png)

- Enter a **Voice Mail Profile Name** and **Description**. Select the Voice Mail Pilot configured earlier. Check **Make this the default…** if you want this profile to be the system default. Click **Save**.

 ![](3487f5c1fdcdd4578dadb3712f48f42e_MD5.png)

Configure Unity Connection

- In CUC, navigate to **Telephony Integration** and then click **Phone System**.

![](f752af98f7be9a53451c77321f6f6a4d_MD5.png)

- Click the default phone system and make any changes you’d like such as the **Phone System** **Name**.

![](b54dbda4eb806a1153a8b3b766b0799e_MD5.png)
- At the top right of the Phone System Basics page look for **Related Links**. Select **Add Port Group** and click **Go**.

![](a4a5ce411e82c0cc0821b0ae248a9fe3_MD5.png)

- Under **Create From**, change the **Port Group Type** to **SIP** from the drop down box. Give the Port Group a Display name. Under **Primary Server** settings enter the IP address of the CUCM server. Click **Save**.

![](70a3080ee5c31f6e66cf2d9a53bef76f_MD5.png)
- Under **Related Links** on the **Port Group Basics** page, select **Add Ports** and click **Go**.

![](39c4956690c3b4743752b40c553eefec_MD5.png)

- Enter the number of **Ports** and click **Save**.

![](f02edd33373d6f974aff72b8481b2fcb_MD5.png)
- Navigate to the **Port Group Basics** by going to **Telephony Integrations** > **Port Group** > and click the newly created port group.

![](87743038d0eea79b95287f84e1617642_MD5.png)
- Click **Edit** and select **Servers**.

![](1b3e7c110e21763afb3c38ecd9d4c69c_MD5.png)

- If you need to add secondary CUCM servers enter them under SIP servers. Follow the same steps to add additional TFTP servers.

![](0afdccf691ebd92ba111b012953371f1_MD5.png)
- Click **Edit** > **Port Group Basics**. Click **Reset** to reset the Port Group.

![](03eff76ddb6c0a3c0c66c8672ef64a1e_MD5.png)

# Test and Verify

This should be enough to configure basic SIP integration between CUCM and CUC. In CUCM you can navigate to Devices > Trunk and verify that the SIP trunk has been established. You can then test by dialing the voice mail pilot number and seeing if you hit Unity Connection.

![](a1d13810f05e05bb504d398e9c59b98f_MD5.png)

