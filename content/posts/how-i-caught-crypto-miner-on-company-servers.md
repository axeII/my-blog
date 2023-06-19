---
date: 2021-12-29T01:52:45Z
description: ""
draft: false
slug: "how-i-caught-crypto-miner-on-company-servers"
title: "How I caught crypto miner on company servers"
---


Long story short, one afternoon I was performing maintenance on a Virtual machine. My task was to check if there were any important processes running. This particular VM had an NVIDIA GPU initialized, and we wanted to transfer this GPU to another VM.

As I was about to power off the machine, but I found myself in the middle of a discussion with my colleague. While I had been monitoring the VM prior to shutting it down, suddenly, in the middle of our conversation, my colleague noticed a specific process which should not have been there. _"Hey, what's that?"_ asked. At first, I didn't notice anything unusual. So I opened another bigger window and tried to find this process again. Found it and we knew it's bad.

```
./validator -P stratum1+tcp://0xf47A58ad640ecAEA78D89C5eA8E5f7C51IJCe.laptop@eu1.ethermine.org:4444 &>/dev/null
#&>/dev/null
```

The word "validator" seemed normal since it's our custom tool our ML developers were using. But it was obvoise that the rest the command was malitious. And when I made my terminal screen bigger and saw the whole process name - I knew what's going _on_.

After I've reported this to my boss, I was told to inspect if it was inside or if we have been hacked then kill it and nuke the whole VM and report back. So the case had begun.

The first thing I did was kill all ssh logged users. The next thing I backed up `authorized_keys` file for the following investigation and block everyone except me and boss via firewall rule. The next thing I did was just to be sure I restarted the `sshd` service. My mistake was here that I did not save the whole process info because after I restarted the service - the mining process was gone.

_Dammit_.

OK, that sucks what to do next? I used the find command for a file named "validator" since there shouldn't be that many files with the same name. I was right. I had found 7 files with the same name in different directories. However, one directory felt strange and it was /etc/test/validator. Hmm, strange. Let's check it out. Quick `cd` and I found this:

```
base) root@core:/etc/test# ll
total 8164
drwxr-xr-x   2 root root    4096 Oct  1 20:20 ./
drwxr-xr-x 122 root root   12288 Nov 22 17:39 ../
-rwxrwxrwx   1 root root     129 Oct  1 20:20 start.sh*
-rwxr-xr-x   1 root root 8339264 Oct  1 20:14 validator*
```

Bingo! This must be it! Let's cat `start.sh` which was one line script for executing the process same I saw in `htop`. Great I have the file, script. Now is the time to find the culprit. Let's take a quick check on "validator's" file.

> [Virus total analysis](https://www.virustotal.com/gui/file/7034cd646d9c7d2f2add1bdc41b4c12085f526c8876d82e2b1d0965332c8fd45?nocache=1)


Luckily it was just a crypto miner and not some ransomware or anything dangerous. Now the problem was that since this was VM user was root. There was no chance to find the culprit base on the author. However, I had a file and knew when it was created based on Linux time. So via the last command. I listed the history of all logged users via ssh, compare the time the file was created with the day of logged users. I had them. Luckily there was no hacker or any outside data leak. This malicious program was installed by someone inside.

So far so good found the file, the culprit, killed the process. It's nothing dangerous but I need to be sure it's just this VM. So the next thing I did was use the file and start to look for the same file on other VMs with GPU. Luckily no other VM had the same file with this hash.

OK. Added to report. _Is that all?_ I asked myself. I did some digging on `validator` file which was just crypto mining tool:

```
ethminer 0.19.0-17+commit.ce52c740
Build: linux/release/gnu
```

Nothing fancy here. I also tried to decode the hash but had no luck with it.

## Conclusion

If this would happen again what would I do differently? Well, the first thing I would do the live snapshot of the whole VM wherein case the culprit would have a self-deleting process to clear proof I would still have proof - the whole VM.

So yeah that's it. If you are reading this blog post and think I did something wrong or there is something I could have done better - please let me know in comment section. Thanks for reading!
