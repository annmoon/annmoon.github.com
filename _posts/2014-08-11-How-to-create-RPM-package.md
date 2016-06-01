---
layout: post
title: "How to create RPM package"
description: ""
category: linux
tags:
---

* This is to provide how to create RPM package. it is tested on centos 6.5

<br>

#### Create rpm build ENV

##### 1) check if rpm-build is installed

{% highlight bash %}
## check if rpm-build is installed.
[root@test1 ~]# rpm -qa rpm-build
rpm-build-4.8.0-37.el6.x86_64
## If not installed, install this package at first.
[root@test1 ~]# yum install rpm-build
{% endhighlight %}


##### 2) login as build user / create directory 

{% highlight bash %}
## my build user is annmoon
[root@test1 ~]# su - annmoon
[annmoon@test1 ~]$
[annmoon@test1 ~]$ mkdir -p ~/rpmbuild/{RPMS,SRPMS,BUILD,SOURCES,SPECS,tmp}
[annmoon@test1 ~]$ cat <<EOF >~/.rpmmacros
> %_topdir   %(echo $HOME)/rpmbuild
> %_tmppath  %{_topdir}/tmp
> EOF
[annmoon@test1 ~]$ 
[annmoon@test1 ~]$ cd ~/rpmbuild
[annmoon@test1 rpmbuild]$ 
{% endhighlight %}

<br>

#### Create source file

{% highlight bash %}
## to test building package, please make simple shell script.
[annmoon@test1 rpmbuild]$ mkdir hello-test-1.0.0
[annmoon@test1 rpmbuild]$ cat <<EOF > hello-test-1.0.0/hello-test
> #!/bin/bash
> echo hello!
> EOF
[annmoon@test1 rpmbuild]$ sh hello-test-1.0.0/hello-test
hello!
[annmoon@test1 rpmbuild]$ 
[annmoon@test1 rpmbuild]$ tar czvf hello-test-1.0.0.tar.gz hello-test-1.0.0/
hello-test-1.0.0/
hello-test-1.0.0/hello-test
{% endhighlight %}

<br>

#### Create SPEC file

{% highlight bash %}
{% raw %}
[annmoon@test1 rpmbuild]$ cp hello-test-1.0.0.tar.gz SOURCES/
[annmoon@test1 rpmbuild]$ vi SPECS/hello-test.spec
{% endraw %}
{% raw %}
Name: hello-test
Version: 1.0.0
{% endraw %}
{% raw %}
Release:        1%{?dist}
Summary: A hello test package
{% endraw %}
{% raw %}
Group:  Testing
License: GPL
URL: http://ann-moon.tistory.com
Source0: %{name}-%{version}.tar.gz
BuildRoot:      %(mktemp -ud %{_tmppath}/%{name}-%{version}-%{release}-XXXXXX)
{% endraw %}
{% raw %}
%description
A hello test package
{% endraw %}
{% raw %}
%prep
%setup -q
{% endraw %}
{% raw %}
%build
{% endraw %}
{% raw %}
%install
rm -rf $RPM_BUILD_ROOT
mkdir -p $RPM_BUILD_ROOT/tmp/bin
cp hello-test $RPM_BUILD_ROOT/tmp/bin
{% endraw %}
{% raw %}
%clean
rm -rf $RPM_BUILD_ROOT
{% endraw %}
{% raw %}
%files
%defattr(-,root,root,-)
%attr(0755,root,root)/tmp/bin/hello-test
{% endraw %}
{% raw %}
%changelog
* Mon Aug 11 2014 Ann Moon <annmoon@example.com> - 1.0.0
- Initial RPM
{% endraw %}
{% endhighlight %}

<br>

#### Build package


* If you do not have GPG key, go to <a href="/2014/08/11/Create-GPG-Key-for-building-package/">HERE</a> to create GPG key.


{% highlight bash %}
[annmoon@test1 rpmbuild]$ rpmbuild --sign -ba SPECS/hello-test.spec                                                                                                                                    
Enter pass phrase: 
Pass phrase is good.
Executing(%prep): /bin/sh -e /home/annmoon/rpmbuild/tmp/rpm-tmp.U6255E
+ umask 022
+ cd /home/annmoon/rpmbuild/BUILD
+ LANG=C
+ export LANG
+ unset DISPLAY
+ cd /home/annmoon/rpmbuild/BUILD
+ rm -rf hello-test-1.0.0
+ /usr/bin/gzip -dc /home/annmoon/rpmbuild/SOURCES/hello-test-1.0.0.tar.gz
+ /bin/tar -xf -
+ STATUS=0
+ '[' 0 -ne 0 ']'
+ cd hello-test-1.0.0
+ /bin/chmod -Rf a+rX,u+w,g-w,o-w .
+ exit 0
Executing(%build): /bin/sh -e /home/annmoon/rpmbuild/tmp/rpm-tmp.jKR5Uh
+ umask 022
+ cd /home/annmoon/rpmbuild/BUILD
+ cd hello-test-1.0.0
{% raw %} 
~~~~~~~~ (SKIP)
{% endraw %}
{% raw %}
Processing files: hello-test-1.0.0-1.el6.x86_64
Requires(rpmlib): rpmlib(CompressedFileNames) <= 3.0.4-1 rpmlib(FileDigests) <= 4.6.0-1 rpmlib(PayloadFilesHavePrefix) <= 4.0-1
Processing files: hello-test-debuginfo-1.0.0-1.el6.x86_64
Checking for unpackaged file(s): /usr/lib/rpm/check-files /home/annmoon/rpmbuild/BUILDROOT/hello-test-1.0.0-1.el6.x86_64
Generating signature: 1005
Wrote: /home/annmoon/rpmbuild/SRPMS/hello-test-1.0.0-1.el6.src.rpm
Generating signature: 1005
Wrote: /home/annmoon/rpmbuild/RPMS/x86_64/hello-test-1.0.0-1.el6.x86_64.rpm
Generating signature: 1005
Wrote: /home/annmoon/rpmbuild/RPMS/x86_64/hello-test-debuginfo-1.0.0-1.el6.x86_64.rpm
Executing(%clean): /bin/sh -e /home/annmoon/rpmbuild/tmp/rpm-tmp.BjJwQC
+ umask 022
+ cd /home/annmoon/rpmbuild/BUILD
+ cd hello-test-1.0.0
+ rm -rf /home/annmoon/rpmbuild/BUILDROOT/hello-test-1.0.0-1.el6.x86_64
+ exit 0
{% endraw %}
{% raw %}
## check the RPM
[annmoon@test1 rpmbuild]$ ls -al RPMS/x86_64/
total 16
drwxr-xr-x. 2 annmoon annmoon 4096 Aug 11 06:19 .
drwxrwxr-x. 3 annmoon annmoon 4096 Aug 11 06:19 ..
-rw-rw-r--. 1 annmoon annmoon 2276 Aug 11 06:19 hello-test-1.0.0-1.el6.x86_64.rpm
{% endraw %}
{% endhighlight %}
