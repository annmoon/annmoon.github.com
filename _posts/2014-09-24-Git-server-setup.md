---
layout: post
title: "GIT server setup / clone on client"
description: ""
category: linux
tags:
---

#### Install (Server)

{% highlight bash %}
[root@test ~]# yum install git
{% endhighlight %}

<br>

#### first set up (Server)

{% highlight bash %}
{% raw %}
## setup user name / email
[root@test ~]# git config --global user.name "Ann Moon"
[root@test ~]# git config --global user.email annmoon@test.com
{% endraw %}
{% raw %}
## If you want to use diff tool to resolve merge conflicts, 
[root@test ~]# git config --global merge.tool vimdiff
{% endraw %}
{% raw %}
## Checking Your Settings
[root@test ~]# git config --list
{% endraw %}
{% raw %}
## create init repository
[root@test ~]# mkdir -p /var/git
[root@test ~]# chmod -R 777 /var/git
[root@test ~]# cd /var/git
[root@test git]# git init
Initialized empty Git repository in /var/git/.git/
{% endraw %}
{% raw %}
## create test file and commit
[root@test git]# touch index.html
[root@test git]# git add index.html
[root@test git]# git commit -m "first file"
[master (root-commit) 1e421b4] first file
 0 files changed, 0 insertions(+), 0 deletions(-)
 create mode 100644 index.html
{% endraw %}
{% raw %}
## create bare repository
[root@test git]# git clone --bare my_project my_project.git
{% endraw %}
{% endhighlight %}

<br>

#### using git (Client)

{% highlight bash %}
{% raw %}
## on client (working pc)
[root@annmoon ~]# git clone annmoon@server_ip or domain:/var/git/my_project.git
{% endraw %}
{% raw %}
## note) if you commit and push at first time, you should use --set-upstream origin master on push command like, 
[annmoon@annmoon my_project]$ git push --set-upstream origin master
{% endraw %}
{% endhighlight %}

<br>

* Reference:

<a href="http://stackoverflow.com/questions/315911/git-for-beginners-the-definitive-practical-guide">http://stackoverflow.com/questions/315911/git-for-beginners-the-definitive-practical-guide</a>

<a href="http://git-scm.com/book/en/Getting-Started-Installing-Git">http://git-scm.com/book/en/Getting-Started-Installing-Git</a>

<a href="http://git-scm.com/book/en/Getting-Started-First-Time-Git-Setup">http://git-scm.com/book/en/Getting-Started-First-Time-Git-Setup</a>

<a href="http://rogerdudler.github.io/git-guide/index.ko.html">http://rogerdudler.github.io/git-guide/index.ko.html</a>

