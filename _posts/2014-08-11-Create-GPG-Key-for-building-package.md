---
layout: post
title: "Create GPG Key for building package"
description: ""
category: linux
tags: [Create GPG Key for building package]
---

This is to provide how to generate GPG key for RPM build.


#### build user create
{% highlight bash %}
[root@test1 ~]# adduser annmoon
[root@test1 ~]# su - annmoon
[annmoon@test1 ~]$
{% endhighlight %}


#### start gpg-agent
{% highlight bash %}
[annmoon@test1 ~]$ rngd -r /dev/urandom
[annmoon@test1 ~]$ gpg-agent --use-standard-socket --daemon
gpg-agent[11340]: directory `/home/annmoon/.gnupg' created
gpg-agent[11340]: directory `/home/annmoon/.gnupg/private-keys-v1.d' created
GPG_AGENT_INFO=/root/.gnupg/S.gpg-agent:11341:1; export GPG_AGENT_INFO;
gpg-agent[11341]: gpg-agent (GnuPG) 2.0.14 started
{% endhighlight %}


#### Key create
{% highlight bash %}
[annmoon@test1 ~]$ cat <<EOF | gpg --batch --no-tty --gen-key
> %echo Generating a standard key
> Key-Type: default
> Key-Length: 2048
> Subkey-Type: default
> Subkey-Length: 2048
> Name-Real: Ann Moon
> Name-Email: annmoon@example.com
> Expire-Date: 5y
> Passphrase: 사용할 패스워드
> %commit
> %echo done
> EOF
gpg: keyring `/home/annmoon/.gnupg/secring.gpg' created
gpg: keyring `/home/annmoon/.gnupg/pubring.gpg' created
gpg: Generating a standard key
gpg: /home/annmoon/.gnupg/trustdb.gpg: trustdb created
gpg: key 34ECC635 marked as ultimately trusted
gpg: done
{% endhighlight %}


#### check gpg fingerprint
{% highlight bash %}
[annmoon@test1 ~]$ gpg --fingerprint
gpg: checking the trustdb
gpg: 3 marginal(s) needed, 1 complete(s) needed, PGP trust model
gpg: depth: 0  valid:   1  signed:   0  trust: 0-, 0q, 0n, 0m, 0f, 1u
gpg: next trustdb check due at 2019-07-30
/home/annmoon/.gnupg/pubring.gpg
------------------------
pub   2048R/34ECC635 2014-08-07 [expires: 2019-07-30]
      Key fingerprint = DABF 4D10 6691 1A4D 3A33  E5BE 83C5 9C90 34EC C635
uid                  Ann Moon <annmoon@example.com>
sub   2048R/F8AB3611 2014-08-07 [expires: 2019-07-30]
{% endhighlight %}


#### export key file
{% highlight bash %}
[annmoon@test1 ~]$ gpg -a -o RPM-GPG-KEY-builduser --export 34ECC635
[annmoon@test1 ~]$ cat RPM-GPG-KEY-builduser
-----BEGIN PGP PUBLIC KEY BLOCK-----
Version: GnuPG v2.0.14 (GNU/Linux) 
mQENBFPjJvgBCACyUcuJUGaaBiUG70lf/mKHORj4d0sInhjaDjfvtWRA59b+opPI
DnzeFL3Mkdgg72yUsavNv3sAwC9RiHstCIlIL5Nq4VXF3sP9Lui8IYXhVC3DFnh/
~~~~ SKIP
-----END PGP PUBLIC KEY BLOCK-----
{% endhighlight %}


#### If you want to use this key to build RPM, you need to put this line into .rpmmacros
{% highlight bash %}
[annmoon@test1 ~]$ echo '%_gpg_name 34ECC635' >> ~/.rpmmacros
[annmoon@test1 ~]$ cat ~/.rpmmacros
%_gpg_name 34ECC635
{% endhighlight %}
