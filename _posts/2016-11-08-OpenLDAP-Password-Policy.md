---
layout: post
title: "OpenLDAP Password Policy"
description: ""
category: LDAP
tags:
---

> Password policy module allows to define policies for the userPassword attribute.

### Configuration with OLC

###### load [ppolicy schema](https://github.com/annmoon/OpenLDAP/blob/master/LDIF/ppolicy.ldif "ppolicy schema") into OLC if it's not loaded yet.

{% highlight bash %}
# ldapmodify -D "cn=root,cn=config" -W -a -f /etc/openldap/schema/ppolicy.ldif
{% endhighlight %}