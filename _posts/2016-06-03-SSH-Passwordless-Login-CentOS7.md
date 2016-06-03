---
layout: post
title: "How to set up SSH Passwordless Login on CentOS 7"
description: ""
category: linux
tags:
---

### create a pair of keys (public/private)

{% highlight bash %}
# on local server

[deploy@local ~ ]$ ssh-keygen -t rsa
Generating public/private rsa key pair.
Enter file in which to save the key (/home/deploy/.ssh/id_rsa): 

# if you want to no passphrase, you just enter. but, it can be riskly on security

Enter passphrase (empty for no passphrase): 
Enter same passphrase again: 
Your identification has been saved in /home/deploy/.ssh/id_rsa.
Your public key has been saved in /home/deploy/.ssh/id_rsa.pub.
The key fingerprint is:
5e:ce:6f:10:73:01:a6:25:82:20:86:81:b8:ea:44:45 deploy@local
{% endhighlight %}

* Once you have created, you will find two files inside the .ssh directory. id_rsa (private key) and id_rsa.pub (public key).

{% highlight bash %}
[deploy@local ~]$ cd .ssh
[deploy@local .ssh]$ ll
total 16
-rw-------. 1 deploy deploy 1675 Jun  3 05:49 id_rsa
-rw-r--r--. 1 deploy deploy  397 Jun  3 05:49 id_rsa.pub
{% endhighlight %}

<br>

### Copy public key to remote host

{% highlight bash %}
# on local server

[deploy@local .ssh]$ ssh-copy-id -i ~/.ssh/id_rsa.pub deploy@remote_servername
{% endhighlight %}

* try to ssh into remote server

{% highlight bash %}
[deploy@local ~]$ ssh deploy@remote_servername
{% endhighlight %}

<br>

>
if your OS is CentOS 7 (or RHEL 7) and it is still required password, it can be due to encrypt the user directory. 
So that the authorized_keys file placed in ~/.ssh/ on remote server would not work.
In this case, let's move authorized_keys file into another place and then make change sshd config as below.

<br>

{% highlight bash %}
# on remote server as root user

[root@remote ~]# sudo cp /home/deploy/.ssh/authorized_keys /etc/ssh/authorized_keys
[root@remote ~]# vi /etc/ssh/sshd_config

# change AuthorizedKeysFile property
AuthorizedKeysFile      /etc/ssh/authorized_keys

[root@remote ~]# systemctl restart sshd
{% endhighlight %}

