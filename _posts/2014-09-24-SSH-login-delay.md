---
layout: post
title: "SSH login delay"
description: ""
category: linux
tags:
---

* How to fix once ssh login delay.


##### 1) Disable reverse dns lookups in ssh config. (for All linux)

{% highlight bash %}
{% raw %}
root@test:~# vi /etc/ssh/sshd_config
{% endraw %}
{% raw %}
## add this line if it is not.
UseDNS no
{% endraw %}
{% raw %}
root@test:~# service ssh restart
ssh stop/waiting
ssh start/running, process 23813
{% endraw %}
{% endhighlight %}

<br>

##### 2) Disable GSSAPIAuthentication (Ubuntu) 

{% highlight bash %}
{% raw %}
root@test:~# vi /etc/ssh/sshd_config
{% endraw %}
{% raw %} 
## change to no from yes
    GSSAPIAuthentication no
{% endraw %}
{% raw %}
root@test:~# service ssh restart
ssh stop/waiting
ssh start/running, process 24193
{% endraw %}
{% endhighlight %}
