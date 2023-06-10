---
# categories: ["tech", "sys admin"]
date: 2021-07-02T23:46:00Z
description: ""
draft: false
slug: "my-home-lab"
tags: ["tech", "sys admin"]
series: ["home-lab"]
series_order: 1
title: "My home lab: Upgrade 2.0"
---


I enjoy working with server hardware and networking stuff, working with them, breaking and fixing them. It's my hobby. Probably the biggest inspiration doing this stuff comes `r/homelab`, `r/sysadmin/`, `r/selfhosted` which is my usual source. By self hosting stuff, you can gain great expericence and freedombut also responsibility. You break something you must fix it or users will not be happy (family). You can manage photos for whole family, manage network, DNS, manage backed up data or even better you can manage personal plex server which you can share with friends!

For me biggest advantage by doing this  stuff and no going with cloud way for home stuff  is that you can learn about whole processing managing personal "clouod". From hardware, network, to applications every tool is under your control. And it's up to you how wild you can go. So here is my post about my setup. I share this to help community the same way that community helped me - by documenting stuff and be inspiring and sharing it with others.

_Anyway_

There are some closed source technologies in my setup - both software and hardware, but overall most of the stuff is FOSS. Tools like proxmox, ubuntu, btrfs, etc. I think that the biggest advantage using them is the community. Blogs, discord channels, reddit posts and many more can be great source of knowlage when trying to understand how to use them. I always like to choose technology where can I find active users who communicate and share knowledge but also help each other. Funny is that even unifi biggest and most helpful support I found is the unofficial channel (you guys rock).

But enough of that and let's get to my homelab. While working on my home setup the biggest challages were:

- small space, small rack (I cloud not purchase classic server with depth 60cm or more)
- noise (room was pretty close to the bedroom)
- again small space so heat issues
- did I mention small space?

