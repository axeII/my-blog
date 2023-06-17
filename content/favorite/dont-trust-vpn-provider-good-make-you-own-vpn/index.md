---
# categories: ["pinned", "sys admin", "devops"]
date: 2020-02-29T14:22:00Z
description: ""
draft: false
# image: "__GHOST_URL__/content/images/2021/07/security.webp"
slug: "dont-trust-vpn-provider-good-make-you-own-vpn"
tags: ["sys admin", "devops"]
title: "You don't trust VPN providers? Setup your own VPN on cloud using Ansible."
---


## Introduction

As of today, there are thousand or even more VPN providers. These times were we need secure connection when we are connecting from unreliable places like hotel wifi - VPN comes in hand. However there may be many VPN providers but you would be surprised how many have same owner. For example company named Kape Technologies has been purchasing other VPN providers.

We can separate all these providers based on features and price, but there is not that much difference.

*  So which provider is the best?
*  Which one really doesn't log anything?
*  Which provider is not banned in my country?

And here goes the ansfer. Don't trust VPN providers be your own VPN provider. Yes make your own VPN. Share it with family or friends. And actually it's not that hard to do.

But first.

## Do I need to use a VPN?

Before we go through making our own VPN, I would like to discuss this question. It's very _important_ and you should ask yourself.

_Do I need VPN?_

Please think about it. It's possible that in the end, you don't really need VPN at all. Security or privacy, all of that can be achieved _without_ VPN. Very common mistake is that VPN does protect you from malware which it doesn't. Or phishing attack. Or social engineering. Which are very common cyber attacks on most internet users. Also, I see all the time people using VPN for geolocation issues like some content is blocked in their country like Netflix shows. For this problem, a simple proxy could be an easy solution (like a SOCK5 proxy).

On the other hand, most of us do need VPN to access our workspace (thanks to COVID these days) which is absolutely correct. This is a use case where using VPN is not just ok but also needed.

{{< alert >}}
**Note:** I strongly recommend to read [this](https://gist.github.com/joepie91/5a9909939e6ce7d09e29) guide about VPN.
{{< /alert >}}

Anyway back to my previous question. Why should one use VPN unless it is due to work?  Without spending hours and hours to discuss if yes or not, here is a very good gist page with FAQ for this specific topic. I do recommend to through with it since it was helpful for me.


## Solution: Algo personal VPN in the cloud

Anyway, let's set up our own VPN service. When I was looking for custom solutions I was sure I want to set up a VPN service on the cloud. Because of that, I had found the perfect tool for this -- Algo. What also really is I do recommend reading this blog [post](https://blog.trailofbits.com/2016/12/12/meet-algo-the-vpn-that-works/). Anyway, in short, Algo has many advantages over other services like:

* encryption since algo supports only IKEv2
* also supports modern vpn protocol wireguard
* multiplaform (iOS, windows, linux, macos, Android)
* easy to deploy on cloud

Algo is written in python and it's available on Github (link below).

{{< github repo="trailofbits/algo" >}}

You need only a few things to run this service:

* VPS (cloud serice like aws or gcp)
* install algo on local machine (to deploy on cloud)
* free time, but it's very easy to deploy (it's task for ~25mins)

### Step 1: Setup cloud service

There are many cloud providers, for example, Google, Amazon, or Microsoft. All of them have different services and different pricing. Pick your poison. You can find many tutorials on how to set up an environment on different cloud services https://github.com/trailofbits/algo/tree/master/docs.

In my case, I will use GCP as a cloud provider. Now you can set up the environment via the website application on https://console.cloud.google.com or via a CLI terminal tool. Google has a great tool for this called `glcloud`. Using this tool is not that hard and makes things a little easier.

Now first thing is to set up the environment with `gcloud`. Replace `USER` with what named you to want to use for the user. `PROJECT_ID` is important to link to your project and at last `BILLING_ID` all these variables you can find in the google cloud profile.

```bash
PROJECT_ID=${USER}-algo-vpn
BILLING_ID="$(gcloud beta billing accounts list --format="value(ACCOUNT_ID)")"

gcloud projects create ${PROJECT_ID} --name algo-vpn --set-as-default
gcloud beta billing projects link ${PROJECT_ID} --billing-account ${BILLING_ID}

## Create an account that have access to the VPN
gcloud iam service-accounts create algo-vpn --display-name "Algo VPN"
gcloud iam service-accounts keys create configs/gce.json \
  --iam-account algo-vpn@${PROJECT_ID}.iam.gserviceaccount.com
gcloud projects add-iam-policy-binding ${PROJECT_ID} \
  --member serviceAccount:algo-vpn@${PROJECT_ID}.iam.gserviceaccount.com \
  --role roles/compute.admin
gcloud projects add-iam-policy-binding ${PROJECT_ID} \
  --member serviceAccount:algo-vpn@${PROJECT_ID}.iam.gserviceaccount.com \
  --role roles/iam.serviceAccountUser
```

Then we need to run:

```bash
gcloud services enable compute.googleapis.com
```

This should create a config file called `configs/gce.json` which is needed for algo deployment. We need this for the next part.

### 2.Deploy

Since we have the environment ready now we need to finally deploy algo. There are two ways how to install or deploy algo on GCP. One option is to follow the classic way which is `pip install requiremetns.txt` and `./algo`. But this option might more difficult than it looks. For example, in my case, I had a problem with installation this way.

Thankfully, there is a different way to deploy algo, and that is called Ansible. This tool, created by RedHat, is very helpful in the world of automation and deployment.

> [how ansible works](https://www.ansible.com/overview/how-ansible-works)

We need to install `ansible-playbook` tool for Ansible. Use your package manager for your OS. It shouldn't be a problem to install this. Before finally deploying to the cloud we can still use one more thing which can help us to make this deployment easier and that is a configuration for Ansible.

Set path to `gcloud` config JSON file.

```bash
GCE_CREDENTIALS_FILE_PATH=/path/to/config.json
```

And then simply deploy to Google GCP cloud. Remember to choose the best region for you. In my case it was `europe-west4`.

```bash
ansible-playbook main.yml -e "provider=gce
                                server_name=algo
                                ondemand_cellular=true
                                ondemand_wifi=true
                                ondemand_wifi_exclude=MyWifi
                                dns_adblocking=true
                                ssh_tunneling=false
                                store_pki=false
                                region=europe-west4"
```

When you use Ansible this way there is no need for any interaction. Unlike normally where you have to enter different variables during deployment. From now installation should either fail or be successful.

### 3.Configuring devices

Since our deployment was successful and we have running algo on GCP all we need now is to connect this service via a secure network tunnel. How? During installation and after deployment there were several files generated. You can find them under directory `deployment` and here you can file that is named like `your-deployed-sercice.ovpn`. This you can share between your device, eg: iOS, macOS. Installing profiles on your devices shouldn't be a problem, just be sure that the profile file is supported by your device. I also recommend using encrypted services like `scp` ideally at your home trusted environment or even better share them directly using tools like `airdrop`.

## Final thoughts

_Yup,_ so that's it! Pretty easy and simple. No logs are stored by default! You know the machine you are connecting to (also the region of a host which is set during deployment). A very simple, secure, and powerful tool you have now under control.✌
