---
layout: post
category: Raspberry Pi
tagline: "Hardware"
tags: [hardware, kisbian, raspberry pi, embedded devices, infosec, wifi, networking]
# `''` to remove well, any value to fill it
well_title: TL;DR
# primary success info warning danger
well_button_color: info
well_button_text: Introduction
well_body: I know we just met and this is crazy, but let's talk about wireless auditing, babeh.
---
{% include JB/setup %}

Kisbian is a project I've been working on in my spare time (which hasn't been much) for some months now. A combination hardware/firmware project. Initially the firmware was based off of Kali Linux, and since been re-based on Raspbian by way of the wonderful [pi-gen](https://github.com/RPi-Distro/pi-gen) build environment. But, today I just want to touch on the hardware side of things. Mostly because the firmware build environment, while functional, is still pretty messy and not to a stage I am proud pushing it. I'm hoping to get some time soon to clean that up and share it (it'll go to a Kisbian branch of my [pi-gen](https://github.com/KhasMek/pi-gen) fork fwiw).

{% include themes/bootstrap/image.html
            img="assets/images/2017-06-09-kisbian-part-1/02-closed.png"
            title="Current Kisbian Build" %}

<!--more-->

---

### WHAT IS KISBIAN?

Kis(met)(Rasp)bian, I'm so witty.

Kisbian is a hardware/firmware package to assist people in auditing 802.11 networks. This package is primarily based around a Raspberry Pi Zero (although it does work on any version of the Raspberry Pi!), some wireless hardware, a nice big battery (it's extremely important it can deliver **at least** 2.4A output per port), a minimal Raspbian install, and [Kismet](https://www.kismetwireless.net). Below is the exact parts list for the hardware I used in its current revision. You can certainly cut the price down further by cutting your own usb/rp-sma cables, not bothering with the Pibow (which means you don't need to get the screw kit too), using fewer (or cheaper) wireless adapters, or the stock wireless antennas - which are probably fine, if we're being honest. At this stage in the firmware build, one wireless adapter is okay, the only benefit you'll gain from multiple adapters is potentially better spectrum spread. However, the development version of Kisbian takes advantage of this by automatically detecting the weakest adapter and configures it as  a wireless access point so you can connect to the Kismet web ui and more easily monitor the assessment's progress.

##### HaK5 has this tool out...

While it's true, the Pineapple can run Kismet, Kisbian is not intended to do any sort of MiTM or anything like that offered by the Pineapple, just straight collection. The Pineapple is a fine piece of kit, Kisbian just works better in my use case for me, at a fraction the price.

#### PARTS LIST

| Hardware                                     | Link                                                    | Price (USD)  |
| :------------------------------------------- | :-----------------------------------------------------: | :----------: |
| 6" USB A to USB Mini B cable                 | [Amazon](https://www.amazon.com/gp/product/B00K86N0BM)  | $5.88        |
| 90mm RP-SMA Extension Cable                  | [Amazon](https://www.amazon.com/gp/product/B01DNMA9YI)  | $8.99        |
| Anker PowerCore 13000 Portable Charger       | [Amazon](https://www.amazon.com/gp/product/B00Z9QVE4Q)  | $29.99       |
| 6" USB A to USB Mirco B cable                | [Amazon](https://www.amazon.com/gp/product/B013G4EAEI)  | $4.99        |
| EVA Hard Protective Case                     | [Amazon](https://www.amazon.com/gp/product/B01MAWKEGM)  | $14.99       |
| Alfa 9dBi Omni-Directional WiFi Antenna (x2) | [Amazon](https://www.amazon.com/gp/product/B002MUU6L4)  | $9.99 (x2)   |
| GlobalSat USB GPS Receiver                   | [Amazon](https://www.amazon.com/gp/product/B008200LHW)  | $30.45       |
| RaLink RT5370 USB wifi Adapter               | [Amazon](https://www.amazon.com/gp/product/B019XUDHFC)  | $5.99        |
| Samsung 32GB MicroSD Card                    | [Amazon](https://www.amazon.com/gp/product/B01DOB6Y5Q)  | $12.99       |
| Powerlink 802.11b/g/n USB Adapter            | [Amazon](https://www.amazon.com/gp/product/B00E7ML88A/) | $27.40       |
| Nylon Hex Spacer/Screw/Nut Kit               | [Amazon](https://www.amazon.com/gp/product/B014J1ZLD6/) | $11.99       |
| Pibow Zero Case                              | [Adafruit](https://www.adafruit.com/product/3005)       | $5.95        |
| Zero4u 4 port USB HAT                        | [Adafruit](https://www.adafruit.com/product/3298)       | $9.95        |

{% include themes/bootstrap/image.html
            img="assets/images/2017-06-09-kisbian-part-1/01-open.png"
            title="Kisbian Internals" %}

#### INSTALL

**Warning:** I would consider this alpha to beta software. Use at your own risk, everything should be fine, but it may cause hair to grow in odd places, etc.

For this version, I would stick as close to the listed base hardware as possible, at least same wireless chipsets, same gps. Most of the cleanup that I need to do before I push the source has to do with converting hard coded definitions to be less stupid and more dynamic. But, sometimes in the early phases of development I like having everything as hard coded. Additionally, I'm not going to release a noobs package for it at this time, but will in the future.

- Download Kisbian [here](https://goo.gl/ZGjYWO) (hosted on Google Drive until I push the kisbian branch to GitHub).
- Install using the standard methods. [reference](https://www.raspberrypi.org/documentation/installation/installing-images/README.md)
- Remember the first boot takes a little bit longer as it's doing initial setup things - expanding the partition to use the entire sdcard, etc.

#### USAGE

Turn it on when you want it to scan, turn it off when you don't want it to scan.

- kismet_server *should* (works reliably for me) start automatically and begin scanning - you'll see activity from your wireless cards adapters.
- gps will be enabled by kismet_server, kismet_server will output logs as all available file types.
- logs and pcap files will be available in `/var/log/kismet/`.
- Using the listed battery pack should give you an easy 24 hours of scan time, depending on number/power of wireless adapters installed.

---

### WHAT IT DO?

Don't use this for malicious stuff, that's not how to awesome human. I am not responsible for your actions.

#### USE CASES

- Wireless assessments of business offices, campuses, parks and other sites to confirm all AP's are legitimate and secured as necessary.
- Multiple devices placed around locations a specific target is known to visit with the goals of gathering intel.
- Map out the Access Points around your town and overlay them onto Google Maps.
- Single or multiple devices placed around locations with the goal of collecting random data to run analytics against. For example, if you have one placed around busy intersections, you may begin to notice that certain mac address show up around the same time every day (commuting to/from work?), two mac address always show up together (a couple?), there's tons of connection building that one could extrapolate from the data collected.
- Combine the previous use cases to discover where certain mac address goes (from their broadcast requests), when, and what routes they typical take.

#### FUTURE IDEAS

Well, for now I'm really just going to focus on cleaning up the repo and releasing the build environment and firmware for Kisbian. I do have a couple ideas that I think I'd like to implement down the line, including-

- Support for other wireless auditing hardware/protocols. e.g. Ubertooth and BT/BTLE
- Larger support of 802.11 chipsets (e.g. Realtek RTL8812AU).
- A Flask built primary web portal to control the device more easily. This will enable the operator to bring up/down devices, monitor the status of other services, etc.
- A smaller, more weatherproofed and solar powered version for long term monitoring. You could combine this with an alert to sound an alarm whenever a certain vendors mac address was identified in the area for example.
- Wire momentary to safely shut down the device. This will fix an issue of Wireshark and other programs warning that the pcap file ended unexpectedly.
