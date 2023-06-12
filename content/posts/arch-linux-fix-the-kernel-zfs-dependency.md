---
date: 2022-11-08T16:13:40Z
description: ""
draft: false
slug: "arch-linux-fix-the-kernel-zfs-dependency"
title: "Arch Linux fix the kernel ZFS dependency"
---


As of November 2022 there are still issues with Arch Linux official support for zfs package. Unlike BTRFS which is part of kernel ZFS still has to be installed via packages.

## The problem: Kernel dependency

If you see error message like this when you want to update packages:

```bash
root@linux ~# pacman -Suyyy
:: Synchronizing package databases...
 core                  158.6 KiB  1823 KiB/s 00:00 [########################] 100%
 extra                1762.9 KiB  17.2 MiB/s 00:00 [########################] 100%
 community               7.2 MiB  22.5 MiB/s 00:00 [########################] 100%
:: Starting full system upgrade...
:: Replace at-spi2-atk with extra/at-spi2-core? [Y/n]
:: Replace atk with extra/at-spi2-core? [Y/n]
resolving dependencies...
looking for conflicting packages...
error: failed to prepare transaction (could not satisfy dependencies)
:: installing linux (6.0.7.arch1-1) breaks dependency 'linux=5.19.4.arch1-1' required by zfs-linux
```

This sucks since you are unable to update your system. And you definitely should update your system. So to achieve the latest version for all packages again for security reasons here is how to fix this problem:

## The solution

To fix this first you need to do:

```bash
pacman-key --recv-keys F75D9D76
pacman-key --finger F75D9D76
pacman-key --lsign-key F75D9D76
```

You can check here [https://wiki.archlinux.org/title/unofficial_user_repositories#archzfs](https://wiki.archlinux.org/title/unofficial_user_repositories#archzfs) from the official guide that this links to archzfs repository for arch linux.  You might have found on the Internet outdated guide notes that link to different url. Since repository was moved as noted here:

[https://github.com/archzfs/archzfs/issues/368](https://github.com/archzfs/archzfs/issues/368)

all you need to do is to add this inside `/etc/pacman.conf`:

```bash
...
[archzfs]
Server = https://mirror.biocrafting.net/archlinux/archzfs/$repo/x86_64

# dont use [zfs-kernels] or you will have issues with servers down

[zfs-linux]
Server = http://kernels.archzfs.com/$repo/
...
```

And 🎉  now you can simply update and fully patch your system!

```bash
pacman -Syyu
```


{{< alert >}}
**Remember** don't forget to **update** your system to make it difficult for the malicious attackers. There is older guide for zfs which can be still useful, it's just down so you can find it [here](http://web.archive.org/web/20200125062036/https://artnoi.com/blog/zfsarch.html).
{{< /alert >}}
