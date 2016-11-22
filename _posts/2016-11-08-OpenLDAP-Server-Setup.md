---
layout: post
title: "OpenLDAP server set up on centos 6"
description: ""
category: LDAP
tags:
---

* This DOC is for basic openldap server setting. For [TLS](https://annmoon.github.io/2015/11/08/OpenLDAP-TLS/ "TLS"), sudoers, password policy setting, you can find another(link) posting.

### Install package

{% highlight bash %}
[root@local ~]# yum install openldap-servers openldap-clients
[root@local ~]# cp /usr/share/openldap-servers/DB_CONFIG.example /var/lib/ldap/DB_CONFIG 
[root@local ~]# chown ldap. /var/lib/ldap/DB_CONFIG 
[root@local ~]# /etc/init.d/slapd start 
Starting slapd: [ OK ]

[root@local ~]# chkconfig slapd on
{% endhighlight %}

<br>

### setting up OpenLDAP

**1) Setting OpenLDAP administrator password**

* [chrootpw.ldif](https://github.com/annmoon/OpenLDAP/blob/master/LDIF/chrootpw.ldif "chrootpw")
* [chdomain.ldif](https://github.com/annmoon/OpenLDAP/blob/master/LDIF/chdomain.ldif "chdomain")
* [basedomain.ldif](https://github.com/annmoon/OpenLDAP/blob/master/LDIF/basedomain.ldif "basedomain")

{% highlight bash %}
[root@local ~]# slappasswd 
New password:
Re-enter new password:
{SSHA}xxxxxxxxxxxxxxxxxxxxxxxx

[root@local ~]# ldapadd -Y EXTERNAL -H ldapi:/// -f chrootpw.ldif
SASL/EXTERNAL authentication started
SASL username: gidNumber=0+uidNumber=0,cn=peercred,cn=external,cn=auth
SASL SSF: 0
modifying entry "olcDatabase={0}config,cn=config"
{% endhighlight %}

<br>

**2) Setting domain name on LDAP DB**
{% highlight bash %}
# generate directory manager's password
[root@local ~]# slappasswd 
New password:
Re-enter new password:
{SSHA}xxxxxxxxxxxxxxxxxxxxxxxx

[root@local ~]# ldapmodify -Y EXTERNAL -H ldapi:/// -f chdomain.ldif

[root@local ~]# ldapadd -x -D cn=Manager,dc=annmoon,dc=com -W -f basedomain.ldif
{% endhighlight %}

<br>

**3) iptable check**
{% highlight bash %}
## if iptables is runnning, allow LDAP port (389 port)

[root@local ~]# iptables -I INPUT 5 -p tcp -m state --state NEW -m tcp --dport 389 -j ACCEPT 
{% endhighlight %}

* reference: <https://www.server-world.info/en/note?os=CentOS_6&p=ldap&f=1>