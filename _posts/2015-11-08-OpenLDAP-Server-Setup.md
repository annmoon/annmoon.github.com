---
layout: post
title: "OpenLDAP server set up on centos 6"
description: ""
category: LDAP
tags:
---

## Install package

{% highlight bash %}
[root@local ~]# yum install openldap-servers openldap-clients
[root@local ~]# cp /usr/share/openldap-servers/DB_CONFIG.example /var/lib/ldap/DB_CONFIG 
[root@local ~]# chown ldap. /var/lib/ldap/DB_CONFIG 
[root@local ~]# /etc/init.d/slapd start 
Starting slapd: [ OK ]

[root@local ~]# chkconfig slapd on
{% endhighlight %}

## setting up OpenLDAP

### Set OpenLDAP administrator password
{% highlight bash %}
[root@local ~]# slappasswd 
New password:
Re-enter new password:
{SSHA}xxxxxxxxxxxxxxxxxxxxxxxx
[root@local ~]# vi chrootpw.ldif
# specify the password generated above for "olcRootPW" section
dn: olcDatabase={0}config,cn=config
changetype: modify
add: olcRootPW
olcRootPW: {SSHA}xxxxxxxxxxxxxxxxxxxxxxxx

[root@local ~]# ldapadd -Y EXTERNAL -H ldapi:/// -f chrootpw.ldif 
SASL/EXTERNAL authentication started
SASL username: gidNumber=0+uidNumber=0,cn=peercred,cn=external,cn=auth
SASL SSF: 0
modifying entry "olcDatabase={0}config,cn=config"
{% endhighlight %}
