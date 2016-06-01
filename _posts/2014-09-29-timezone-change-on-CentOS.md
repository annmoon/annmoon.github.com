---
layout: post
title: "timezone change on CentOS"
description: ""
category: linux
tags:
---

* timezone change on CentOS

{% highlight bash %}
{% raw %}
## Change to UTC
[root@test ~]# rm /etc/localtime
[root@test ~]# ln -s /usr/share/zoneinfo/UTC /etc/localtime
{% endraw %}
{% raw %} 
## You can find other timezone on /usr/share/zoneinfo/
{% endraw %}
{% raw %}
[root@test ~]# vi /etc/sysconfig/clock
## edit ZONE value
ZONE="Etc/UTC"
{% endraw %}
{% endhighlight %}

<br>

* After change tomezone on server, you should restart all the daemons your server actively uses.
