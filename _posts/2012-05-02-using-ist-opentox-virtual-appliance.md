---
layout: post
title: "IST Opentox Virtual Appliance"
description: "**The IST Opentox Virtual Appliance is a tool that facilitates using IST's developments.**"
category: "General"
tags: [VirtualBox, Windows]
---

{% include JB/setup %}

**The IST Opentox Virtual Appliance is a tool that facilitates using IST's developments.**

Bringing a native version of IST's server components to every relevant platform natively is not feasible. 
IST's efforts concentrate on Linux, which is versatile and suited for both, Desktops and servers. 
For other platforms (such as Mac OS and Windows), consider using a virtual machine (VM). 
Besides bringing the code to different platforms, VMs have features such as adjustable (virtual) hardware, snapshots ("frozen" states of the system that can be recovered), or easy backup (by exporting the VM to a file). Moreover, the contents of a VM can be easily extracted to real (physical) hardware. IST uses VirtualBox for VM deployment, but other systems (such as VMWare) are easily supported. This is, however, not covered here.


<br> 
* * *
<br />

**This section describes how to set up ISTs server components on a host with graphical user interface.**


# 1. Installing Virtualbox

[Install VirtualBox](http://www.virtualbox.org/wiki/Downloads). The IST Opentox Virtual Appliance (referred to as _VA_) was built using VirtualBox. It is recommended to also use it to run the _VA_.


# 2. Downloading the VA

Download the _VA_ (you should have received a download link already). The _VA_ is distributed as a single, large, compressed file.
Recommendation: use a [download manager](http://www.freedownloadmanager.org) to download such large files. Download managers can resume broken downloads at the point the download was interrupted.

  Extract the appliance. Download and install the [7zip](http://www.7-zip.org/download.html) program and unpack the downloaded file to a directory of your choice.


# 3. Running the VA

Import the extracted file at "File", "Import Appliance...". Click through the rest of the process. This will take some time. Then, change the settings of the imported VA and assign it the highest [number of CPUs](http://www.virtualbox.org/manual/ch03.html#settings-processor) and [amount of RAM](http://www.virtualbox.org/manual/ch03.html#settings-motherboard) you can afford.

[Take a snapshot](http://www.virtualbox.org/manual/ch01.html#idp14849456) of the initial state of the system, which allows you to easily revert to this state later on.

Click "Run" to run the Appliance. A window showing the Linux desktop will appear.

[![](http://www.maunz.de/wordpress/wp-content/uploads/2011/05/Desktop-300x209.png)](http://www.maunz.de/wordpress/wp-content/uploads/2011/05/Desktop.png)


# 4. Using the VA

Start the Firefox Web Browser on the Linux desktop, which will load the graphical user interface of IST services.


<br>
* * *
<br>

# Hints:
	
* Do not rename the .ova file extracted from the archive.

How to use the IST OpenTox Virtual Appliance in VirtualBox


# Troubleshooting

Do these steps in order, i.e. the latter only if the former do not resolve the problem.

- _Restart:_ Click `(Re)Start.sh` to start the application in case of non-responding services. Wait until the script window has closed.<br>
_Note_: Services are started automatically on startup. 

- _Shutdown:_ Click "System", "Shutdown" to terminate the machine. After shutdown, click "Run" again.<br>
_Warning_: Do not power-off the machine without proper shutdown.



<br>
* * *
<br>

**This section is more technical and gives advice on how to set up the _VA_, so that it can be reached from the outside, i.e. the local network that the host is in, and how to start it in "headless" mode.**

# Portforwarding

For SSH connections to the appliance and HTTP server connections, you
need to configure portforwarding.

    VBoxManage setextradata "IST Opentox Virtual Appliance" "VBoxInternal/Devices/pcnet/0/LUN#0/Config/ssh/HostPort" 2222
    VBoxManage setextradata "IST Opentox Virtual Appliance" "VBoxInternal/Devices/pcnet/0/LUN#0/Config/ssh/GuestPort" 22
    VBoxManage setextradata "IST Opentox Virtual Appliance" "VBoxInternal/Devices/pcnet/0/LUN#0/Config/ssh/Protocol" TCP
    VBoxManage setextradata "IST Opentox Virtual Appliance" "VBoxInternal/Devices/pcnet/0/LUN#0/Config/nginx/HostPort" 8000
    VBoxManage setextradata "IST Opentox Virtual Appliance" "VBoxInternal/Devices/pcnet/0/LUN#0/Config/nginx/GuestPort" 80
    VBoxManage setextradata "IST Opentox Virtual Appliance" "VBoxInternal/Devices/pcnet/0/LUN#0/Config/nginx/Protocol" TCP

Now port `2222` and port `8000` on the host are forwarded to the _VA_ ports 22 (SSH) and 80 (HTTP). This means machines on the local network the host is in can access the graphical user interface of IST services at `http://host:8000`.

*Note:* For a new portforwarding configuration, you have to restart the _VA_ one time for SSH and access to the graphical user interface of IST services to work.

<br>
* * *
<br>

# Logging in to the VA

_Caution_ Technical skills on Linux required.

For login via SSH, and for running privileged commands, you need user credentials. Accounts activated by default on the _VA_ are:

|User|Password|
|:---|:-------|
|ist |ist|
|root|ist|

It is recommended to change passwords via

    passwd # change for user 'ist'
    sudo passwd # change for user 'root'

Furthermore, it is recommended to stay user `ist` and dynamically aquire privileges via prefixing privileged commands with `sudo`.

You can login directly in the appliance (in the windows opened when clicking "Run" when not in headless mode), or via SSH from the host machine (recommended). For the latter, enter in a terminal window on the host:

    ssh -p 2222 ist@localhost

When your host machine is running Windows, use Putty (see section "Tips and Tricks" below). Of course, this will also work from other hosts on the same network as the host machine by exchanging `localhost` for the host machines name.


_Hint:_ Use SCP in the appliance to copy data from the appliance to the host and vice versa.


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


<br>
* * *
<br>

# Headless Mode

The following command (run from the host system's command line) starts the _VA_ invisibly, in the background.

    VBoxHeadless -startvm "IST Opentox Virtual Appliance" &

Similarly, the following command shuts down the _VA_:

    VBoxManage controlvm "IST Opentox Virtual Appliance" acpipowerbutton &

If you are on Windows, omit the `&` at the end.



<br>
* * *
<br>


**Tips and Tricks**

## Script for Windows users

The script will open VirtualBox, set portforwarding, start VM Headless and connect to it via ssh (putty).
_Note_: VirtualBox needs at least main user privileges.

[Download _Putty_](http://portableapps.com/apps/internet/putty_portable), a Windows client for SSH.


Create a file `start_opentox.bat` file and copy the following code:

    @ECHO off
    ECHO Starting VirtualBox...
    START /MIN "" "c:/path/to/VirtualBox/VirtualBox"
    ECHO Please wait until VirtualBox window is opened.
    ping 0.0.0.0 -n 10 -w 10000 >NUL
    pause
    ECHO Configuring Virtual Appliance (SSH and NGINX forwarding):
    "/path/to/VirtualBox/VBoxManage" setextradata "IST Opentox Virtual Appliance" "VBoxInternal/Devices/pcnet/0/LUN#0/Config/ssh/HostPort" 2222
    "/path/to/VirtualBox/VBoxManage" setextradata "IST Opentox Virtual Appliance" "VBoxInternal/Devices/pcnet/0/LUN#0/Config/ssh/GuestPort" 22
    "/path/to/VirtualBox/VBoxManage" setextradata "IST Opentox Virtual Appliance" "VBoxInternal/Devices/pcnet/0/LUN#0/Config/ssh/Protocol" TCP
    "/path/to/VirtualBox/VBoxManage" setextradata "IST Opentox Virtual Appliance" "VBoxInternal/Devices/pcnet/0/LUN#0/Config/nginx/HostPort" 8000
    "/path/to/VirtualBox/VBoxManage" setextradata "IST Opentox Virtual Appliance" "VBoxInternal/Devices/pcnet/0/LUN#0/Config/nginx/GuestPort" 80
    "/path/to/VirtualBox/VBoxManage" setextradata "IST Opentox Virtual Appliance" "VBoxInternal/Devices/pcnet/0/LUN#0/Config/nginx/Protocol" TCP
    ECHO Configuration finished.
    pause
    START "" "c:/path/to/VirtualBox/VBoxHeadless" -startvm "IST Opentox Virtual Appliance"
    ECHO IST OpenTox VA is running headless... Please wait.
    ping 0.0.0.0 -n 20 -w 10000 > NUL
    ECHO Please use putty window to login.
    START /MAX "" "c:/path/to/putty.exe" -ssh ist@localhost 2222
    exit


In the code above, replace all `c:/path/to/` with the real path to VirtualBox and Putty, respectively. Then save and close the file and run it.

## Guest Additions

[Guest additions](http://ubuntu-tutorials.com/2010/06/26/install-virtualbox-guest-additions-on-virtualbox-guests/)
will improve user experience with 

* automated mouse support (mouse not "trapped" in the _VA_ window anymore)
* text copying (can copy text from and to the _VA_ window)

_Note_: Guest additions are perhaps already installed on your VA.




