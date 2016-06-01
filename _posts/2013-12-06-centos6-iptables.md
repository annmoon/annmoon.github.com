---
layout: post
title: "CentOS Iptables"
description: ""
category: linux
tags: [network]
---

- in case open 8080 port.
{% highlight bash %}
[root@ServerA ~]# vi /etc/sysconfig/iptables
{% raw %}
# add below line
-A INPUT -m state --state NEW -m tcp -p tcp --dport 8080 -j ACCEPT
{% endraw %}
{% raw %}
# restart iptable
[root@ServerA ~]# /sbin/iptables -L -v -n
{% endraw %}
{% endhighlight %}

