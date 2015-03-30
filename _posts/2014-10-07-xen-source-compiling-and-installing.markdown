---
layout: post
title: Xen-4.4 Installation from source
category: Cloud Computing
tagline: by wubin
tags: [Cloud Computing,Xen,Linux]
---


The first step is to intall a clean Ubuntu Server (Amd-64) on your server (Version 14.04 is strongly recommended for the best compatibility).


Get the source code of Xen ready by `git clone` (If some errors occur in this step, you may check the /ect/apt/source.list and update it by running `$ sudo apt-get update`*)

<!--more-->

	$ git clone git://xenbits.xen.org/xen.git

If you have not yet install "git", please run "sudo apt-get install git" on your terminal.

	$ cd xen

Use "git branch" to show the versions of Xen:

	$ git branch -av  
	master                     3e2331d VT-d: suppress UR signaling for further desktop chipsets
	remotes/origin/HEAD        -&gt; origin/master
	remotes/origin/master      3e2331d VT-d: suppress UR signaling for further desktop chipsets
	remotes/origin/stable-4.0  2692df2 compat/gnttab: Prevent infinite loop in compat code
	remotes/origin/stable-4.1  8995a94 page-alloc: scrub pages used by hypervisor upon freeing
	remotes/origin/stable-4.2  7bcdeb9 update Xen version to 4.2.5
	remotes/origin/stable-4.3  a153d8a update Xen version to 4.3.3
	remotes/origin/stable-4.4  be84c34 evtchn: check control block exists when using FIFO-based events
	remotes/origin/staging     3e2331d VT-d: suppress UR signaling for further desktop chipsets
	remotes/origin/staging-4.0 2692df2 compat/gnttab: Prevent infinite loop in compat code
	remotes/origin/staging-4.1 8995a94 page-alloc: scrub pages used by hypervisor upon freeing
	remotes/origin/staging-4.2 7bcdeb9 update Xen version to 4.2.5
	remotes/origin/staging-4.3 a153d8a update Xen version to 4.3.3
	remotes/origin/staging-4.4 be84c34 evtchn: check control block exists when using FIFO-based events
	
Checkout to "stable-4.4" and create a new branch named "netopt" and then checkout to it:
															
	$ git checkout stable-4.4
	Switched to branch 'stable-4.4'
	$ git branch netopt
	$ git checkout netopt
	Switched to branch 'net opt'																													   

Before compiling the source code of Xen, you must install the dependencies: (If some errors occur in this step, you may check the **/ect/apt/source.list** and update it by running `$ sudo apt-get update`)

	$ sudo apt-get install build-essential
	$ sudo apt-get install bcc bin86 gawk bridge-utils iproute libcurl3 libcurl4-openssl-dev bzip2 module-init-tools transfig tgif 
	$ sudo apt-get install texinfo texlive-latex-base texlive-latex-recommended texlive-fonts-extra texlive-fonts-recommended pciutils-dev mercurial
	$ sudo apt-get install make gcc libc6-dev zlib1g-dev python python-dev python-twisted libncurses5-dev patch libvncserver-dev libsdl-dev libjpeg-dev
	$ sudo apt-get install iasl libbz2-dev e2fslibs-dev git-core uuid-dev ocaml ocaml-findlib libx11-dev bison flex xz-utils libyajl-dev
	$ sudo apt-get install gettext pandoc markdown python-dev
	$ sudo apt-get install libperl-dev libgtk2.0-dev libaio-dev gcc-multilib

Now you can compile the source code and install Xen: (Note that this step takes a long time. Be patient!)

	$ ./configure
	$ make world
	$ sudo make install

After previous step, Xen is already successfully installed on your machine. However, some post-installation operations is necessary:

You can enable automatic start of Xen Project services on system startup:

	$ sudo update-rc.d xencommons defaults 19 18 
	$ sudo update-rc.d xendomains defaults 21 20 
	$ sudo update-rc.d xen-watchdog defaults 22 23

By running "$ ls /etc/init.d/", you can find `"xencommons"`, `"xendomains"` and `"xen-watchdog"`. Note that Starting with Xen-4.3, "xend" is abandoned and replace by "xl".

You also need to make some changes to "/boot/grub/grub.cfg" to correctly boot to xen.gz:

	$ sudo update-grub
	$ sudo vim /boot/grub/grub.cfg

Modify the grub default option from "0" to "2": set default="2" (The option "2" normally locates the menuentry of "xen.gz")

Reboot the Ubuntu Server: (Now your machine will automatically boot from Xen's core)

    $ sudo reboot

You can run `"sudo xl list"` to test whether Xen is running correctly. If some errors occur, try to add a soft link to "libxenctrl.so.4.4":

    $ ln -s /usr/local/lib/libxenctrl.so.4.4 libxenctrl.so.4.4
	$ sudo ldconfig

Then restart service xencommons:

    $ sudo service xencommons start/restart

or:

    $ sudo /etc/init.d/xencommons start/restart

Congradulations! All steps are completed and now you can create a virtual machine by sudo xl create **.
