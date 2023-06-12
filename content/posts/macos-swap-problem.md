+++
categories = ["tech", "multimedia"]
date = 2021-01-18T01:01:23Z
description = ""
draft = false
slug = "macos-swap-problem"
tags = ["tech", "multimedia"]
title = "macOS swap problem"

+++


If you are owner of MacBook then you will know how much does macOS system swap. It's actually not that big dial these days since there is supper [fast](https://eclecticlight.co/2020/12/12/how-fast-is-the-ssd-inside-an-m1-mac/) SSD's that mac are equipted with. Now that may seemed like if there is fast disk then it's ok.

Well to tell the truth it does depend which MacBook do you own. Which CPU does you Mac have, how much memmory, also does it have HDD or SSD?

On older models I can confirm that swaping have huge inpact on system perfomance. However if you use newer model expecialy pro model, you there is a less inpact on system performace which you may not notice and you don't have to restart every week (_lucky you_).

---

_Note:_  _I can't confirm if M1 MacBooks also work same, however versions with intel process do work like this._

---

It doesn't really matter how much RAM does you mac have (of course more is better and it has inpact on system), macOS is designed this way to.... well swap a lot. Other systems (like linux) don't seemed to swap this lot.

Here is how does it work:

1. System is booted with clean memory
2. User operates with laptop using memory swap in minial
3. Macbook is set to sleep mode by user, system creates file in filesystem `/var/vm/sleepimage`. Size of this file equal size of RAM (unless SSD is on low capacity).
4. System stores contents of RAM __ this file. Note that this file is not delete when system is back on.

Well you can delete this file, but once you "delete it", the space will not be freed until the kernel and other processes close the file, so restart your Mac. The image file will be recreated next time you need to sleep - so if you had a HDD with that file contiguously allocated, you will make things worse when it's allocated non-contiguous blocks. SSD won't care either way.

Also there are two problems with this why you don't want this feature.

1. You loose some size on disk. (As for me system still stores huge file in filesystem.
2. Since you write 8gb size of data on your SSD every time you put you macbook into sleep mode, it has some inpact on SSD lifespan.

If you run `sudo pmset -a hibernatemode 0; sudo rm /var/vm/sleepimage`, the Mac will use a normal sleep mode (like desktop Macs by default) and it won't recreate `/var/vm/sleepimage`

>hibernatemode = 0 (binary 0000) by default on supported desktops. The system will not back memory up to persistent storage. The system must wake from the contents of memory; the system will lose context on power loss. This is, historically, plain old sleep.

Meaning that 0 (traditional sleep mode) enables fast wake up and sleep, saves disk space, and reduces writing to the drive.

I also didn't see any noticable impact on my macbook's battery. This can save you ssd lifespan and since you can't swap SSD on newer MacBooks, it does also save your whole MacBook life span.

Oh and I almost forgot - please don't remove the swap files ?.

---

_Note: Testing was made on macbook air 2017 8GB RAM_ Sources: "[https://apple.stackexchange.com/questions/50235/what-i-can-delete-from-private-var-vm/115748](https://apple.stackexchange.com/questions/50235/what-i-can-delete-from-private-var-vm/115748)"
