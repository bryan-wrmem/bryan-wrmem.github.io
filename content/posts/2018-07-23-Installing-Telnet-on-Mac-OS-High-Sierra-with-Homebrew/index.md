+++
title = "Installing Telnet on MAC OS High Sierra with Homebrew"
date = 2018-07-23T10:05:01-04:00
author = "bryan"
draft = false
tags = []
+++
I was both sad and excited to see that Apple removed telnet from Mac OS High Sierra, excited because [telnet is a nearly 40 year old protocol that is highly insecure for network management](https://www.ssh.com/ssh/telnet) and saddened because I often have to use telnet when at client sites.

There are a few different methods you can use to bring back telnet, including copying over the binaries from a Sierra install to /usr/local/bin (as seen [here](https://www.petenetlive.com/KB/Article/0001364))  or using a session manager like [SecureCRT](https://www.vandyke.com/products/securecrt/), but since I’ve been using [Homebrew](https://brew.sh/) to manage a few other packages for a while now I figured I’d just go ahead and use that.

- First install Homebrew from your terminal
    `/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"`
    
- Run
    
    `brew install telnet`
    

`==> Downloading https://homebrew.bintray.com/bottles/telnet-54.50.1.high_sierra.`
`Already downloaded: /Users/me/Library/Caches/Homebrew/telnet-54.50.1.high_sierra.bottle.1.tar.gz`
`==> Pouring telnet-54.50.1.high_sierra.bottle.1.tar.gz`
`🍺  /usr/local/Cellar/telnet/54.50.1: 4 files, 246KB`

There you have it. Run telnet by simply issuing the telnet command and the ip/port you want to connect to.

`telnet towel.blinkenlights.nl`