# Desktop Devil
Real Time, Host based Malicous Software and Intrusion Detection System for Linux Desktops

**License:** GPLv3

**Dependencies:**
* inotify-tools
* libnotify
* bash compatible command processor
* notification daemon of choice

**Optional**
* alltray : (not necessary, but minimising the control panel to the system tray is a really good idea if you would like to run a continuous watch)
* nano    : (easily configured to something else)

<br><br>
### Contents:
* What is Desktop Devil?
* Features:
* Notes:
* Installation:

<br><br>
### What is Desktop Devil?
Linux has a large number of very powerful *HIDS*  packagaes, *(Host Based Intrusion Detection Systems,)* aimed at the server market, but little to nothing for your average desktop user. Desktop Devil is an extremely lightweight *"Real Time HIDS"* package that is aimed squarely at the Linux desktop. While it is currently only a prototype or template, it is still quite usable.

The basic idea is: On launch, Desktop Devil  sets up IRQ's for activity in the watched files and folders. When malicious software, or an intruder, tries to do something to the watched files and folders, this will automatically trigger the  IRQ.

Using the native *inotify* capabilities of the Linux kernel, it can watch all the important directories, *eg: "/bin, /boot, /dev, /etc, /opt, /pro, /run, /srv, /sys, /usr and /var",* with virtually no cost to system resources. In my test-bed, covering the above-mentioned folders, when I run *PS aux | grep "inotifywait"*, %CPU and %MEM usage are too small to register.

By default watches are continuous and recursive, *(inotifywait -mr)*. Meaning sub-folders and new files are watched and watches run until the program is terminated.

<br><br>
### Features:
* Violations are recorded to the log along with a pop-up message outlining the trigger event.

* An "OverWatch" which monitors the *"inotifywait"* watches. When it detects watches being unlawfully killed: it beeps twice, notifies the desktop with pop-up and sends a report to the log.

* A sleep function to allow essential system maintenance without spamming the log and desktop with false alarms. For security, this uses three random keys generated at runtime to validate the authenticity of the sleep msg. Eventually, this will be encrypted with further security features enabled.

* Both the *"--exclude(..)"* and *"--include(..)"* options of *"inotifywait"* are supported, though the latter is *'buggy'*

* An important goal is to make the configuration so simple even total noobs are not put off. For this reason, I am actively working on a set of automated tools to create the *"--exclude(..)"* and *"--include(..)"* expressions. However, as the demo configuration file shows, a simple, *no frills* system wide configuration is easily implemented.

* A test routine launched from the control panel which automatically creates, (and deletes,) nested test directories, along with editable text files, in a target directory. (Requires nano.)

<br><br>
### Notes & Issues:
Okay, at the moment, the biggest single issue is symlinks. In any security situation, symlinks are a major vulnerability with multiple issues and problems. Try a Google search, you will find a great many diverse technical discussions on the subject.

In the case of Desktop Devil, the problem is: "How can I detect changes in the link target, without actually indexing or following the link?"

To explain, consider the file system architecture of Arch Linux: */bin*, */sbin* and */usr/sbin* are all symlinked to */usr/bin*. Even worse, **/lib* and */usr/lib64* are symlinked to */usr/lib*. For malware, these are all prime real estate, but, by setting a watch on the symlinks, you are indexing the same folder several times. Now this is bad for several reasons, not the least of which is: It can set up a race condition where the first watch is triggered by the second watch ... etc.

So, in my demo configuration, I try to exclude symlinks. However, this leaves open the possibility of an attacker changing the target of the link. This is a major security hole for which I am actively researching possible solutions.

<br><br>
### Installation:
As stated, this is a prototype, I am running it, (without privileges,) from my git folder. I have have it running more or less constantly and, when browsing in the darker regions of the net, I once actually caught some genuine suspicious activity when a website tried, (and failed,) to create a file where it shouldn't.

Once you have either cloned or otherwise downloaded the repo, you need to change the *"PathToWorkDir="$HOME/Git/DesktopDevil"* in the main file *"desktopdevil"*. Apart from that, all the other paths are relative.

The exception being the *"PathToHOME"* directory in the configuration file. Since it is based on my system, you will be editing this file anyway, BUT, you should note that, to validate that the *inotifywait* PIDs belong to Desktop Devil, *OverWatch* greps the configuration file for the expanded path. In other words, do not use environment variables like *$HOME*.

With the exception of the watch configuration file, *"dtdwatchlist"*, all the user configurable option for Desktop Devil are found in *"dtduservariables"*. Here you can change the annoying messages, set *"WatchLimits""*, rename logfiles and a whole host other personal preferences.

You should note that both *"dtdwatchlist"* and *"dtduservariables"* have extensive inline documentation for the various options.



