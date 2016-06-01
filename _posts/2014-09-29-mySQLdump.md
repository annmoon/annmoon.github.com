---
layout: post
title: "mysqldump (db backup/ restore)"
description: ""
category: mySQL
tags:
---

#### Data backup using mysqldump

{% highlight bash %}
{% raw %}
## whole DB backup
[root@test ~]# mysqldump -u USER_NAME -p DB_NAME > /tmp/DB_FILE_NAME.sql
{% endraw %}
{% raw %}
## table dump
[root@test ~]# mysqldump -u USER_NAME -p DB_NAME TABLE_NAME > /tmp/FILE_NAME.sql
{% endraw %}
{% raw %}
## specific data dump - field:no , value: 5~10 (-w option)
 [root@test ~]# mysqldump -u USER_NAME -p DB_NAME TABLE_NAME -w  'no=>5 and no=<10' > /tmp/FILE_NAME.sql
{% endraw %}
{% raw %}
## to create an empty copy of the db/table (-d option)
[root@test ~]# mysqldump -u USER_NAME -p -d DB_NAME > /tmp/FILE_NAME.sql
[root@test ~]# mysqldump -u USER_NAME -p -d DB_NAME TABLE_NAME > /tmp/FILE_NAME.sql
{% endraw %}
{% endhighlight %}

<br>

#### Data restore using mysqldump

{% highlight bash %}
[root@test ~]# mysql -u USER_NAME -p < /tmp/DB_FILE_NAME.sql
[root@test ~]# mysql -u USER_NAME -p DB_NAME < /tmp/FILE_NAME.sql
{% endhighlight %}
