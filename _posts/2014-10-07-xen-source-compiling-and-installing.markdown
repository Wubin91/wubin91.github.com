---
layout: post
title:  "Compiling Xen-4.4 From Source And Installing It On Ubuntu Server"
category: Xen
tags: Xen
---

####1. First of all, you should install a clean Ubuntu Server (Amd-64) on your server (Version 14.04 is strongly recommended for the best compatibility).



####2. Get the source code of Xen ready by "git clone" (If some errors occur in this step, you may check the /ect/apt/source.list and update it by running `$ sudo apt-get update`*)
{% highlight ruby %}
$ git clone git://xenbits.xen.org/xen.git
{% endhighlight %}

If you have not yet install "git", please run "sudo apt-get install git" on your terminal.

{% highlight ruby %}
$ cd xen
{% endhighlight %}

Use "git branch" to show the versions of Xen:

{% highlight ruby %}
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
{% endhighlight %}
Checkout to "stable-4.4" and create a new branch named "netopt" and then checkout to it:																
{% highlight ruby %}
$ git checkout stable-4.4
Switched to branch 'stable-4.4'
$ git branch netopt
$ git checkout netopt
Switched to branch 'net opt'
{% endhighlight %}
																													    


####3. Before compiling the source code of Xen, you must install the dependencies: (If some errors occur in this step, you may check the **/ect/apt/source.list** and update it by running `$ sudo apt-get update`)

{% highlight ruby %}
$ sudo apt-get install build-essential
$ sudo apt-get install bcc bin86 gawk bridge-utils iproute libcurl3 libcurl4-openssl-dev bzip2 module-init-tools transfig tgif 
$ sudo apt-get install texinfo texlive-latex-base texlive-latex-recommended texlive-fonts-extra texlive-fonts-recommended pciutils-dev mercurial
$ sudo apt-get install make gcc libc6-dev zlib1g-dev python python-dev python-twisted libncurses5-dev patch libvncserver-dev libsdl-dev libjpeg-dev
$ sudo apt-get install iasl libbz2-dev e2fslibs-dev git-core uuid-dev ocaml ocaml-findlib libx11-dev bison flex xz-utils libyajl-dev
$ sudo apt-get install gettext pandoc markdown python-dev
$ sudo apt-get install libperl-dev libgtk2.0-dev libaio-dev gcc-multilib
{% endhighlight %}



####4. Now you can compile the source code and install Xen: (Note that this step takes a long time. Be patient!)

{% highlight ruby %}
$ ./configure
$ make world
$ sudo make install
{% endhighlight %}



####5. After step 4, Xen is already successfully installed on your machine. However, some post-installation operations is necessary:

You can enable automatic start of Xen Project services on system startup:

{% highlight ruby %}
$ sudo update-rc.d xencommons defaults 19 18 
$ sudo update-rc.d xendomains defaults 21 20 
$ sudo update-rc.d xen-watchdog defaults 22 23
{% endhighlight %}

By running "$ ls /etc/init.d/", you can find "xencommons", "xendomains" and "xen-watchdog". Note that Starting with Xen-4.3, "xend" is abandoned and replace by "xl".

You also need to make some changes to "/boot/grub/grub.cfg" to correctly boot to xen.gz:

{% highlight ruby %}
$ sudo update-grub
$ sudo vim /boot/grub/grub.cfg
{% endhighlight %}

Modify the grub default option from "0" to "2": set default="2" (The option "2" normally locates the menuentry of "xen.gz")



####6. Reboot the Ubuntu Server: (Now your machine will automatically boot from Xen's core)

{% highlight ruby %}
    $ sudo reboot
{% endhighlight %}




####7. You can run "sudo xl list" to test whether Xen is running correctly. If some errors occur, try to add a soft link to "libxenctrl.so.4.4":

{% highlight ruby %}
    $ ln -s /usr/local/lib/libxenctrl.so.4.4 libxenctrl.so.4.4
	$ sudo ldconfig
{% endhighlight %}

Then restart service xencommons:

{% highlight ruby %}
$ sudo service xencommons start/restart
{% endhighlight %}

or:

{% highlight ruby %}
$ sudo /etc/init.d/xencommons start/restart
{% endhighlight %}





####8. Congradulations! All steps are completed and now you can create a virtual machine by sudo xl create **.
