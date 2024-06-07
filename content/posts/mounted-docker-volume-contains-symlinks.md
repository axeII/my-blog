---
date: 2021-02-18T23:21:02Z
description: ""
draft: false
slug: "mounted-docker-volume-contains-symlinks"
tags: ["Software devlopment", "devops", "sys admin"]
title: "Mounted docker volume contains symlinks"

showHero: false
---


This is quick tip that happened to me this afternoon. If you mount volume on docker (which is always brings some complications) than you may need to check mounted volume for symlinks.

## Problem

Now since you first mount volume to cotainer and this volume (directory) contains symlinks. Symlinks are a big challenge inside docker. Container will not have access to those files and will no be able to load to your conatiner. Inside you container this files will look like broken files or missing.

It's simple problem with simple solution even this can be troblesome if you don't know or except this docker behaviour.

## Solution

You can simply fix that by mounting also that volume which symlinks are linking to. Mount volume to docker conainer as you do plus mount addiotional directory where are symlinks pointing eg:

Example:

`docker run -v /data/:/data/ -v /mnt/mounted:/mnt/mounted helloword`

By that conatiner will have acces those files again and your which may need those files, will finally work again.

Fixed!
