---
layout: post
title: "Using IST Opentox Virtual Appliance under Windows (or other platforms)"
description: "**The ****IST ****Opentox Virtual Appliance is a tool that facilitates using IST's developments under Windows (applies also to other platforms).**"
category: "General"
tags: [VirtualBox, Windows]
---

{% include JB/setup %}

**The IST Opentox Virtual Appliance is a tool that facilitates using IST's developments under Windows (applies also to other platforms).**

Since a native Windows version of IST's software is not deemed feasible, an alternative is to use a virtual machine that can be played on Windows and other platforms.


# 1) Installing Virtualbox


The IST Opentox Virtual Appliance (VA) was built using VirtualBox. It is recommended to use it also to play the VA.  [Installing VirtualBox](http://www.virtualbox.org/wiki/Downloads) is a simple setup process. There exist also other virtualization solutions, such as[ VMWare Player](http://www.vmware.com/products/player/).


# 2) Starting the Appliance


[**Download** the VA](http://www.maunz.de/opentox/Opentox IST Virtual Appliance.ova) (important: use a [download manager](https://secure.wikimedia.org/wikipedia/en/wiki/Download_manager), such as _DownThemAll_), then check the integrity of the archive by [calculating its MD5 sum](http://support.microsoft.com/kb/841290). It should equal 'ef360fed4524a956b701eecd74f4331e', otherwise your download has failed.

**Import** the file at "File", "Import Appliance...". Click through the rest of the process.

**Run:** Click "Run" to run the Appliance. A window showing the Linux system will appear.

[![](http://www.maunz.de/wordpress/wp-content/uploads/2011/05/Desktop-300x209.png)](http://www.maunz.de/wordpress/wp-content/uploads/2011/05/Desktop.png)

**Restart:** Click (Re)Start.sh to start the application in case of non-responding services.
_Note_: Services are started automatically on startup.

**Shutdown:** Click "System", "Shutdown" to terminate the machine.
_Warning_: Do not power-off the machine without proper shutdown.


## 3) Using the Appliance


**Start Browser:** Start the Firefox Web Browser. The familiar ToxCreate GUI will appear. Try creating a model by uploading the hamster dataset from the Desktop.



* * *



**Hints:**



	
  * Visit the technical [HOWTO](https://github.com/helma/opentox-documentation/wiki/HOWTO ) website for tips and tweaks to improve your user experience. In particular, consider enabling port forwarding for HTTP, allowing you to access the VA from outside.

	
  * __If your mouse and keyboard are "trapped" inside the appliance window, try hitting the right Ctrl key.

	
  * Do not rename the .ova file.
