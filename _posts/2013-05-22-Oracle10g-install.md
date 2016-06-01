---
layout: post
title: "Oracle 10g OUI mode installation"
description: ""
category: Oracle
tags:
---

#### Preparation

##### 1) Create Oracle user
![](/images/post/20130522_01.jpg)

<br>

##### 2) config on sysctl.conf
 
{% highlight bash %}
{% raw %}
[root@chacha ~]# vi /etc/sysctl.conf
## add below lines
kernel.shmmax=536870912
kernel.shmmni=4096
kernel.shmall=2097152
kernel.sem=250 32000 100 128
fs.file-max=65536
net.ipv4.ip_local_port_range=1024 65000
net.core.rmem_default=262144
net.core.rmem_max=262144
net.core.wmem_default=262144
net.core.wmem_max=262144
{% endraw %}
{% raw %}
[root@chacha ~]# sysctl -p
net.ipv4.ip_forward = 0
net.ipv4.conf.default.rp_filter = 1
net.ipv4.conf.default.accept_source_route = 0
kernel.sysrq = 0
kernel.core_uses_pid = 1
kernel.shmmax = 536870912
kernel.shmmni = 4096
kernel.shmall = 2097152
kernel.sem = 250 32000 100 128
fs.file-max = 65536
net.ipv4.ip_local_port_range = 1024 65000
net.core.rmem_default = 262144
net.core.rmem_max = 262144
net.core.wmem_default = 262144
net.core.wmem_max = 262144
{% endraw %}
{% endhighlight %}

<br>

##### 3) config on limits.conf

{% highlight bash %}
[root@chacha ~]# vi  /etc/security/limits.conf
## adding below lines
oracle10g soft nproc 2047
oracle10g hard nproc 16384
oracle10g soft nofile 1024
oracle10g hard nofile 65536
{% endhighlight %}

<br>

##### 4) config on /etc/pam.d/login

{% highlight bash %}
[root@chacha ~]# vi  /etc/pam.d/login
## adding below lines
session   required   pam_limits.so
{% endhighlight %}

<br>

##### 5) install rpm and RHEL4 patch

{% highlight bash %}
[root@chacha ~]# umount /media/cdrecorder/
[root@chacha ~]# mount /media/cdrecorder/
mount: block device /dev/hdc is write-protected, mounting read-only
[root@chacha ~]# cd /media/cdrecorder/RedHat/RPMS/
[root@chacha RPMS]# rpm -Uvh libaio-devel-0.3.105-2.i386.rpm
warning: libaio-devel-0.3.105-2.i386.rpm: V3 DSA signature: NOKEY, key ID db42a60e
Preparing...                ########################################### [100%]
   1:libaio-devel           ########################################### [100%]
[root@chacha RPMS]#
[root@chacha RPMS]# cd /home/oracle/patch
[root@chacha patch]# unzip p4198954_40_LINUX.zip
Archive:  p4198954_40_LINUX.zip
  inflating: compat-libcwait-2.1-1.i386.rpm
  inflating: compat-oracle-rhel4-1.0-5.i386.rpm
  inflating: README.txt
[root@chacha patch]# rpm -Uvh compat-*
Preparing...                ########################################### [100%]
   1:compat-oracle-rhel4    ########################################### [ 50%]
   2:compat-libcwait        ########################################### [100%]
{% endhighlight %}

<br>

##### 6) Oracle environment setup

{% highlight bash %}
[root@chacha patch]# su - oracle
[oracle@chacha ~]$ vi .bash_profile
## adding below lines
export EDITOR=vi
export LD_ASSUME_KERNEL=2.4.19
export ORACLE_BASE=/home/oracle
export ORACLE_HOME=$ORACLE_BASE/product/10g
export ORACLE_SID=testdb
export LANG=c
export ORACLE_TERM=xterm
export NLS_LANG=AMERICAN_AMERICA.KO16MSWIN949
export ORA_NLS33=$ORACLE_HOME/ocommon/nls/admin/data
export LD_LIBRARY_PATH=$ORACLE_HOME/lib:/lib:/usr/lib:/usr/local/lib
export PATH=$PATH:$ORACLE_HOME/bin
export CLASSPATH=$ORACLE_HOME/JRE:$ORACLE_HOME/jlib:$ORACLE_HOME/rdbms/jlib
{% endhighlight %}

<br>

##### 7) prepare installatoin file (10201_database_linux32.zip)

