+++
date = 2021-11-10T17:04:00Z
description = ""
draft = false
slug = "simple-fix-for-macos-issue-with-nfs"
title = "Simple fix for macOS issue with NFS"

+++


This is going to be a super short post however this could save someone an hour. If you are like me and you have a problem connecting on mac to a local NFS server there is a simple solution for that.

All you need to do is edit file `/etc/nfs.conf` and enter this text in there using your favorite editor:

```bash
#/etc/nfs.conf
# nfs.conf: the NFS configuration file
nfs.client.mount.options = vers=4
```

In case this solution doesn't work make sure that there is not any other problem with your setup (RPC, firewall, correct IP, etc)

Solving this issue helped me application called Console.app - Super useful when the GUI app does not return anything useful which makes solving the problem not easier. Definitely check it out! And also try to connect to the NFS server using the terminal. At least you will have in case of the error immediately output.
