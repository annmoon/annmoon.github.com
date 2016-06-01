---
layout: post
title: "Two Factor SSH with Google Authenticator"
description: ""
category: linux
tags: [Two Factor SSH]
---

- How to set up Two Factor SSH with Google Authenticator (on CentOS 6)

#### Download & Install
{% highlight bash %}
[root@test ~]# yum install make gcc pam-devel
[root@test ~]# yum install wget
[root@test ~]# yum install ntp
[root@test ~]# /etc/init.d/ntpd start
Starting ntpd:                                             [  OK  ]
[root@test ~]# chkconfig ntpd on
[root@test ~]# cd /opt/
[root@test opt]# wget https://google-authenticator.googlecode.com/files/libpam-google-authenticator-1.0-source.tar.bz2
--2014-09-11 15:11:58--  https://google-authenticator.googlecode.com/files/libpam-google-authenticator-1.0-source.tar.bz2
Resolving google-authenticator.googlecode.com... 173.194.72.82, 2404:6800:4008:c04::52
Connecting to google-authenticator.googlecode.com|173.194.72.82|:443... connected.
ERROR: cannot verify google-authenticator.googlecode.com’s certificate, issued by “/C=KR/ST=Seoul/L=Seoul/~~~~~~~”:
  Unable to locally verify the issuer’s authority.
To connect to google-authenticator.googlecode.com insecurely, use ‘--no-check-certificate’.
{% raw %}
## if you see above error, you need to use --no-check-certificate option 
[root@test opt]# wget --no-check-certificate https://google-authenticator.googlecode.com/files/libpam-google-authenticator-1.0-source.tar.bz2
{% endraw %}
[root@test opt]# bzip2 -d libpam-google-authenticator-1.0-source.tar.bz2
[root@test opt]# tar -xvf libpam-google-authenticator-1.0-source.tar
[root@test opt]# cd libpam-google-authenticator-1.0
[root@test libpam-google-authenticator-1.0]# 
[root@test libpam-google-authenticator-1.0]# make
[root@test libpam-google-authenticator-1.0]# make install
{% endhighlight %}

<br>

#### Set up Google Authenticator
{% highlight bash %}
{% raw %}
- Run “google-authenticator” as the user you want to log in with via SSH
[root@test libpam-google-authenticator-1.0]# su - ann
[ann@test ~]$ google-authenticator
Do you want authentication tokens to be time-based (y/n) y
https://www.google.com/chart?chs=200x200&chld=M|0&cht=qr&chl=otpauth://totp/ann@krbonstool01%3Fsecret%3DEFZSVUJOIT3373OR
Your new secret key is: EFZSVUJOIT3373OR
Your verification code is 343638
Your emergency scratch codes are:
  12537915
  43625506
  55016125
  81323386
  33075629
Do you want me to update your "/home/ann/.google_authenticator" file (y/n) y
Do you want to disallow multiple uses of the same authentication
token? This restricts you to one login about every 30s, but it increases
your chances to notice or even prevent man-in-the-middle attacks (y/n) y
By default, tokens are good for 30 seconds and in order to compensate for
possible time-skew between the client and the server, we allow an extra
token before and after the current time. If you experience problems with poor
time synchronization, you can increase the window from its default
size of 1:30min to about 4min. Do you want to do so (y/n) y
If the computer that you are logging into is not hardened against brute-force
login attempts, you can enable rate-limiting for the authentication module.
By default, this limits attackers to no more than 3 login attempts every 30s.
Do you want to enable rate-limiting (y/n) y
{% endraw %}
- Open the URL and then scan the QR code using the Google Authenticator application on your smartphone.
{% endhighlight %}

<br>

#### Configuration 
{% highlight bash %}
{% raw %}
- Enable Google authenticator for SSH login
[ann@test ~]$ exit
logout
[root@test ~]# 
[root@test ~]# vi /etc/pam.d/sshd
## add below line on the top.
auth       required     pam_google_authenticator.so
{% endraw %}
{% raw %}
- Open the SSH configuration file and set to yes for ‘ChallengeResponseAuthentication‘ line
[root@test ~]# vi /etc/ssh/sshd_config
ChallengeResponseAuthentication yes
{% endraw %}
{% raw %}
## restart sshd
[root@test ~]# service sshd restart
Stopping sshd:                                             [  OK  ]
Starting sshd:                                             [  OK  ]
{% endraw %}
{% endhighlight %}

#### NOTE

- If you cannot login with Verification code, you need to check SELINUX setting.

- If SELINUX is set as enforcing, it should be changed to permissive or disabled
{% highlight bash %}
[root@test ~]# vi /etc/selinux/config
SELINUX=permissive
{% endhighlight %}
