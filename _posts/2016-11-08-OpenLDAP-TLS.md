---
layout: post
title: "OpenLDAP TLS setting"
description: ""
category: LDAP
tags:
---

### prerequisite

* [Create SSL certificate files](https://annmoon.github.io/2015/11/08/OpenLDAP-Cert/ "SSL")

### Configure LDAP Server TLS

{% highlight bash %}
[root@local ~]# cp /etc/pki/tls/certs/server.key \
/etc/pki/tls/certs/server.crt \
/etc/pki/tls/certs/ca-bundle.crt \
/etc/openldap/certs/ 
[root@local ~]# chown ldap:ldap /etc/openldap/certs/server.key \
/etc/openldap/certs/server.crt \
/etc/openldap/certs/ca-bundle.crt

[root@local ~]# vi mod_ssl.ldif
dn: cn=config
changetype: modify
add: olcTLSCACertificateFile
olcTLSCACertificateFile: /etc/openldap/certs/ca-bundle.crt
-
replace: olcTLSCertificateFile
olcTLSCertificateFile: /etc/openldap/certs/server.crt
-
replace: olcTLSCertificateKeyFile
olcTLSCertificateKeyFile: /etc/openldap/certs/server.key

[root@local ~]# ldapmodify -Y EXTERNAL -H ldapi:/// -f mod_ssl.ldif 
SASL/EXTERNAL authentication started
SASL username: gidNumber=0+uidNumber=0,cn=peercred,cn=external,cn=auth
SASL SSF: 0
modifying entry "cn=config"

[root@local ~]# vi /etc/sysconfig/ldap
# change below setting to yes
SLAPD_LDAPS=yes

[root@local ~]# /etc/init.d/slapd restart 
Stopping slapd:                    [  OK  ]
Starting slapd:                    [  OK  ]
{% endhighlight %}

<br>

### Configure LDAP Client TLS

{% highlight bash %}
[root@client ~]# echo "TLS_REQCERT allow" >> /etc/openldap/ldap.conf 
[root@client ~]# echo "tls_reqcert allow" >> /etc/nslcd.conf 
[root@client ~]# echo "tls_reqcert allow" >> /etc/pam_ldap.conf
[root@client ~]# authconfig --enableldaptls --update 
[root@client ~]# exit 

# try login as ldap account (like, testuser)

# ssh testuser@client
Password:
[testuser@client ~]$
{% endhighlight %}

* reference: <https://www.server-world.info/en/note?os=CentOS_6&p=ldap&f=3>