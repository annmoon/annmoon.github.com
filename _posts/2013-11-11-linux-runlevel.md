---
layout: post
title: "Linux runlevel"
description: ""
category: linux
tags: [runlevel]
---
To shutdown linux system, there was some ways as below:

{% highlight bash %}
1) shutdown -h now (shutdown right now)
2) halt
3) init 0
{% endhighlight %}

<b>init</b> command is related with Runlevel which is a preset operating state on a Unix-like operating system.
here are Runlevel Definition.

{% highlight bash %}
0 - System halt; no activity, the system can be safely powered down.
1 - Single user; rarely used.
2 - Multiple users, no NFS (network filesystem); also used rarely.
3 - Multiple users, command line (i.e., all-text mode) interface; the standard runlevel for most Linux-based server hardware.
4 - User-definable
5 - Multiple users, GUI (graphical user interface); the standard runlevel for most Linux-based desktop systems.
6 - Reboot; used when restarting the system. 
{% endhighlight %}   

The default runlevel for a system is specified in the /etc/inittab file, which will contain an entry such as, 
 
{% highlight bash %}
id:3:initdefault
{% endhighlight %}
