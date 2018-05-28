# Desktop Devil
A real time, host based, malicious software and intrusion detection system for Linux desktops.

**This project was always educational in scope and is now officially dead. Certain elements will, eventually, be incorporated into my [Linux, hardening project](https://github.com/Irvinehimself/TheBeggarsHardeningProject), but, for now, to mark it's official death, I am archiving it as 'read only'**

**License:** GPLv3

**Dependencies:**
* inotify-tools
* libnotify
* bash compatible command processor
* notification daemon of choice

**Optional**
* alltray : (not necessary, but minimising the control panel to the system tray is a really good idea if you would like to run a continuous watch)
* nano    : (easily configured to something else)

### Contents:
* What is Desktop Devil?
* Features:
* Notes:
* Installation:

### What is Desktop Devil?
Linux has a large number of very powerful *HIDS*  packages, *(Host Based Intrusion Detection Systems,)* aimed at the server market, but little to nothing for your average desktop user. Desktop Devil is an extremely lightweight *"Real Time HIDS"* package that is aimed squarely at the Linux desktop. While it is currently only a prototype or template, it is still quite usable.

The basic idea is: On launch, Desktop Devil  sets up IRQ's for activity in the watched files and folders. When malicious software, or an intruder, tries to do something to the watched files and folders, this will trigger the  IRQ.

Using the native *inotify* capabilities of the Linux kernel, it can watch all the important directories, *eg: "/bin, /boot, /dev, /etc, /opt, /pro, /run, /srv, /sys, /usr and /var",* with virtually no cost to system resources. In my test-bed, covering the above-mentioned folders, when I run *PS aux | grep "inotifywait"*, %CPU and %MEM usage are too small to register.

By default watches are continuous and recursive, *(inotifywait -mr)*. Meaning sub-folders and new files are watched and watches run until the program is terminated.

### Features:
* Violations are recorded to the log along with a pop-up message outlining the trigger event.

* An "OverWatch" which monitors the *"inotifywait"* watches. When it detects watches being unlawfully killed: it beeps twice, notifies the desktop with pop-up and sends a report to the log.

* A sleep function to allow essential system maintenance without spamming the log and desktop with false alarms. For security, this uses a random key generated at runtime to validate the authenticity of the sleep msg. Eventually, this will be encrypted with further security features enabled.

* Both the *"--exclude(..)"* and *"--include(..)"* options of *"inotifywait"* are supported, though the latter is *'buggy'*

* An important goal is to make the configuration so simple even total noobs are not put off. For this reason, I am actively working on a set of automated tools to create the *"--exclude(..)"* and *"--include(..)"* expressions. This is accessed from the main control panel and can be safely used while Desktop Devvils is running. Currently, it consists of a generic list of applications, *"createcludeslist"*, for which you may wish to apply higher security to the respective folders. Giving it a target, eg *'/usr/lib/'* or *'/etc'*. It uses various sort algorythms to create Exclude/Include expressions speciffic to the folder contents, which you can copy/paste directly into *"dtdwatchlist"*

* A test routine launched from the control panel which automatically creates, (and deletes,) nested test directories, along with editable text files, in a target directory. (Requires nano.)

### Notes & Issues:
Okay, at the moment, the biggest single issue is symlinks, in particular symlinked folders. In any security situation, symlinks can be the source of major vulnerabilities with multiple issues and problems. Try a Google search, you will find a great many diverse technical discussions on the subject.

Noting that a link is actually a file outlining a path, the problem is: "How can I detect changes to the *link file*, without actually indexing the *link target*?"

To explain, consider the file system architecture of Arch Linux: */bin*, */sbin* and */usr/sbin* are all symlinked to */usr/bin*. Even worse, */lib* and */usr/lib64* are symlinked to */usr/lib*. For malware, these are all prime real estate. But, by setting a watch on the symlinks, you are indexing the same folder several times. Now this is bad for several reasons, not the least of which is: It can set up a race condition when the first watch is triggered by a second watch double indexing the folder.

In my demo configuration I try to exclude symlinks to folders. However, this leaves open the possibility of an attacker changing the target of the link. While the above mentioned symlinks, are so fundamental to the correct operation of the OS, any interference would be immediately noticeable, this may not always be the case for other symlinked folders.

As a result, this is a hole in the coverage for which I am actively researching possible solutions. On the other hand, one way or another, the folders themselves are being watched. So, providing you are not running as root, the situation is probably not as desperate as it may sound.

### Installation:
As stated, this is a prototype, I am running it from my git folder without privileges, (hence the reason my demo config doesn't index files and folders for which only root has access.)

Incidently, I have have Desktop Devil on auto-start and it is running more or less constantly. Recently, when browsing in the darker regions of the net, I actually caught some genuine suspicious activity when a website tried, (and failed,) to create a file where it shouldn't.

Anyway, once you have either cloned or otherwise downloaded the repo, you need to change the *"PathToWorkDir="$HOME/Git/DesktopDevil"* in the main file *"desktopdevil"*. With the exception outlined below, all the other paths are relative.

In the demo configuration file, I have used the pseudonym  *"PathToHOME"*. Since it is based on my system, you will have to edit this file no matter what. BUT, you should note: To validate the *inotifywait* PIDs, *OverWatch* greps the configuration file for the expanded path. In other words, do not use environment variables like *$HOME*.

With the exception of the configuration files, *"dtdwatchlist"* and *"createcludeslist"*, all the user configurable option for Desktop Devil are found in *"dtduservariables"*. Here you can change the annoying messages, set *"WatchLimits""*, rename logfiles and a whole host other personal preferences.

You should note that both *"dtdwatchlist"* and *"dtduservariables"* have extensive inline documentation for the various options.


