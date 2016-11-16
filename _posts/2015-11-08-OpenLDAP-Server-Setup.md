---
layout: post
title: "OpenLDAP server set up on centos 6"
description: ""
category: LDAP
tags:
---

* This DOC is for basic openldap server setting. For TLS, sudoers, password policy setting, you can find another posting.

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

<br>

**2) Setting domain name on LDAP DB**
{% highlight bash %}
# generate directory manager's password
[root@local ~]# slappasswd 
New password:
Re-enter new password:
{SSHA}xxxxxxxxxxxxxxxxxxxxxxxx

[root@local ~]# vi chdomain.ldif
# replace to your own domain name for "dc=***,dc=***" section
# specify the password generated above for "olcRootPW" section
dn: olcDatabase={1}monitor,cn=config
changetype: modify
replace: olcAccess
olcAccess: {0}to * by dn.base="gidNumber=0+uidNumber=0,cn=peercred,cn=external,cn=auth"
  read by dn.base="cn=Manager,dc=annmoon,dc=com" read by * none

dn: olcDatabase={2}bdb,cn=config
changetype: modify
replace: olcSuffix
olcSuffix: dc=annmoon,dc=com

dn: olcDatabase={2}bdb,cn=config
changetype: modify
replace: olcRootDN
olcRootDN: cn=Manager,dc=annmoon,dc=com

dn: olcDatabase={2}bdb,cn=config
changetype: modify
add: olcRootPW
olcRootPW: {SSHA}xxxxxxxxxxxxxxxxxxxxxxxx

dn: olcDatabase={2}bdb,cn=config
changetype: modify
add: olcAccess
olcAccess: {0}to attrs=userPassword,shadowMax,email by dn="cn=Manager,dc=annmoon,dc=com" write by anonymous auth by self write by * none
olcAccess: {1}to attrs=shadowLastChange by dn="cn=Manager,dc=annmoon,dc=com" write by anonymous auth by self write by * read
olcAccess: {2}to dn.base="" by * read
olcAccess: {3}to * by dn="cn=Manager,dc=annmoon,dc=com" write by * read

[root@local ~]# ldapmodify -Y EXTERNAL -H ldapi:/// -f chdomain.ldif 

[root@local ~]# vi basedomain.ldif
# replace to your own domain name for "dc=***,dc=***" section
dn: dc=annmoon,dc=com
objectClass: top
objectClass: dcObject
objectclass: organization
o: annmoon world
dc: annmoon

dn: cn=Manager,dc=annmoon,dc=com
objectClass: organizationalRole
cn: Manager
description: Directory Manager

dn: ou=People,dc=annmoon,dc=com
objectClass: organizationalUnit
ou: People

dn: ou=groups,dc=annmoon,dc=com
objectClass: organizationalUnit
ou: groups

dn: cn=test,ou=groups,dc=annmoon,dc=com
objectClass: top
objectClass: posixGroup
cn: test
gidNumber: 200
memberUid: testuser


dn: cn=testuser,ou=People,dc=annmoon,dc=com
objectClass: extensibleObject
objectClass: shadowAccount
objectClass: posixAccount
objectClass: top
objectClass: person
objectClass: organizationalPerson
objectClass: inetOrgPerson
cn: testuser
gidNumber: 200
homeDirectory: /home/testuser
sn: testuser
uid: testuser
uidNumber: 2001
email: annmoon@test.com
gecos: testuser
host: *
loginShell: /bin/bash
userPassword: {ssha}x

[root@local ~]# ldapadd -x -D cn=Manager,dc=annmoon,dc=com -W -f basedomain.ldif 
{% endhighlight %}