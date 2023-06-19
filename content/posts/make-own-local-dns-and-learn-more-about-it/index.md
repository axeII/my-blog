---
# categories: ["tech", "pinned", "sys admin"]
date: 2019-08-05T21:19:53Z
description: ""
draft: false
# image: "__GHOST_URL__/content/images/2019/08/k9i75izpuq531.jpg"
slug: "make-own-local-dns-and-learn-more-about-it"
tags: ["tech", "sys admin"]
title: "Make own local DNS and learn more about it"
---


This morning I had a great idea to make my own DNS server. So I started googling and ducking more about the DNS. How does it really work? Working on this took some valuable time and the results were not exactly what I was expecting. That's why I am making this post so it can help others who would like to go on the same adventure I did. And honestly, there are few things I would like to share with you.

## Should one really do this?

The answer is probably not.

My initial idea doing this was I could gain more privacy. But now I think rather than doing this you could gain more privacy by using VPN and even using that technology you will not succed achiving 100% privacy. Maybe using tor browser  but that is another topic. The main reason I did it was to gain more knowledge about networking and about the DNS. Now read on and you'll see results at the end of the article.You also need a Linux machine to run the service on the local network. And also _free_ time since this could take you some time especially if it's your first time working with similar technologies.Before we begin with all the _voodoo_ stuff there are some facts you need to know. You need to know what kind of DNS you would like to host yourself. There basically two types:

* Authorative - answers for a specific domain
* Resolvers - fetch answers from authorative servers on behalf of your clients

In case you would like to host your own site and have your own resolvers then go for authoritative DNS and prepare VPS plus domain for that. You'll need glue records. Here is a good tutorial on how to do [that](https://zwischenzugs.com/2018/01/26/how-and-why-i-run-my-own-dns-servers/).

In my case, I wanted to setup a DNS **resolver**. And there are few steps how to do so.

First, you need to decide which service  would you like to use. The famouse are:

* dnsmasq
* bind
* unbound
* coredns

I went with the `bind`. I'll might try one of these later and measure speed difference in the future.


## Theory

There are two main choice how to do this. One is iterative and second is recursive. These two methods are used for resolving domain names into IP addresses. Iterative DNS involves a step-by-step process where a DNS resolver, such as a client's computer or a DNS server, queries other DNS servers for the complete resolution of a domain name. In this approach, the DNS resolver receives partial information from each server and continues to make subsequent requests until it obtains the final IP address. On the other hand, recursive DNS takes a more automated approach. When a DNS resolver receives a query for a domain name, it directly contacts other DNS servers and handles all the subsequent requests and responses itself until it obtains the final IP address. [Recursive](https://www.slashroot.in/difference-between-iterative-and-recursive-dns-query) DNS simplifies the process for the DNS resolver, as it doesn't have to manage multiple requests and responses manually.

## Installation

Now for the installation. The first step is to install package `bind9`.

```bash
sudo apt update
sudo apt install bind9 bind9utils bind9-doc bind9-host
```

Next, you need to configure bind (It should work from the box but you will need to add few things). Go to directory `/etc/bind/`. Here you should find storage files and also configs. You need to edit the file named `named.conf` (btw **named** is bind daemon). Configuration for this file should look like this:

```bash
include "/etc/bind/named.conf.local";
include "/etc/bind/named.conf.default-zones";

// Set up our personal DNS resolver
options {
  directory "/var/cache/bind";
  dnssec-validation auto;
  auth-nxdomain no;

  // Either replace 'any' with a list
  //of IPs you allow requests from, or add firewall rules
  // change the rules here for allowed ips exspecially
  //if you are planning to open your dns server to public.
  allow-recursion { any; };
  allow-query-cache { any; };
  allow-query { any; };

  // In case you can't use port 53 you can set different one
  // 127.0.0.1 must be set and also set your local ip adress
  //range to for access
  listen-on port 5353 {
    127.0.0.1; 192.168.0.0/24;
  };
};
```

