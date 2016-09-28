---
layout: post
category: howto 
tagline: "and configuration"
tags: [tutorial, GLi, GL-AR150, embedded devices, infosec, wifi, networking]
# `''` to remove well, any value to fill it
well_title: TL;DR
# primary success info warning danger
well_button_color: success
well_button_text: Easy
well_body: Flash Wifi Pineapple firmware up and running on a GLi GL-AR150.
---
{% include JB/setup %}

I was recently turned on to the [GL Innovations GL-AR150](http://www.gl-inet.com/ar150/ "GL-AR150") DIY router.
It's an Atheros 9331 SoC with specs very similar to the [Pineapple Nano](https://www.wifipineapple.com/pages/nano "nano"). As has already been mentioned by other people ( [here](https://www.gl-inet.com/wifi-pineapple-for-firmware-ar150/ "alzhao") | [here](http://jerrygamblin.com/2016/04/11/turning-a-25-gl-ar150-into-a-100-wifipineapple/ "Jerry Gamblin") ) Patrick Sapinski has ported the pineapple firmware to the GL-AR150 ( [Github](https://github.com/kow/glinetpineapple "kow") ) and it works quite nicely. However, my experience was slightly different than others that have talked about it. So I thought I'd share my process in hopes it may assist others considering this route.

The installation is fairly straightforward. Download the latest firmware from Github, currently [1.0.6 v2](https://github.com/kow/glinetpineapple/raw/master/nano-1.0.6-gl-ar150-sysupgrade-v2.bin "Github"). You'll need to set a static ip on your host computer's ethernet connection to 192.168.1.2 and connect it to the LAN port of the GL-AR150. While holding down the *reset* button, plug the USB power in. The red light will flash seven times and on the seventh flash the center green light will flash as well. Release the *reset* button and in a browser on your computer go to 192.168.1.1. If everything has gone correctly you will see the web UI for the devices U-Boot bootloader. 

{% include themes/bootstrap/image.html
            img="assets/images/2016-09-27-ar150-pineapple-install/01-uboot.png"
            title="U-Boot Web UI" %}

<!--more-->

Browse to the firmware you just downloaded and select *Open* and then *Update Firmware*.

{% include themes/bootstrap/image.html
            img="assets/images/2016-09-27-ar150-pineapple-install/02-uboot-select-firmware.png"
            title="U-Boot Select Firmware" %}

{% include themes/bootstrap/image.html
            img="assets/images/2016-09-27-ar150-pineapple-install/03-uboot-update-in-progress.png"
            title="U-Boot Update In Progress" %}

 After a couple of minutes the GL-AR150 will silently reboot (you'll only be notified by a flash in the lights). But, if you weren't paying attention to it, after five minutes you should be safe to proceed. Disable the static IP on your computer, reconnect your computer to the GL-AR150's WAN port and you should receive a dynamic IP address of something like 172.16.42.42. If not, try setting a static IP to that. Again, in your browser visit 172.16.42.1:**1471** and go through the first time setup of WiFi Pineapple.

{% include themes/bootstrap/image.html
            img="assets/images/2016-09-27-ar150-pineapple-install/04-wifi-pineapple-first-boot.png"
            title="WiFi Pineapple First Boot" %}

It is recommended to do the initial setup in secure mode.

{% include themes/bootstrap/image.html
            img="assets/images/2016-09-27-ar150-pineapple-install/05-wifi-pineapple-secure-setup.png"
            title="Wifi Pineapple Secure Setup" %}

{% include themes/bootstrap/image.html
            img="assets/images/2016-09-27-ar150-pineapple-install/06-wifi-pineapple-admin-setup.png"
            title="WiFi Pineapple Admin Setup" %}

{% include themes/bootstrap/image.html
            img="assets/images/2016-09-27-ar150-pineapple-install/07-wifi-pineapple-setup-complete.png"
            title="WiFi Pineapple Setup Complete" %}

{% include themes/bootstrap/image.html
            img="assets/images/2016-09-27-ar150-pineapple-install/08-wifi-pineapple-login-screen.png"
            title="WiFi Pineapple Login Screen" %}

{% include themes/bootstrap/image.html
            img="assets/images/2016-09-27-ar150-pineapple-install/09-wifi-pineapple-dashboard.png"
            title="WiFi Pineapple Dashboard" %}

Once you have everything up and running, you'll want to actually have some fun with the hardware and for that you'll need an Internet connection.

{% include themes/bootstrap/image.html
            img="assets/images/2016-09-27-ar150-pineapple-install/10-wifi-pineapple-no-internet.png"
            title="No Internet Connection" %}

 Wifi Pineapple has a handy script for setting up forwarding if you don't know how to manually do it. The linux version ( [here](http://wifipineapple.com/wp6.sh) ) doesn't quite work for me on the GL-AR150 due to the regex for the mac address being wrong. I would assume the same is true for other versions of the script. There is two occurrences of the faulty regex, line 184 and 196 at the time of this writing. Below is a quick shell script to download/fix the script with the mac address of the **device connected to the GLi-AR150** (get the mac address by running `ifconfig`)

```bash
#!/bin/sh

echo "  Downloading the most current version of wp6.sh."
wget wifipineapple.com/wp6.sh -q --show-progress
echo "  Enter the mac address of your network device..."
read mac
sed -i "/pineappleiface/{;s/00:\[cC\]0:\[cC\]\[aA\]\\\|00:13:37/$mac/g}" wp6.sh
chmod +x wp6.sh
echo "  mac address updated."
echo "  From now on you only need to execute wp6.sh."
```
[Download](https://gist.githubusercontent.com/KhasMek/2997f99dbf8f5f1289d962e857023a09/raw/1804831a85066bb46c153153ddfd8ca684851046/update_wp6.sh)

{% include themes/bootstrap/image.html
            img="assets/images/2016-09-27-ar150-pineapple-install/11-wifi-pineapple-with-internet.png"
            title="Working Internet Connection" %}

From here on out your experience will be nearly identical to using an official Pineapple Nano with a few exceptions. The GL-AR150 only has one onboard wireless card, so if you want to do any of the fun client mode stuff you'll need to plug in a USB wifi adapter. I'm using this [Alpha](https://www.amazon.com/gp/product/B001O9X9EU) and it's been working just fine. 

{% include themes/bootstrap/image.html
            img="assets/images/2016-09-27-ar150-pineapple-install/12-wifi-pineapple-no-wlan1.png"
            title="Client Mode Disabled Without USB WiFi Adapter" %}

{% include themes/bootstrap/image.html
            img="assets/images/2016-09-27-ar150-pineapple-install/13-wifi-pineapple-with-wlan1.png"
            title="Client Mode Enabled With USB WiFi Adapter Connected" %}

Clearly, there are two ethernet ports on the GL-AR150, I've found things work more consistently if I use the WAN port as the admin interface apposed to the LAN port, but ymmv.

The last thing I would stay away from is the official updater. I have tried it without any success, so I would assume there's some signature check or something going on. In any case, if it did flash, there could be some issues due to the slight variances. Then again, U-Boot to the rescue.

{% include themes/bootstrap/image.html
            img="assets/images/2016-09-27-ar150-pineapple-install/14-wifi-pineapple-check-for-update.png"
            title="Maybe don't auto update?" %}
