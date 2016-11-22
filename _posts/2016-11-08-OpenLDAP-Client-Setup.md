---
layout: post
title: "OpenLDAP Client Setup"
description: ""
category: LDAP
tags:
---

### OpenLDAP Client Setup

{% highlight bash %}
[root@client ~]# yum install authconfig openldap-clients nss-pam-ldapd
[root@client ~]# authconfig --enableldap --enableldapauth --ldapserver=ldap.annmoon.com --ldapbasedn="dc=annmoon,dc=com" --enablemkhomedir --updateall
Starting nslcd:                          [  OK  ]
[root@client ~]# exit 

# try login as ldap account (like, testuser)

# ssh testuser@client
Password:
Creating directory '/home/testuser'.
[testuser@client ~]$
{% endhighlight %}

* reference: <https://www.server-world.info/en/note?os=CentOS_6&p=ldap&f=2>