---
layout: post
title: "Installing Ubuntu on Thinkpads"
description: ""
category: General
tags: [Linux, Thinkpad]
---
{% include JB/setup %}

<p></p>
---
<p></p>

Lenovo Thinkpads are well-suited for Linux. Although they work out-of-the-box, there are still many options that can be tuned.

# Installing **Ubuntu** on a System with Windows (Dual-Boot)

Normally, Windows (Vista or better) can not be resized to less than half the hard disk size. Here is how to do it:

1.  Shrink Windows partition
    1.  Start Windows.
    2.  Prepare drive `C:` for shrinking (so that it can be made smaller than 50% of the disk capacity)

        1.  [Set pagefile to
            zero](https://www.evernote.com/shard/s89/sh/62db75f8-c3b0-4b64-85aa-0b6915fa1dfd/4917c857af11acb623d704c01a413a88).
        2.  [Disable System
            snapshots](https://www.evernote.com/shard/s89/sh/055a042c-63fb-40d6-9818-6eac6c49de3f/0ba25202bc986966d23be4b66561dad8).
        3.  Reboot Windows and defrag drive `C:`.

    3.  Shrink `C:` using the Windows 7 tools (Control Panel, type `partition` in the search box) to a minimal size.
    4.  Revert pagefile and snapshots.
2.  Decide what version of Ubuntu to use: 32bit or 64bit. Advice: always use the latter if you have more than 2G of RAM. Otherwise choose the 32bit version. **Always use the latest version** of Ubuntu!
3.  Install **Ubuntu** on the freed space and reboot to Ubuntu.

# Miscalleneous Tweaks

1.  **Use less battery power** (also useful for desktops, e.g. for silent cooling). My Thinkpad now is in the deep sleep state (C7) about 97% of the time, and runs longer with Ubuntu than with Windows.

    1.  Check powersave potential on your device.

        1. Install **Powertop** (`sudo apt-get install powertop`).
        2. Run `sudo powertop`. Let powertop gather data for a while. Check sections **Device Stats** and **Tunables**. The former show you what hardware is using most power, the latter what you can do to tune your software.

    2.  [Install **TLP** (german)](http://thinkpad-wiki.org/TLP_-_Stromspareinstellungen_fuer_Ubuntu).
    3. With **TLP** installed, run **Powertop** again and compare. Google for any remaining recommendations under **Tunables**.

2.  For US keyboards (recommended): Run `sudo dpkg-reconfigure keyboard-configuration`, select 105 key generic / english-US layout with EUR sign on ‘5’ key. use AltGr as modifier for foreign symbols (default).

3.  Read [ubuntuguide.org](http://ubuntuguide.org) how to set up the
    system with your desired user applications.

