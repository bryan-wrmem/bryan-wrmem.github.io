+++
title = "Set a Budget in AWS to Avoid Costly Overages"
date = 2018-08-10T10:05:49-04:00
author = "bryan"
draft = false
tags = ["aws"]
+++
If you’re like me and are experimenting with the AWS free tier it might be a good idea to configure a budget notification to avoid getting caught off guard by any overages.

Amazon has a great document on how to enable alerts located [here](https://docs.aws.amazon.com/awsaccountbilling/latest/aboutv2/tracking-free-tier-usage.html), but here’s a quick guide on how to enable alerts and budget reminders.

- First, log in to your AWS account.

- Click the drop down menu next to your name on the top right hand of the console page and select “**My Billing Dashboard**.”

![](687366d950f122c96d2430b55f727494_MD5.png)

- From the left hand menu select **Preferences.**

- Check “**Receive Free Tier Usage** **Alerts**” and enter your email address. Click **Save Preferences**.

![](fadf3a6588b9bbbd2fbdbc1de5ee869f_MD5.png)

- Next, click **Budgets** from the left hand menu.

- Click **Create Budget**.

![](b7cbafaec782235383710ab4e1c9cbe8_MD5.png)

- Select **Cost** and under **Budgeted Amount** select the threshold you’d like to be alerted at. For example, $0.01.

![](81018a99ac085c4341e146870de19dd9_MD5.png)

- Under **Notifications** select to be alerted when costs are equal to your 100% of your Budgeted Cost and enter your email address.

![](d762532e5a6ab8d415101277a6bd8451_MD5.png)

- Click **Create** and you should now have a new budget alert that will email you if you’re free tier is about to cost you some actual money.

![](8c014c88df07943b62988df0f5d268ba_MD5.png)