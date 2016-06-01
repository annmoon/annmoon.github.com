---
layout: post
title: "Linux kernel compile for upgrading"
description: ""
category: linux
tags: [kernel]
---

#### check the current kernel version
{% highlight bash %}
[root@ServerA ~]# uname -r
2.6.32-358.el6.x86_64
{% endhighlight %}

<br>

#### download new kernel source
{% highlight bash %}
[root@ServerA ~]# wget https://www.kernel.org/pub/linux/kernel/v3.x/linux-3.12.tar.xz
[root@ServerA ~]# tar xvfvJ linux-3.12.tar.xz -C /usr/src
[root@ServerA ~]# cd /usr/src/linux-3.12/
{% endhighlight %}

<br>

#### install additional packages
{% highlight bash %}
[root@ServerA linux-3.12]# yum groupinstall "Development Tools"
[root@ServerA linux-3.12]# yum install ncurses-devel
[root@ServerA linux-3.12]# yum update
{% endhighlight %}

<br>

#### kernel config initialization
{% highlight bash %}
[root@ServerA linux-3.12]# pwd
/usr/src/linux-3.12
[root@ServerA linux-3.12]# make mrproper
[root@ServerA linux-3.12]# 
{% endhighlight %}

<br>

#### kernel configuration

- 그래픽 환경에서는 # make xconfig 를 사용하면 되지만, 그래픽 환경을 사용하지 못하는 곳에서는 make menuconfig 를 사용하면 된다.

- 그리고 기존 환경을 그대로 사용하고 싶다면 make oldconfig 를 이용하면 된다. oldconfig 를 할 때 여러가지 항목에 y/n/m 입력을 피하고 싶으면 (기존 설정 그대로 원한다면) 아래 command 로 실행해 주면 된다. 
{% highlight bash %}
# sh -c 'yes "" | make oldconfig' 
{% endhighlight %}
- 위의 명령어로 기존과 동일하게 설정해보자.
{% highlight bash %}
[root@ServerA linux-3.12]# sh -c 'yes "" | make oldconfig'
{% endhighlight %}

<br>

#### clean old config
{% highlight bash %}
[root@ServerA linux-3.12]# make clean
[root@ServerA linux-3.12]# 
{% endhighlight %}

<br>

#### kernel compile

- make : 커널 컴파일 환경 설정대로 소스를 실제 컴파일 하는 과정

- make modules_install : 컴파일된 모듈을 /lib/modules/ 디렉토리에 설치하는 과정

- make install : 실제 컴파일된 커널을 /boot/ 디렉토리에 설치하는 과정
{% highlight bash %}
[root@ServerA linux-3.12]#  make
[root@ServerA linux-3.12]#  make modules_install
[root@ServerA linux-3.12]#  make install
{% endhighlight %}

<br>

#### check boot loader

/lib/modules 디렉토리와 /boot 디렉토리를 확인해 보면 새로운 커널 버전을 확인할 수 있다. 
{% highlight bash %}
[root@ServerA ~]# ls -l /lib/modules/
total 16
drwxr-xr-x. 7 root root 4096 Nov 21 10:30 2.6.32-358.23.2.el6.x86_64
drwxr-xr-x. 8 root root 4096 Nov 13 23:47 2.6.32-358.el6.x86_64
drwxr-xr-x. 3 root root 4096 Nov 21 13:18 2.6.39
drwxr-xr-x. 3 root root 4096 Nov 22 17:45 3.12.0
[root@ServerA ~]#
[root@ServerA ~]# ls -l /boot
total 74540
-rw-r--r--. 1 root root   104112 Oct 17 04:01 config-2.6.32-358.23.2.el6.x86_64
-rw-r--r--. 1 root root   104081 Feb 22  2013 config-2.6.32-358.el6.x86_64
drwxr-xr-x. 3 root root     1024 Nov 12 08:26 efi
drwxr-xr-x. 2 root root     1024 Nov 22 17:45 grub
-rw-r--r--. 1 root root 16603681 Nov 21 10:30 initramfs-2.6.32-358.23.2.el6.x86_64.img
-rw-r--r--. 1 root root 16631249 Nov 13 23:48 initramfs-2.6.32-358.el6.x86_64.img
-rw-r--r--. 1 root root  4904840 Nov 21 13:18 initramfs-2.6.39.img
-rw-r--r--. 1 root root  5007188 Nov 22 17:45 initramfs-3.12.0.img
drwx------. 2 root root    12288 Nov 12 08:20 lost+found
-rw-r--r--. 1 root root   185978 Oct 17 04:02 symvers-2.6.32-358.23.2.el6.x86_64.gz
-rw-r--r--. 1 root root   185734 Feb 22  2013 symvers-2.6.32-358.el6.x86_64.gz
lrwxrwxrwx. 1 root root       23 Nov 22 17:45 System.map -> /boot/System.map-3.12.0
-rw-r--r--. 1 root root  2408974 Oct 17 04:01 System.map-2.6.32-358.23.2.el6.x86_64
-rw-r--r--. 1 root root  2407466 Feb 22  2013 System.map-2.6.32-358.el6.x86_64
-rw-r--r--. 1 root root  2363469 Nov 21 13:18 System.map-2.6.39
-rw-r--r--. 1 root root  2363469 Nov 21 13:11 System.map-2.6.39.old
-rw-r--r--. 1 root root  2699213 Nov 22 17:45 System.map-3.12.0
lrwxrwxrwx. 1 root root       20 Nov 22 17:45 vmlinuz -> /boot/vmlinuz-3.12.0
-rwxr-xr-x. 1 root root  4047152 Oct 17 04:01 vmlinuz-2.6.32-358.23.2.el6.x86_64
-rwxr-xr-x. 1 root root  4043888 Feb 22  2013 vmlinuz-2.6.32-358.el6.x86_64
-rw-r--r--. 1 root root  3930720 Nov 21 13:18 vmlinuz-2.6.39
-rw-r--r--. 1 root root  3930720 Nov 21 13:11 vmlinuz-2.6.39.old
-rw-r--r--. 1 root root  4359984 Nov 22 17:45 vmlinuz-3.12.0
{% endhighlight %}
