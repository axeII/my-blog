---
date: 2022-02-02T23:46:00Z
description: ""
draft: false
slug: "my-home-lab-upgrade"
tags: ["tech", "sys admin"]
series: ["home-lab"]
series_order: 2
title: "My home lab: Upgrade"

showHero: false
---

## Leak

First here's the kicker: behind the ubiquit's massive leak is employee who was using a regular old commercial VPN, and get this, paid for it with his personal credit card! Talk about leaving a digital trail. Ubiquiti, however, hasn't said anything about the whole thing. I'm hoping it's because they already had their suspicions about from inside.

Let's hope that this will not happen again.


## Upgrade
I was not satisfied with the setup how it is. So I decided to upgrade my gear. It never ends! The main reason for this upgrade was that my network infrastructure was getting bigger. I had to add more AP for better signal distribution. Unfortunately, I've run out of ethernet slots on my UDM Pro so I had to add a new switch to my home lab setup. I decide to add Switch 16 PoE (https://store.ui.com/collections/unifi-network-switching/products/usw-16-poe). This switch has 8 ports supporting 802.3at PoE+ standard that I needed to power supply my new 6 APs. Thanks to this I was able to save space on power supply sockets. Also, I made more space in my rack since I could remove Unifi Poe adapters that were suppling previously power to my APs.

![fresh look](images/image06.jpg "New fresh look")

Now about APs. As mentioned before, they support the new WIFI standard called "WIFI 6". However, don't mistake these with ones supporting 6Ghz. These AP's don't support this bandwidth, unfortunately. However, antennas in these AP are much more powerful. They have more transmit power. Data throughput is much higher than in previous generations. The Access Point WiFi 6 Long-Range has over 5 GHz band 2.4 Gbps through (4x4 MU-MIMO and OFDMA). And Access Point WiFi 6 Lite _only_ 1.2 Gbps (2x2 MU-MIMO and OFDMA). So both are more than enough for my network that supports a maximum speed 1Gbps. I plan a 10Gbps upgrade in the future. But right now 1Gbps is enough speed for my network. Also, the price for 10G switches is really high. And even if would have a 10Gbps switch I would still need next-generation APs that support such a speed. The WiFi 6E standard will finally support the 6Ghz band. First Access Points are coming out in 2022.