So to beat these challenges I had to make several choices. I could not afford a classic server because of depth of my rack so I decided to go with something like a single-board computer (like a raspberry pi) but more powerful like Intel NUC (AMD fans dont' worry and read more!) .

## Issue 1: Server

The first problem was - which hardware to go with. I could go with Raspberry pi as 80% people who selfhost stuff, _or_ my fancier and go with Intel's Nucs. But I decided to choose a different brand!

_Mmmm_

Honestly at the time I was working on this project AMD was on hype. Of course I would fell for such thing and that's why I made chioce with the Udoo BOLT V8 (link is [here](https://duckduckgo.com/?t=ffab&q=udoo+bolt+v8+&ia=web)). Brand is Bolt and it has nothing to do with audi v8 _haha._  I could go with intel who had bad PR or with AMD Ryzen. Bolt wasn't just any computer. It was the only SBC (single board computer) at the on market what used AMD processors with Vega graphics. I though that cool let's go with it. Nothing bad can happen.

Was it a good option? Yes and no. It's complicated.

Yes is that this processor has enough processing power that I had no problem running all my services so far I do host without any problem and it's not that power-hungry.

No is that AMD's graphic processors do have still some issues on Linux. So here you can Intel or Nvidia better choice. It's not perfect yet but I am sure AMD will issue these problems in near future. Another problem was with fan. There was firmware issue with fan that he was running either 0% or 100% of power. It was so annoying that this machine was for serveral second quiet and suddenly fan was running full power. Luckily company made a patch a BIOS update. And after this update everything was fine. I recommend to anyone be carefull with what you choose because it can be a board without future support with broken firmware can be useless.  Luckily for me thing went fine.

![UDM Pro and server](images/image01.jpg)
<!-- <span class="img-caption" id="{{ $id }}">{{ $caption }}</span> -->

Bolt as a single server machine was fine choice I think after a 1 of usage. But today I would go probably rather with server HW that would support IPMI or iLO or any similar technology.

## Issue 2: Storage

Another issue I had was to deal with the storage. This was a problem from begging of my homelab. I always I had an issue with full storage. One disk wasn't enough, after I bought second one it full really fast. And then I did not have enough USB, SATA III ports on my machies.I knew I needed more disks in my lab for more data, but I didn't want a NAS. What choice I did then?

[DAS](https://en.wikipedia.org/wiki/Direct-attached_storage) (which stands for _direct-attached storage_). DAS is a much more simple technology than NAS. Very similar to NAS but with much fewer features. It's basically just a case for disks attached that could be attached to computer, NAS or to a server. No disk encryption but it supprots HW raid or mainly JBOD.

And this was what I exactly wanted. I wanted an _option_ to add more disk (at least 4)  for my lab to expand the storage. And NAS with custom OS seemed overkill to me.

So instead NAS, I use SATA and USB-C and JBOD. Disks are connect to QNAP via SATA. Then whole DAS is connect via USB-C to a server and finally servers sees all disks via JBOD (just a bunch of disks) technology.

#### So DAS but what OS and raid?

For raid, I went with a software solution even though QNAP DAS has support for hardware RAID. My choice was the `btrfs`. I could go with `ZFS` but that's primary for enterprise ( you need 1GB memory for 1TB of storage) but also you can't add to the existing pool another disk which was a deal-breaker for me.

`Btrfs` is the solution for Linux, you can add more disks later. However btrfs still has issues and linux community doesn't find btrfs very stable. There are issues with raid 5,6 are not recommended to use. In short, using RAID 6 gives you more storage to use. I found project callend`Stratis` but this one seems still young technology I culd not find large community. Let's hope that `Bcachefs` will be the linux FOSS future!

#### Custom NAS

In my opinion, most NAS solution these days sucks. They have complicated UI. But they work. So if you want a magic box that is plugged and working goes with NAS. Or you can create one and customize it based on your needs. All you need is some SBC like rock64 (it has 1gbps ethernet port and 4GB ram) and direct storage like QNAP DAS. Connect them via USB, install Linux, then btrfs, and finally NFS and voilà you have NAS.

This way I can have a software-controlled raid (which is better than hardware controlled one).  Raid is a great thing but remember it's not a backup!

{{< figure src="__GHOST_URL__/content/images/2021/07/IMG_43DDFC241A9D-1.jpeg" caption="QNAP DAS" >}}

## Issue 3: Network

You need a network e.g. WIFI router to connect to the Internet for your server and for your home network. And that's it.

{{< figure src="__GHOST_URL__/content/images/2021/07/rack1.webp" caption="Work in progress" >}}

_However_, you can go fancy like me and go with UDMP (Unifi dream machine pro). It is a network tool that is a switch, router, security gateway, and cloud key in one machine. Cool right!

{{< figure src="__GHOST_URL__/content/images/2021/07/finished-rack.webp" caption="Finally work is done." >}}

There are _soo_ many things you can do with this machine! For me using this machine was such a great boost. I can set up VLANs networks, I can control bandwidth for every AP, I can set up firewall rules, I can use DPI to control what network users are doing, I can block p2p, I can setup IPS to control security, I can set up honeypot in case someone does something funny.

There is so much I can do with this machine and it's not that expensive for what it does. Give it try. I thought this was a perfect machine until something bad happened.

{{< figure src="__GHOST_URL__/content/images/2021/07/unifi-1.webp" caption="Thanks to DPI one can easily manage the network." >}}

In December 2020, company Ubiquiti was compromised thanks to one employee that who didn't set up 2FA for his AWS s3 cloud account. The whole s3 bucket, which allegedly contained all privates keys and more secure sensitive linked to every running UDM pro in the world, was _compromised_.

---

EDIT: 2021 DecemberThe culprit behind this leak was caught. And to my surprise, it wasn't any hacking group or AWS account without 2FA. It seems that data were stolen by an internal employee on the high post who had access to everything. The funny thing is he was caught using a commercial VPN paid with his personal credit card. Since Ubiquiti didn't make any statement about the leak, I hope that the reason for this is they had a suspicion of who could be behind this leak. Let's hope that this will not happen again.

## Bonus

I was not satisfied with the setup how it is. So I decided to upgrade my gear. It never ends! The main reason for this upgrade was that my network infrastructure was getting bigger. I had to add more AP for better signal distribution. Unfortunately, I've run out of ethernet slots on my UDM Pro so I had to add a new switch to my home lab setup. I decide to add Switch 16 PoE (https://store.ui.com/collections/unifi-network-switching/products/usw-16-poe). This switch has 8 ports supporting 802.3at PoE+ standard that I needed to power supply my new 6 APs. Thanks to this I was able to save space on power supply sockets. Also, I made more space in my rack since I could remove Unifi Poe adapters that were suppling previously power to my APs.

{{< figure src="__GHOST_URL__/content/images/2022/01/IMG_2911.jpeg" caption="New fresh look" >}}

Now about APs. As mentioned before, they support the new WIFI standard called "WIFI 6". However, don't mistake these with ones supporting 6Ghz. These AP's don't support this bandwidth, unfortunately. However, antennas in these AP are much more powerful. They have more transmit power. Data throughput is much higher than in previous generations. The Access Point WiFi 6 Long-Range has over 5 GHz band 2.4 Gbps through (4x4 MU-MIMO and OFDMA). And Access Point WiFi 6 Lite _only_ 1.2 Gbps (2x2 MU-MIMO and OFDMA). So both are more than enough for my network that supports a maximum speed 1Gbps. I plan a 10Gbps upgrade in the future. But right now 1Gbps is enough speed for my network. Also, the price for 10G switches is really high. And even if would have a 10Gbps switch I would still need next-generation APs that support such a speed. The WiFi 6E standard will finally support the 6Ghz band. First Access Points are coming out in 2022.
