---
layout: post
title: "Linux network setting"
description: ""
category: linux
tags: [network]
---

1) use DHCP on eht0
{% highlight bash %}
[root@ServerA ~]# vi /etc/sysconfig/network-scripts/ifcfg-eth0 
DEVICE=eth0
HWADDR=00:0C:29:DF:3A:CA
TYPE=Ethernet
UUID=9fbb27c3-b7e2-4171-8d4b-45bcdbfd6723
ONBOOT=yes
NM_CONTROLLED=yes
 
## BOOTPROTO setting
BOOTPROTO=dhcp
{% endhighlight %}

2) use Static IP
{% highlight bash %}
[root@ServerA ~]# vi /etc/sysconfig/network-scripts/ifcfg-eth0 
DEVICE=eth0
BOOTPROTO=none
HWADDR=00:0C:29:DF:3A:CA
ONBOOT=yes
NETMASK=255.255.255.0
IPADDR=192.168.101.134
GATEWAY=192.168.101.2
TYPE=Ethernet
USERCTL=no
IPV6INIT=no
PEERDNS=yes
{% endhighlight %}   

3) hostname, domain setting 
{% highlight bash %}
[root@ServerA ~]# vi /etc/sysconfig/network
NETWORKING=yes
HOSTNAME=ServerA
{% endhighlight %}

4) DNS setting
{% highlight bash %}
[root@ServerA ~]# vi /etc/resolv.conf
# Generated by NetworkManager
domain localdomain
search localdomain
nameserver 192.168.101.2
{% endhighlight %}

5) /etc/host.conf
{% highlight bash %}
# Lookup names via DNS first then fall back to /etc/hosts.
order bind,hosts
{% endhighlight %}

6) default gateway
{% highlight bash %}
sudo route del default gw 192.168.56.1 metric 0 eth2
sudo route add default gw 10.0.2.2 metric 0 eth0
{% endhighlight %}