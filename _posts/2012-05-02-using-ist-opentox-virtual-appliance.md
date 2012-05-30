---
layout: post
title: "IST Opentox Virtual Appliance"
description: "**The IST Opentox Virtual Appliance is a tool that facilitates using IST's developments.**"
category: "General"
tags: [VirtualBox, Windows]
---

{% include JB/setup %}

**The IST Opentox Virtual Appliance is a tool that facilitates using IST's developments.**

Bringing a native version of IST's server components to every relevant computer platform is not feasible. 
IST's efforts concentrate on Linux, which is versatile and suited for both, Desktops and servers. 
For other platforms (such as Mac OS and Windows), consider using a virtual machine (VM). 
Besides bringing the code to different platforms, VMs have features such as adjustable (virtual) hardware, snapshots ("frozen" states of the system that can be recovered), or easy backup (by exporting the VM to a file). Moreover, the contents of a VM can be easily extracted to real (physical) hardware. IST uses VirtualBox for VM deployment, but other systems (such as VMWare) are easily supported. This is, however, not covered here.


<br> 
* * *
<br />

**This section describes how to set up ISTs server components on a host with graphical user interface.**


# 1. Installing Virtualbox

[Install VirtualBox](http://www.virtualbox.org/wiki/Downloads). The IST Opentox Virtual Appliance (referred to as _VA_) was built using VirtualBox (version 4.1.16). It is recommended to also use it to run the _VA_.


# 2. Downloading the VA

Download the _VA_ (you should have received a download link). The _VA_ is distributed as a single, large, compressed file.
Recommendation: use a [download manager](http://www.freedownloadmanager.org) to download such large files. Download managers can resume broken downloads at the point the download was interrupted.

  Extract the appliance. Download and install the [7zip](http://www.7-zip.org/download.html) program and unpack the downloaded file to a directory of your choice.


# 3. Running the VA

Import the extracted file at "File", "Import Appliance...". Click through the rest of the process. When imported, change the settings of the _VA_ and assign it the highest [number of CPUs](http://www.virtualbox.org/manual/ch03.html#settings-processor) and [amount of RAM](http://www.virtualbox.org/manual/ch03.html#settings-motherboard) you can afford. Computationally expensive parts of IST's algorithms are designed for parallel processing, thus the _VA_ will profit from several CPU cores.

[Take a snapshot](http://www.virtualbox.org/manual/ch01.html#idp14849456) of the initial state of the system, which allows you to easily revert to this state later on.

Click "Run" to run the Appliance. A window showing the Linux desktop will appear.


# 4. Using the VA

There are two ways for accessing the graphical user interface:

- Start the web browser on the Linux desktop inside the _VA_ and point it to `http://istva:8080/toxcreate/predict` (this is the default when starting the web browser).
- Start a web browser on (a computer on) the same network as the host the _VA_ is running on and point it to `http://hostname:8080/toxcreate/predict`, where `hostname` should be replaced by the name of the host the _VA_ is running on.

![IST GUI](/images/IST_Nestle_GUI.png)      

_Note_: Server components are immediately started upon system startup. Additionally, constant interval checks for responsiveness ensure availability of services and (re-)start them appropriately.



<br>
* * *
<br>


# Troubleshooting

* Do not rename the .ova file extracted from the archive before importing.

- _Restart:_ Use the link on the desktop to (re-)start the application in case of non-responding services. Wait until the script window has closed. Note that responses, especially single predictions, can take up to minutes, as descriptors need to be calculated, involving e.g. time-consuming 3D calculations of molecular structure.<br>

- _Shutdown:_ Click "System", "Shutdown" to terminate the machine. After shutdown, click "Run" again. _Warning_: Do not power-off the machine without proper shutdown.



<br>
* * *
<br>

**Techical section**

The procedures described below are intended for system administrators. Non-technical users can ignore the remainder of this page.

# Portforwarding and Logging in to the VA

By default, port `2222` and port `8080` on the host are forwarded to the _VA_ ports `22` (SSH) and `8080` (HTTP). To change portforwarding configuration, change settings under "Settings", "Network", "Port Forwarding" in the main VirtualBox GUI.

For SSH logins, and for running privileged commands, you need user credentials. Accounts activated by default on the _VA_ are:

|User|Password|
|:---|:-------|
|ist |ist|
|root|ist|

It is recommended to stay user `ist` and dynamically aquire privileges via prefixing privileged commands with `sudo`, i.e. not work as `root`.

You can login directly in the appliance (in the window that opens when clicking "Run"), or via SSH from the host machine (recommended). For the latter, enter in a terminal window on the host:

    ssh -p 2222 ist@localhost

When your host machine is running Windows, use Putty as SSH client (see section "Tips and Tricks" below). Of course, this will also work from other hosts on the same network as the host machine by exchanging `localhost` for the host machines name.


After login, start the server using

    otstart


Shutdown the server using

    otkill

Shutdown the IST Opentox Virtual Appliance by entering

    sudo shutdown -h now

To adjust the appliance to your keyboard and timezone preferences, when
logged in use

    sudo dpkg-reconfigure keyboard
    sudo dpkg-reconfigure tzdata


**Security Advice**: It is recommended to change passwords via

    passwd # change for user 'ist'
    sudo passwd # change for user 'root'

and/or disable forwarding of port `2222` before using the machine productively.



<br>
* * *
<br>

# Headless Mode

The following command (run from the host system's command line) starts the _VA_ invisibly, in the background.

    VBoxHeadless -startvm "Name Of VM" &

Similarly, the following command shuts down the _VA_:

    VBoxManage controlvm "Name of VM" acpipowerbutton &

If you are on Windows, omit the `&` at the end. Adjust the VM name (the name is displayed in the main VirtualBox GUI)

If, additionally, you want to spare the overhead of running a GUI within the VA, edit file `/etc/default/grub`, and change line that starts with `GRUB_CMDLINE_LINUX_DEFAULT`

to

    GRUB_CMDLINE_LINUX_DEFAULT="text"

then run

    sudo update-grub

which will switch the _VA_ to text mode after restart.

_Note:_ Switching to text mode removes the immediate startup of server components (triggered by loading the graphical user interface). When in text mode, server components start with a delay of up to five minutes.


<br>
* * *
<br>


**Tips and Tricks**

## Script for Windows users

The script will open VirtualBox, set portforwarding, start VM Headless and connect to it via ssh using Putty.
_Note_: VirtualBox needs at least main user privileges.

[Download _Putty_](http://portableapps.com/apps/internet/putty_portable), a Windows client for SSH.


Create a file `start_opentox.bat` file and copy the following code:

    @ECHO off
    ECHO Starting VirtualBox...
    START /MIN "" "c:/path/to/VirtualBox/VirtualBox"
    ECHO Please wait until VirtualBox window is opened.
    ping 0.0.0.0 -n 10 -w 10000 >NUL
    pause
    START "" "c:/path/to/VirtualBox/VBoxHeadless" -startvm "Name of VM"
    ECHO IST OpenTox VA is running headless... Please wait.
    ping 0.0.0.0 -n 20 -w 10000 > NUL
    ECHO Please use putty window to login.
    START /MAX "" "c:/path/to/putty.exe" -ssh ist@localhost 2222
    exit


In the code above, replace all `c:/path/to/` with the real path to VirtualBox and Putty, respectively, and adjust the VM name after `-startvm` (the name is displayed in the main VirtualBox GUI). Then save and close the file and run it.

## Guest Additions

[Guest additions](http://ubuntu-tutorials.com/2010/06/26/install-virtualbox-guest-additions-on-virtualbox-guests/)
will improve user experience with 

* automated mouse support (mouse not "trapped" in the _VA_ window anymore)
* text copying (can copy text from and to the _VA_ window)

_Note_: Guest additions are probably already installed on your _VA_.




