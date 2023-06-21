---
# categories: ["multimedia", "Software devlopment"]
date: 2020-12-06T01:00:15Z
description: ""
draft: false
slug: "apple-script-to-remove-all-cookies-from-safari"
tags: ["multimedia", "Software devlopment"]
title: "Apple script to remove all cookies from safari"
---

In the vast landscape of the internet, cookies hold a hidden power: they can track and identify users, raising concerns about privacy. Deleting cookies periodically is important for taking control over your online identity and protecting personal information. Let's take a look how to delete cookies from your browser which can give back your privacy.


## The script

Here is short apple script for deleting cookies. Just copy and paste this into `Script Editor.app`. Then save this as application. Named it e.g: `CleanCookies.app`. Move this app to `~/Application` or `/Applications`. After that all you need is to call this app from Spotlight. From there you can enjoy yoru web browser from tracking cookies.

```AppleScript
tell application "Safari"
    activate
    repeat until frontmost is true
    end repeat
    tell application "System Events" to tell process "Safari"
        tell application "System Events" to keystroke "," using command down
        delay 1.0E-3
        tell window 1
            click button "Privacy" of toolbar 1
            click button "Manage Website Data…" of group 1 of group 1
            delay 1
            tell sheet 1
                if exists row 1 of table 1 of scroll area 1 then
                    click button "Remove All"
                    tell application "System Events" to key code 36 #return
                end if
                click button "Done"
                click button 1 of window 1
            end tell
            close
        end tell

    end tell
end tell
```

{{< alert >}}
**Important**: For directory `/Applications` you need admin rights  thanks to SIP. This can be done **only** via gui.
{{< /alert >}}
