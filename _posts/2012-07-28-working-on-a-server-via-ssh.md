---
layout: post
title: "Working on a server via SSH"
description: "Working on a server via SSH"
category: Development
tags: [Linux, Ubuntu, Debian, Terminal, SSH, Linux]
---
{% include JB/setup %}

Working on a remote machine is a common task, when, for example, data mining or
other long running jobs are put on a dedicated machine. Having some useful
tools at hand is important, because SSH connections often close unexpectedly,
for example on unstable WLAN connections.

<p></p>
---
<p></p>

Cool Shell Tools Configurations
===============================

Your shell should be configured such that it maximizes the amount of
information you can get from a text-based representation. This includes color
support and other sensible settings for the most important shell tools. A dedicated
repository offers an installer for some important shell tool's configuration
files.

    git checkout git://github.com/amaunz/home-bin
    cd home-bin
    ./_init.sh

This will copy configuration files for different shell tools to your `$HOME`
directory, including configuration for `bash`, `vim`, `irb`, `git`, `screen`,
`htop`, and more. These configurations are designed to maximize productivity,
customizing color support, syntax highlighting, command history, layout, among
others. Explaining all options is beyond this document's scope. See the
configuration files for comments documenting the options.

Note: Your current settings are *not* overwritten. If, for any configuration
file, a different configuration is already present, `_init.sh` opens a
`vimdiff` (side-by-side editing) session, such that you can push and pull
changes manually to your settings.

![home-bin](/images/homebin.png)

See [this tutorial](http://goo.gl/7SH1I) to learn how to work with vimdiff.


<p></p>
---
<p></p>


Using htop
==========

`htop` is an ncurses-based tool for monitoring CPU, RAM, load, and processes. 
See this [tutorial](http://goo.gl/bJlXD).


<p></p>
---
<p></p>


Using GNU screen
================

Do *not* open several SSH sessions to accomplish several tasks.  Any session
creates load, while the server could use it's CPU cycles and memory for other
jobs, such as running your applications. Use a single SSH session only. It is
less likely to be interrupted than many sessions at once.

Also use the "screen" utility. It even saves your work when your single SSH
session is interrupted. This is how a screen session can look like. 

![home-bin](/images/screen2.png)

It shows two regions (a larger and a smaller one) on a single window. The
status bar at the bottom includes hostname, windows list (currently 4), and 
CPU load (part of the cool shell tool configuration package).

**Start screen session:**

    screen -S "screentitle"

This puts you inside a session called "screentitle". Start only one session, then use as many windows as you want inside this screen session.

**Working with several windows inside screen:**

You can have several so-called windows inside a screen session, which you can imagine as tabs or sheets. You can switch between them. Every window inside a session is a full terminal. You can run different things on each window, just as you did previously with several SSH connections, but you need only one. If the single SSH connection fails, nothing is lost. Simply reconnect and re-attach to your screen session, then go to the right window.

- `Ctrl+a c`  Create new window
- `Ctrl+a k`  Kill the current window / session
- `Ctrl+a w`  List all windows
- `Ctrl+a 0-9`  Go to a window numbered 0 9, use `Ctrl+a w` to see number
- `Ctrl+a Ctrl+a` Toggle / switch between the current and previous window
- `Ctrl+a :fit` Fit screen size to new terminal size. You can also hit `Ctrl+a F` for the the same task
- `Ctrl+a D` Power detach and logout. *Think twice before doing this!*
- `Ctrl+a d`  Detach but keep shell window open. *This is safe!*
- `Ctrl-a ?`  Display help screen i.e. display a list of commands

You can also have so-called regions that split your screen window, so you can monitor different things at the same time.

- `Ctrl+a S`  Split terminal horizontally into regions and press `Ctrl+a` c to create new window there
- `Ctrl+a :resize`  Resize region
- `Ctrl+a tab`  Move to next region
- `Ctrl+a :remove`  Remove / delete region. You can also hit `Ctrl+a X` for the same taks

Scrolling

- `Ctrl+a esc`  Start scroll mode. Scroll up and down using the arrow or page up/down keys. Hit `esc` again to leave scoll mode.


**Leave screen session** (but keep it running in the background)**:**

Inside screen, press `Ctrl+a d`. This detaches the screen session and runs it in the background. The same happens if your connection is interrupted.


**Re-attach to screen session:**

    screen -ls  # show running session names as PID.screentitle
    screen -r PID.screentitle  # re-attach to sesssion

You should be back to your session named "screentitle".

![home-bin](/images/screen1.png)

Try it: open a screen session on a remote machine, then pull the network cable.


