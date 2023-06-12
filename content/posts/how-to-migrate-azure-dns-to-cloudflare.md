+++
date = 2022-04-14T10:50:56Z
description = ""
draft = false
slug = "how-to-migrate-azure-dns-to-cloudflare"
title = "How to migrate from Azure DNS to Cloudflare"

+++


Recently, at work we decided that we should migrate our domains to Cloudflare. I really happy, about this choice since Clouflare has variety of cool features one can use. And there are many of those!

> As a DevOps engineer I like to manage domains on Cloudlfare using Terraform. It helps with automating creating dns records and gettings ssl certificates.

Anyway. Before migration we were using Azure DNS and for some reason I could not find any useful blog post were I could get simple and clear instructions how to do this. So here we are. Hopefully this post can save an hour of googling and testing.

## Step 1. Login to Azure CLI

Unfortunately Azure web site app doesn't have the option to generate BIND file which is accepted by Cloudlfare. Yes you can try to scan your domain when you are registering domain but you risk that some pages won't be scanned. And when you migrate nameserver - some services won't accessible. _Sucks right?_ On the web app I only found was some azure json file template format which Cloudlfare does not accept.

Instructions here are for macOS but if you are using different OS please follow this [guide](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli). Now to install via homebrew:

```bash
brew update \
brew install azure-cli
```

Then you need to login to your space via browser. If you are already logged this step should be very easy for you.

```bash
az login
```

Set correct subscription:

```bash
 az account set --subscription EXAMPLE
```

## Step 2. Generate the BIND file

To export a DNS zone you need to generate bind file. Use the following Azure CLI command:

```bash
az network dns zone export -g <resource group> -n <zone name> -f <zone file name>

az network dns zone export -g resourceGroupExample -n example.com -f example.com.txt
```

## Step 3. Insert generate file

Honestly this step is very easy. Once you have the generated file all you need to do is to is insert this file while you are creating new domain settings on the Cloudlfare.  When you are on the page just click on advanced setings on the right side and then you will see input window to insert your file.

And that's it!

## Final thoughs

Even tough we use mainly at work terraform to generate our resources, sometimes we need to use azure CLI (like this one). Using azure CLI tool can be very useful sometimes. For more info how to use it check out this [page](https://docs.microsoft.com/en-us/cli/azure/).One can even write his own scripts, but yeah for the usual stuff I recommend to use Terafform.
