---
layout: post
title: "OpenLDAP Password Policy"
description: ""
category: LDAP
tags:
---

> Password policy module allows to define policies for the userPassword attribute.

### Configuration with OLC


#### 1) load [ppolicy schema](https://github.com/annmoon/OpenLDAP/blob/master/LDIF/ppolicy.ldif "ppolicy schema") into OLC and [ppolicymodule module](https://github.com/annmoon/OpenLDAP/blob/master/LDIF/ppolicymodule.ldif "ppolicymodule")

{% highlight bash %}
[root@local ~]# ldapmodify -D "cn=Manager,cn=config" -W -a -f /etc/openldap/schema/ppolicy.ldif
[root@local ~]# ldapmodify -D "cn=Manager,cn=config" -W -a -f ppolicymodule.ldif
{% endhighlight %}

#### 2) Configure [ppolicy overlay](https://github.com/annmoon/OpenLDAP/blob/master/LDIF/ppolicyOverlayConfig.ldif "overlay")

{% highlight bash %}
[root@local ~]# ldapmodify -D "cn=Manager,cn=config" -W -a -f ppolicyoverlay.ldif
{% endhighlight %}

#### 3) Definition of a [password policy](https://github.com/annmoon/OpenLDAP/blob/master/LDIF/pwd_policy.ldif "policy")

{% highlight bash %}
[root@local ~]# ldapadd -x -D "cn=Manager,dc=annmoon,dc=com" -W -f pwd_policy.ldif
{% endhighlight %}

> This policy applies to all userPassword attributes

> To see all parameters, please refer to [this](http://www.zytrax.com/books/ldap/ch6/ppolicy.html#pwdpolicyattributes "parameters")

> reference: <https://tobrunet.ch/articles/openldap-password-policy-overlay/> , <http://www.openldap.org/doc/admin24/overlays.html#Password%20Policies>

