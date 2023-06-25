---
date :  2021-10-12T15:52:00Z
draft :  false
slug :  "use-touchid-for-sudo-in-terminal-instead-of-password"
title :  "Use Touch ID for sudo in terminal or tmux instead of password"
---


This is a super simple life-changing (for me) feature where you can use touchID instead of using a super long password in your terminal. Plus this is also useful when someone is next to you and you don't need to enter your super long password.

When I have to enter a password in front of someone the feeling that they can see my password makes me screw up writing the password on the first try and I have to enter it again.And if there is the option to use it so _why not_?

## Standart macOS terminal app

Here is a simple two-line shell script that sets Touch ID as a default option for authorization in the terminal when entering `sudo`:

```shell
#!/bin/bash

chmod +w /private/etc/pam.d/sudo
sed -i '' -e ' 1 s/.*/&\nauth\tsufficient\tpam_tid.so/' /private/etc/pam.d/sudo
# in case of security set pam.d readonly
chmod -w /private/etc/pam.d/sudo
```

## Tmux

For the tmux is little bit different what you need are two steps:

* first you need to install this project: [https://github.com/fabianishere/pam_reattach](https://github.com/fabianishere/pam_reattach) for installation I recommend to use brew:

```bash
brew install fabianishere/personal/pam_reattach
```

* second you need to activate this inside `/private/etc/pam.d/` you need to add the first line to link to the pam_reattach.so.

```bash
# sudo: auth account password sessios
auth     optional    /opt/homebrew/lib/pam/pam_reattach.so
auth       sufficient     pam_tid.so
auth       sufficient     pam_smartcard.so
auth       required       pam_opendirectory.so
account    required       pam_permit.so
password   required       pam_deny.so
session    required       pam_permit.so
```

Note that I use M1 so the path is different than for intel macs. For those there is no need to special path just use `pam_reattach.so`.
