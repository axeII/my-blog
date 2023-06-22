---
# categories: ["sys admin", "devops"]
date: 2021-05-16T17:26:48Z
description: ""
draft: false
slug: "how-run-firewalld-with-docker"
tags: ["sys admin", "devops"]
title: "Firewalld doesn't go well with Docker"
---

`Firewalld` is a great project. It's a tool to control iptables rules (or the firewall in short). So basically, it's just a frontend while `iptables` (or ntftables today) is the backend. And it was made by the Red Hat company, so it's an open-source project (cool, right?).

Well, while it's a popular tool today, it has some issues with Docker. So if you are like **me** and have activated `firewalld` on the same machine where you host containers using Docker, you might have the same issue as I had.


![meme1](images/image01.jpg)

## Problem

When I installed and activated `firewalld` I had a problem -- all Docker containers we re not able to connect internet. So any conatiner I tested - I wasn't able to ping `1.1.1.1`. I could access the local network but that was all. Later on when I googled if anyone has the same issue as I had - I found on github very large issue for this problem (and it's still open at this time). See it for [yourself](https://github.com/firewalld/firewalld/issues/461).

So yeah you running docker and `firewalld` and now all containers aren't able to connect to internet (in case you are building custom ones this can be great issue).

_So how to fix it?_


## Fix

To fix the problem one does need to run these commands on their hosts terminal:

```
# Masquerading allows for docker ingress and egress (this is the juicy bit)
firewall-cmd --zone=public --add-masquerade --permanent

# Reload firewall to apply permanent rules
firewall-cmd --reload
```

{{< alert >}}
**Important** You need to enable masquerading to make this work. It looked like `dockerd` already did this through `iptables`, but apparently this needs to be specifically enabled for the firewall zone for `iptables` masquerading to work.
{{< /alert >}}

You can also disable firewalld which does seemed to do the trick, but it's not prefered method. While inspecting network rules with `iptables`, By switching to `nftables`, means that iptables are now an abstraction layer that only shows a small part of the nftables rules. If not all of the firewalld configuration will be applied outside the scope of iptables.