{% highlight bash %}
[oracle@chacha ~]$ exit
[root@chacha ~]# cd /home/oracle
[root@chacha oracle]# unzip 10201_database_linux32.zip
[root@chacha oracle]# chown -R oracle.dba /home/oracle 
{% endhighlight %}

<br>

#### Installation

* 완전히 로그 아웃 후 VM 에서 oracle 계정으로 로그인, 새 터미널을 하나 띄우고, runInstaller 실행한다.

![](/images/post/20130522_02.jpg)

![](/images/post/ins_01.jpg)
![](/images/post/ins_02.jpg)
![](/images/post/ins_03.jpg)
![](/images/post/ins_04.jpg)
![](/images/post/ins_05.jpg)


* 설치 중 아래와 같은 화면이 뜨면 root 계정으로 위의 스크립트들을 실행 시킨 후 진행한다. 

![](/images/post/ins_06.jpg)
![](/images/post/ins_07.jpg)
![](/images/post/ins_08.jpg)


* 설치 완료 되면 sqlplus 로 접속 확인을 한다.

{% highlight bash %}
[root@chacha ~]# su - oracle
[oracle@chacha ~]$ sqlplus / as sysdba
SQL*Plus: Release 10.2.0.1.0 - Production on Wed May 22 11:01:50 2013
Copyright (c) 1982, 2005, Oracle.  All rights reserved.
Connected to an idle instance.
SQL>
{% endhighlight %}

<br>

#### Patch

##### 1) Prepare Patch file

{% highlight bash %}
[root@chacha ~]# cd /home/oracle/patchset
[root@chacha patchset]# unzip p6810189_10204_Linux-x86.zip
{% endhighlight %}

<br>

##### 2) VM 안의 터미널에서 oracle 계정으로 patch 실행

![](/images/post/p_01.jpg)
![](/images/post/p_02.jpg)
![](/images/post/p_03.jpg)
![](/images/post/p_04.jpg)
![](/images/post/p_05.jpg)
![](/images/post/p_06.jpg)
![](/images/post/p_07.jpg)

* 위와 같은 창이 나오면 root 계정으로 위의 스크립트를 실행해 준다.

![](/images/post/p_08.jpg)

![](/images/post/p_09.jpg)


* 설치 완료 되면 sqlplus 로 접속 확인을 한다.

{% highlight bash %}
[root@chacha ~]# su - oracle
[oracle@chacha ~]$ sqlplus / as sysdba
SQL*Plus: Release 10.2.0.4.0 - Production on Wed May 22 11:15:29 2013
Copyright (c) 1982, 2007, Oracle.  All Rights Reserved.
Connected to an idle instance.
SQL>
{% endhighlight %}

<br>

#### Create DataBase

* VM 안의 터미널에서 oracle 계정으로 dbca 를 입력한 후 뜨는 창에서 아래 그림과 같이 진행한다.

![](/images/post/d_01.jpg)
![](/images/post/d_02.jpg)
![](/images/post/d_03.jpg)
![](/images/post/d_04.jpg)
![](/images/post/d_05.jpg)
![](/images/post/d_06.jpg)
![](/images/post/d_07.jpg)
![](/images/post/d_08.jpg)
![](/images/post/d_09.jpg)
![](/images/post/d_10.jpg)
![](/images/post/d_11.jpg)
![](/images/post/d_12.jpg)


* 위의 화면에서 종료를 누르면 마지막 확인할 수 있는 창이 뜨고 맨 밑에 OK 버튼이 숨겨져 있다. 그걸 누르면 아래와 같이 파일 생성이 진행된다.

![](/images/post/d_13.jpg)
![](/images/post/d_14.jpg)


* 설치 완료 되면 sqlplus 로 접속 확인을 하고 생성 된 DB 를 확인한다.

{% highlight bash %}
[oracle@chacha ~]$ sqlplus / as sysdba
SQL*Plus: Release 10.2.0.4.0 - Production on Wed May 22 11:49:37 2013
Copyright (c) 1982, 2007, Oracle.  All Rights Reserved.
Connected to:
Oracle Database 10g Enterprise Edition Release 10.2.0.4.0 - Production
With the Partitioning, OLAP, Data Mining and Real Application Testing options
SQL> select status from v$instance;
STATUS
------------
OPEN
SQL> select name from v$database;
NAME
---------
TESTDB
SQL>
{% endhighlight %}

