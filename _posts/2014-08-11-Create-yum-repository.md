---
layout: post
title: "Create yum repository"
description: ""
category: linux
tags:
---

* This is to provide how to create YUM repository. it is tested on CentOS 6.5

<br>

#### Preparation

##### 1) Create RPM GPG Key

* If you do not have GPG key, go to <a href="/2014/08/11/Create-GPG-Key-for-building-package/">HERE</a> to create GPG key.


##### 2) Install createrepo 

{% highlight bash %}
{% raw %}
## check if package is installed.
[root@test1 ~]# rpm -qa createrepo
createrepo-0.9.9-18.el6.noarch
{% endraw %}
{% raw %}
 ## if not installed, install createrepo
[root@test1 ~]# yum install createrepo
{% endraw %}
{% endhighlight %}

<br>

##### 3) Install apache 

{% highlight bash %}
{% raw %}
## check if package is installed.
[root@test1 ~]# rpm -qa httpd
httpd-2.2.15-29.el6.centos.x86_64
{% endraw %}
{% raw %}
## if not installed, install httpd
[root@test1 ~]# yum install httpd
{% endraw %}
{% raw %}
## apache start
[root@test1 ~]# service httpd start
{% endraw %}
{% raw %}
## for detail configuration of apache, please see this - http://httpd.apache.org/docs/2.2/configuring.html
{% endraw %}
{% endhighlight %}

<br>

#### Create repository directory.

{% highlight bash %}
{% raw %}
[root@test1  ~]# mkdir -p /var/www/html/repo/Packages
[root@test1 ~]# cp /home/annmoon/rpmbuild/RPMS/x86_64/hello-test-1.0.0-1.el6.x86_64.rpm /var/www/html/repo/Packages/
## annmoon is rpm build user which is created on http://ann-moon.tistory.com/109
{% endraw %}
{% raw %}
[root@test ~]# createrepo -v /var/www/html/repo/
Spawning worker 0 with 1 pkgs
Worker 0: reading Packages/hello-test-1.0.0-1.el6.x86_64.rpm
Workers Finished
Gathering worker results
{% endraw %}
{% raw %}
Saving Primary metadata
Saving file lists metadata
Saving other metadata
Generating sqlite DBs
Starting other db creation: Thu Sep 11 08:27:33 2014
Ending other db creation: Thu Sep 11 08:27:33 2014
Starting filelists db creation: Thu Sep 11 08:27:33 2014
Ending filelists db creation: Thu Sep 11 08:27:33 2014
Starting primary db creation: Thu Sep 11 08:27:33 2014
Ending primary db creation: Thu Sep 11 08:27:34 2014
Sqlite DBs complete
{% endraw %}
{% raw %}
[root@test1 ~]# cp /home/annmoon/rpmbuild/RPM-GPG-KEY-builduser /var/www/html/repo/
{% endraw %}
{% raw %}
## check RPM-GPG-KEY-builduser via web 
## (my http port is 8080. If you just set up by 80, use curl http://127.0.0.1/repo/RPM-GPG-KEY-builduser)
[root@KRBONSTEST1 ~]# curl http://127.0.0.1:8080/repo/RPM-GPG-KEY-builduser
-----BEGIN PGP PUBLIC KEY BLOCK-----
Version: GnuPG v2.0.14 (GNU/Linux)
~~~~ SKIP
-----END PGP PUBLIC KEY BLOCK-----
{% endraw %}
{% endhighlight %}

<br>

#### Verify repository

##### 1) connection test from other computer.

{% highlight bash %}
{% raw %}
## from test2
[root@test2 ~]# nc -z test1.test.com 8080
Connection to test1.test.com 8080 port [tcp/webcache] succeeded!
{% endraw %}
{% raw %}
[root@test2 ~]# curl http://test1.test.com:8080/repo/RPM-GPG-KEY-builduser
-----BEGIN PGP PUBLIC KEY BLOCK-----
Version: GnuPG v2.0.14 (GNU/Linux)
~~~ SKIP
-----END PGP PUBLIC KEY BLOCK-----
{% endraw %}
{% endhighlight %}

<br>

##### 2) add repository

{% highlight bash %}
{% raw %}
## from test2
[root@test2 ~]# echo "[test1-repo]
> name=Test1 Repo
> baseurl=http://test1.test.com:8080/repo
> gpgkey=http://test1.test.com:8080/repo/RPM-GPG-KEY-builduser
> enabled=1
> gpgcheck=1" > /etc/yum.repos.d/test1-repo.repo
{% endraw %}
{% raw %}
## check if test1-repo's status. it will have 1 pkg.
[root@test2 yum.repos.d]# yum repolist
{% endraw %}
{% endhighlight %}

<br>

#### 3) install test package

{% highlight bash %}
{% raw %}
[root@test2 ~]# yum install hello-test
Loaded plugins: fastestmirror
Loading mirror speeds from cached hostfile
 * base: ftp.riken.jp
 * extras: ftp.riken.jp
 * updates: ftp.nara.wide.ad.jp
Setting up Install Process
Resolving Dependencies
--> Running transaction check
---> Package hello-test.x86_64 0:1.0.0-1.el6 will be installed
--> Finished Dependency Resolution
{% endraw %}
{% raw %}
Dependencies Resolved
{% endraw %}
{% raw %}
==================================================================================================================================================================================================
 Package                                         Arch                                        Version                                             Repository                                             Size
==================================================================================================================================================================================================
Installing:
 hello-test                                      x86_64                                      1.0.0-1.el6                                         krbonstest1-repo                                      2.8 k
{% endraw %}
{% raw %}
Transaction Summary
==================================================================================================================================================================================================
Install       1 Package(s)
{% endraw %}
{% endhighlight %}

