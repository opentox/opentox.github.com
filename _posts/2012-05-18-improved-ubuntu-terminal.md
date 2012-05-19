---
layout: post
title: "Improved Ubuntu Terminal"
description: "Some techniques to improve user experience on Ubuntu terminal sessions"
category: General
tags: [Linux, Ubuntu, Terminal]
---
{% include JB/setup %}

Working in terminal windows is common on Linux, which is different from Windows or Mac OS, where most applications are graphically oriented. There are many tricks to improve user experience on Ubuntu terminal sessions, although recent versions are already quite well configured. A recent Ubuntu version is needed, and **bash** is assumed to be the shell. 

<p></p>
---
<p></p>

# Linux Literature

Working on a terminal means working close to the "heart" of Linux. Also, the tools on the console are paradigmatic for the philosophy of Linux. Therefore, it pays to invest in understanding the latter. Here are some good, free readings.

- [Grundlagen für Informatiker (german)](http://openbook.galileocomputing.de/it_handbuch/)

- [Komplette Einführung in Linux (german)](http://openbook.galileocomputing.de/linux/)

- [Introduction to the **GNU Core-Utils**](http://www.gnu.org/s/coreutils/manual/coreutils.pdf)

It is worthwile to completely read all the above works. It is a lot of material, but foundational stuff, which reliefs 95+% of all practical (beginner) issues under Linux.

For terminal work, all parts of the **Introduction to GNU Core-Utils** are highly relevant, and the following chapters of **Komplette Einführung in Linux** are relevant:

- Read quickly: 1,5
- Read thoroughly: 6-11,21,26,30,31

The other chapters have virtually no relevance for our purposes.


<p></p>
---
<p></p>


# Terminal Sessions

## **Sudo** without password

**Sudo** is needed all the time to run tasks that need administrative privileges. It is annoying that it urges you to enter your password all the time.
Here is how to stop this behavior (**Warning**: this makes your system less secure): 

Backup the existing **Sudo** configuration by running `sudo cp /etc/sudoers /etc/sudoers~`. Then run `visudo`. Edit as follows:

    # Allow members of group sudo to execute any command
    %sudo ALL=(ALL:ALL) ALL

change this to

    # Allow members of group sudo to execute any command
    %sudo ALL=(ALL) NOPASSWD: ALL

Be careful and precise! If you mess up, there is no way to become root again and you have to e.g. boot with a rescue disc to restore the backed up file.

<p></p>
---
<p></p>


## Configuring US Keyboards to support German Umlauts

As a programmer you want a US keyboard. Here is how to provide a US keyboard with german umlauts: insert the following in `~/.Xmodmap`, then check with `xmodmap ~/.Xmodmap` for errors (that's it, the configuration will be loaded automatically on login, if the check gives no errors):

    ! remove CapsLock, as nobody needs it
    remove Lock = Caps_Lock
    
    ! reconfigure CapsLock as switch, s.t. it activates umlauts on a, o, u, s
    keycode  66 = Mode_switch
    keycode  30 = u U udiaeresis Udiaeresis
    keycode  32 = o O odiaeresis Odiaeresis
    keycode  38 = a A adiaeresis Adiaeresis
    keycode  39 = s S ssharp
    
Example: to get an ä press `CapsLock`-`a`, Ä with `CapsLock`-`Shift`-`A`.

<p></p>
---
<p></p>


## Using **Terminator**, the Cool Terminal Emulator

**Terminator** is a terminal emulator that can split screens and is in many ways an [improvement](http://software.jessies.org/terminator/) over the built-in gnome-terminal. 

![Terminator](/images/terminator.png)

Install **Terminator**:
    
1.  `sudo apt-get install terminator`
2.  Set up a [keyboard shortcut](https://www.evernote.com/shard/s89/sh/3295a820-7922-494a-876a-dfb95acb5477/7d02933ca42fa97f4d79191912dfb355) for starting **Terminator**. I use my blue “ThinkVantage” button for this (Lenovo ThinkPad).
3.  Configure **Terminator** by running the preferences editor via `python /usr/share/terminator/terminatorlib/prefseditor.py` and configure as follows (only differences to default values shown):

    1.  Global

        1.  Terminal separator size: 1 (only 1px to separate terminals)
        2.  Window state: Fullscreen
        3.  DBUS-Server: yes (as long as DBUS exists)

    2.  Profiles

        1.  General

            1.  Use system width fixed font: no
            2.  Font: mono, size as desired
            3.  Copy on selection: yes (text will be copied by simply selecting it)

        2.  Colors

            1.  Use colors from system theme: no
            2.  Built-in schemes: ambience

        3.  Background

            1.  Transparent background: yes, 0.9 (not necessary)

        4.  Scrolling

            1.  Scrollbar is: disabled
            2.  Scrollback: 5000 lines (recommend 5000 as minimum)

4.  [Learn keyboard shortcuts](https://www.evernote.com/shard/s89/sh/1e592a87-c9ce-4bcd-9111-5df6ba42badc/f1ae7810ba9383f299c899a2e4039b09)
for **Terminator**.

You can also copy the below configuration directly to `~/.config/terminator/config`:

    [global_config]
      dbus = True
      window_state = fullscreen
      handle_size = 1
    [keybindings]
    [profiles]
      [[default]]
        use_system_font = False
        copy_on_selection = True
        background_darkness = 0.95
        scrollback_lines = 5000
        background_type = transparent
        scrollbar_position = hidden
        foreground_color = "#ffffff"
        font = Monospace 8
        background_color = "#300a24"
      [layouts]
        [[default]]
          [[[child1]]]
            type = Terminal
            parent = window0
          [[[window0]]]
            type = Window
            parent = ""
    [plugins]


<p></p>
---
<p></p>


## More Improved Terminal Experience

Handling files on the console is very efficient, but can also be dangerous, since there is no 'trash can'. Deletion means **real** deletion!

###  Harden terminal commands **mv**, **cp** against data loss

Do not (never) delete directories or files unless for disk space (avoid **rm** command). This holds especially for beginners, but even power users employ the below tips!
If there is a need to store data to an occupied target, backup the target instead of deleting:

    am@sciocco:/tmp$ ls
    test1 test2

Consider renaming `test1` to `test2`, but `test2` exists. Solution: use `mv -b -T` (`-b`: backup, `-T`: treat target as file, not as directory)

    am@sciocco:/tmp$ mv -v -b -T test1 test2
    `test1' -> `test2' (backup: `test2~')
    am@sciocco:/tmp$ ls
    test2 test2~

Simpler version, i.e. "remove" file `test2` by backing it up to `test2~`:

    am@sciocco:/tmp$ ls
    test2 test2~
    am@sciocco:/tmp$ mv -v -b -T test2 test2~
    `test2' -> `test2~' (backup: `test2~~')
    am@sciocco:/tmp$ ls -l
    test2~ test2~~

Numbered backups by `--backup=numbered` are better than `-b`, since the latter stops at three tilde signs (`~`), then it deletes your data :

    ot3@toxcreate3:~/mv$ tree
    .
    |-- test1
    |   `-- test1.file
    |-- test2
    |   `-- test2.file
    `-- test3
        `-- test3.file

    ot3@toxcreate3:~/mv$ mv -v --backup=numbered -T test1 backup
    `test1' -> `backup'
    ot3@toxcreate3:~/mv$ mv -v --backup=numbered -T test2 backup
    `test2' -> `backup' (backup: `backup.~1~')
    ot3@toxcreate3:~/mv$ mv -v --backup=numbered -T test3 backup
    `test3' -> `backup' (backup: `backup.~2~')
    ot3@toxcreate3:~/mv$ tree
    .
    |-- backup
    |   `-- test3.file
    |-- backup.~1~
    |   `-- test1.file
    `-- backup.~2~
        `-- test2.file

It is clear that this strategy avoids data loss. To use it permanently, **which is highly recommended**, put in your `~/.bash_aliases`: 

    alias mv='mv -v --backup=numbered' 

and use `-T` as needed (for directories as targets). Do this also for **cp**: 

    alias cp='cp -v --backup=numbered'

as **cp** can also destroy data (by overwriting the target).

###Undelete

If a file got accidentally deleted by **rm**, use **undel** to recover it.

    sudo apt-get install e2undel
    man e2undel


<p></p>
---
<p></p>


### Color support in **Man** and **Less**

Put this in your `.bash_aliases`:

    man() {
    	env \
    		LESS_TERMCAP_mb=$(printf "\e[1;31m") \
    		LESS_TERMCAP_md=$(printf "\e[1;31m") \
    		LESS_TERMCAP_me=$(printf "\e[0m") \
    		LESS_TERMCAP_se=$(printf "\e[0m") \
    		LESS_TERMCAP_so=$(printf "\e[1;44;33m") \
    		LESS_TERMCAP_ue=$(printf "\e[0m") \
    		LESS_TERMCAP_us=$(printf "\e[1;32m") \
    			man "$@"
    }

This will make **Man** interpret color information.

Put this in your `.bashrc`:

    export LESS="-R"
    eval "$(lesspipe)"

This will make **Less** highlight source code when there is a file `~/.lessfilter` that has the following contents and is executable:

    #!/bin/sh
    source-highlight -fesc -oSTDOUT -i "$1" 2>/dev/null


<p></p>
---
<p></p>


# Ready-to-use script package

A convenient package to improve user experience [is available](https://github.com/amaunz/home-bin/).

    cd
    git clone https://github.com/amaunz/home-bin/
    cd home-bin
    ./_init.sh

This copies

- **git** configuration: diff-wrapper that creates side-by-side diffs in **Vim**
- **bash** configuration files: History without duplicates, colored prompt with dark background (better to read, less battery), syntax-highlighted **Less** output, ...
- **vim** configuration file: Syntax highlighting, correct indentation (using spaces), unobtrusive color scheme with dark background (better to read, less battery), ruler, window title, ...
- **irb** (ruby interactive) configuration file: Command completion, history.

to your home directory (backups of existing files are created). Just log out and log in again to let the changes take effect.