And add this bottom line to the config file `named.conf.options`

```bash
    dnssec-validation auto;

    auth-nxdomain no;    # conform to RFC1035
    listen-on-v6 { any; };

    // optional - BIND default behavior is recursion
    recursion yes;

    // provide recursion service to trusted clients only
    // again you local ip range might be different
    allow-recursion { 127.0.0.1; 192.168.0.0/24; };

    // enable the query log
    querylog yes;
```

Don't forget to check all the time you config files via command `sudo named-checkconf`. Next, you should add rules for the firewall to allow new ports - in my case it was port 5353.

```bash
ufw allow 5353
```

And fire up bind.

```bash
sudo systemctl enable bind9
sudo systemctl start bind9
```

And after you set on your machine DNS resolver Linux machine you can happily use your own DNS.

BUT.

![meme1](images/image01.jpg)


Yeah, your home DNS is slow (OMG!).

I did some testing and tool `dig` was giving me almost **1023 ms** speed for querying DNS records! That is _so_  _much_ slower than public DNS resolvers like google or Cloudflare! But why is it so slow? Well the culprit seems to be the parameter `recursion yes;`.

<blockquote> <cite>When you're having your DNS server act as a resolver and force it to do all its recursion on its own, you'll likely always have higher latency in comparison to a large DNS provider like OpenDNS or Google. They likely have their servers hosted on better links with lower latency, so when those servers need to do a full recursive query it'll likely be faster than your server can do it.</cite></blockquote>

Ok, we should try to fix that. Speed up the whole DNS or whole goddamn thing is for _nothing_. What we need to do now is to speed things little bit by setting up [forwarding](https://www.digitalocean.com/community/tutorials/how-to-configure-bind-as-a-caching-or-forwarding-dns-server-on-ubuntu-14-04)

. Here is what I mean:

<blockquote><cite> A forwarding DNS server offers the same advantage of maintaining a cache to improve DNS resolution times for clients. However, it actually does none of the recursive querying itself. Instead, it forwards all requests to an outside resolving server and then caches the results to use for later queries.</cite> </blockquote>

So yeah in my case I updated the config `named.conf.options` with parameter:

```bash
forwarders {
    1.1.1.1;
};
```

Yeah, I did choose Cloudflare due to their godspeed but you can use Google or OpenDNS here (or any other provider).

NOW time for the **results**. When using directly only 1.1.1.1 as my DNS resolver.

```bash
; <<>> DiG 9.10.6 <<>> duckduckgo.com
;; Query time: 39 msec
;; SERVER: 1.1.1.1#53(1.1.1.1)
;; WHEN: Mon Aug 05 23:00:10 CEST 2019
;; MSG SIZE  rcvd: 91
```

Ok, that nice let's try it with bind service:

```bash
; <<>> DiG 9.10.6 <<>> duckduckgo.com
;; Query time: 59 msec
;; SERVER: 192.168.88.100#53(192.168.88.100)
;; WHEN: Mon Aug 05 23:01:24 CEST 2019
;; MSG SIZE  rcvd: 272
```

Oh yeah well it's _slower_ but we should have much better speed if we repeat the query and see much better results thanks to caching. Here is the result with caching with bind:

```bash
; <<>> DiG 9.10.6 <<>> duckduckgo.com
;; Query time: 53 msec
;; SERVER: 192.168.88.100#53(192.168.88.100)
;; WHEN: Mon Aug 05 23:02:45 CEST 2019
;; MSG SIZE  rcvd: 91
```

![meme2](images/image02.jpg)

Oh well.  Results are that it's 39ms without bind and 53ms with it.  Seems like your home server which could be rasberry pi is not better than multi milion dollar datacenter (shoking I know) but still it was fun to play with these tools.

## Final thoughts

This fun eventhough I can't think of this huge success. My home server was still slower in the end that public provider like Google or Cloudflare.  Honestly I was dissapointed with the results but in the end I learned something new.
