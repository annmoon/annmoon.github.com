---
layout: post
title: "OpenLDAP SSL Certificates"
description: ""
category: LDAP
tags:
---

### Create SSL Certificates

{% highlight bash %}
[root@local ~]# cd /etc/pki/tls/certs 

[root@local certs]# make server.key 
umask 77 ; \
/usr/bin/openssl genrsa -aes128 2048 > server.key 
Generating RSA private key, 2048 bit long modulus
......................................................++++++
.............++++++
e is 61251 (0x10001)
Enter pass phrase:# set passphrase
Verifying - Enter pass phrase:# confirm
# remove passphrase from private key
[root@local certs]# openssl rsa -in server.key -out server.key 
Enter pass phrase for server.key:# input passphrase
writing RSA key
[root@local certs]#

[root@local certs]# make server.csr 
umask 77 ; \
/usr/bin/openssl req -utf8 -new -key server.key -out server.csr
You are about to be asked to enter information that will be incorporated
into your certificate request.
What you are about to enter is what is called a Distinguished Name or a DN.
There are quite a few fields but you can leave some blank
For some fields there will be a default value,
If you enter '.', the field will be left blank.
-----
Country Name (2 letter code) [XX]:KR
State or Province Name (full name) [e]:Seoul
Locality Name (eg, city) [Default City]:Seoul
Organization Name (eg, company) [Default Company Ltd]:your company
Organizational Unit Name (eg, section) []:your department
Common Name (eg, your server's hostname) []:ldap.annmoon.com
Email Address []:admin@annmoon.com
Please enter the following 'extra' attributes
to be sent with your certificate request
A challenge password []:# Enter
An optional company name []:# Enter
[root@local certs]#

[root@local certs]# openssl x509 -in server.csr -out server.crt -req -signkey server.key -days 3650
Signature ok
subject=/C=KR/ST=Seoul/L=Seoul/O=xxxx/OU=xxx/CN=ldap.annmoon.com/emailAddress=admin@annmoon.com Getting Private key
[root@local certs]# chmod 400 server.*
{% endhighlight %}

* reference: <https://www.server-world.info/en/note?os=CentOS_6&p=ssl>