---
layout: post
title: "How to Install and Configure MySQL Cluster on CentOS 7"
description: ""
category: mySQL
tags:
---

### Cluster nodes

| Role            | Hostname | OS       | IP           |
| --------------- | -------- | -------- | ------------ |
| Management Node | ndb_mgmd | CentOS 7 | 192.168.1.57 |
| Data Node 1     | ndb01    | CentOS 7 | 192.168.1.12 |
| Data Node 2     | ndb02    | CentOS 7 | 192.168.1.50 |
| API Node 1      | api01    | CentOS 7 | 192.168.1.51 |
| API Node 2      | api02    | CentOS 7 | 192.168.1.62 |

<br>

### Setup Management Node

#### 1) Download / install / remove packages

{% highlight bash %}
[root@ndb_mgmd ~]# cd /var/tmp
[root@ndb_mgmd tmp]# 
[root@ndb_mgmd tmp]# 
[root@ndb_mgmd tmp]# wget http://dev.mysql.com/get/Downloads/MySQL-Cluster-7.4/MySQL-Cluster-gpl-7.4.10-1.el7.x86_64.rpm-bundle.tar
--2016-06-01 08:55:44--  http://dev.mysql.com/get/Downloads/MySQL-Cluster-7.4/MySQL-Cluster-gpl-7.4.10-1.el7.x86_64.rpm-bundle.tar
Resolving dev.mysql.com (dev.mysql.com)... 137.254.60.11
Connecting to dev.mysql.com (dev.mysql.com)|137.254.60.11|:80... connected.
HTTP request sent, awaiting response... 302 Found
Location: http://cdn.mysql.com//Downloads/MySQL-Cluster-7.4/MySQL-Cluster-gpl-7.4.10-1.el7.x86_64.rpm-bundle.tar [following]
--2016-06-01 08:55:44--  http://cdn.mysql.com//Downloads/MySQL-Cluster-7.4/MySQL-Cluster-gpl-7.4.10-1.el7.x86_64.rpm-bundle.tar
Resolving cdn.mysql.com (cdn.mysql.com)... 104.76.138.251
Connecting to cdn.mysql.com (cdn.mysql.com)|104.76.138.251|:80... connected.
HTTP request sent, awaiting response... 200 OK
Length: 384563200 (367M) [application/x-tar]
Saving to: ‘MySQL-Cluster-gpl-7.4.10-1.el7.x86_64.rpm-bundle.tar’

[root@ndb_mgmd tmp]# tar xvf MySQL-Cluster-gpl-7.4.10-1.el7.x86_64.rpm-bundle.tar
MySQL-Cluster-client-gpl-7.4.10-1.el7.x86_64.rpm
MySQL-Cluster-test-gpl-7.4.10-1.el7.x86_64.rpm
MySQL-Cluster-devel-gpl-7.4.10-1.el7.x86_64.rpm
MySQL-Cluster-embedded-gpl-7.4.10-1.el7.x86_64.rpm
MySQL-Cluster-server-gpl-7.4.10-1.el7.x86_64.rpm
MySQL-Cluster-shared-compat-gpl-7.4.10-1.el7.x86_64.rpm
MySQL-Cluster-shared-gpl-7.4.10-1.el7.x86_64.rpm
{% endhighlight %}

* if you did not install perl-Data-Dumper yet, you should install first and remove mariadb-libs package.

{% highlight bash %}
[root@ndb_mgmd tmp]# yum -y install perl-Data-Dumper
[root@ndb_mgmd tmp]# yum -y remove mariadb-libs
[root@ndb_mgmd tmp]# rpm -Uvh MySQL-Cluster-client-gpl-7.4.10-1.el7.x86_64.rpm
[root@ndb_mgmd tmp]# rpm -Uvh MySQL-Cluster-server-gpl-7.4.10-1.el7.x86_64.rpm
[root@ndb_mgmd tmp]# rpm -Uvh MySQL-Cluster-shared-gpl-7.4.10-1.el7.x86_64.rpm
{% endhighlight %}

<br>

#### 2) Configure MySQL Cluster

{% highlight bash %}
[root@ndb_mgmd tmp]# mkdir -p /var/lib/mysql-cluster
[root@ndb_mgmd tmp]# cd /var/lib/mysql-cluster
[root@ndb_mgmd mysql-cluster]# vi config.ini
{% endhighlight %}

* Paste the below configuration on config.ini

{% highlight bash %}
[ndb_mgmd default]
# Directory for MGM node log files
DataDir=/var/lib/mysql-cluster

[ndb_mgmd]
#Management Node
HostName=192.168.1.57

[ndbd default]
NoOfReplicas=2      # Number of replicas
DataMemory=256M     # Memory allocate for data storage
IndexMemory=128M    # Memory allocate for index storage
#Directory for Data Node
DataDir=/var/lib/mysql-cluster

[ndbd]
#Data Node 1
HostName=192.168.1.12

[ndbd]
#Data Node 2
HostName=192.168.1.50

[mysqld]
#SQL(api) Node 1
HostName=192.168.1.51

[mysqld]
#SQL(api) Node 2
HostName=192.168.1.62
{% endhighlight %}

<br>

#### 3) Start the Management Node

{% highlight bash %}
[root@ndb_mgmd mysql-cluster]# ndb_mgmd --config-file=/var/lib/mysql-cluster/config.ini
MySQL Cluster Management Server mysql-5.6.28 ndb-7.4.10
2016-06-01 09:18:25 [MgmtSrvr] INFO     -- The default config directory '/usr/mysql-cluster' does not exist. Trying to create it...
2016-06-01 09:18:25 [MgmtSrvr] INFO     -- Sucessfully created config directory
{% endhighlight %}

<br>

#### 4) monitor the node

{% highlight bash %}
[root@ndb_mgmd mysql-cluster]# ndb_mgm
-- NDB Cluster -- Management Client --
ndb_mgm> show
Connected to Management Server at: localhost:1186
Cluster Configuration
---------------------
[ndbd(NDB)]     2 node(s)
id=2 (not connected, accepting connect from 192.168.1.12)
id=3 (not connected, accepting connect from 192.168.1.50)

[ndb_mgmd(MGM)] 1 node(s)
id=1    @192.168.1.57  (mysql-5.6.28 ndb-7.4.10)

[mysqld(API)]   2 node(s)
id=4 (not connected, accepting connect from 192.168.1.51)
id=5 (not connected, accepting connect from 192.168.1.62)

ndb_mgm> 
{% endhighlight %}

<br>

### Setup the MySQL Cluster Data Nodes

#### 1) Download / install / remove packages

* login as root user to Data Node 1 (192.168.1.12)

{% highlight bash %}
[root@ndb01 ~]# cd /var/tmp
[root@ndb01 tmp]# wget http://dev.mysql.com/get/Downloads/MySQL-Cluster-7.4/MySQL-Cluster-gpl-7.4.10-1.el7.x86_64.rpm-bundle.tar
[root@ndb01 tmp]# tar xvf MySQL-Cluster-gpl-7.4.10-1.el7.x86_64.rpm-bundle.tar
[root@ndb01 tmp]# yum -y install perl-Data-Dumper
[root@ndb01 tmp]# yum -y remove mariadb-libs

[root@ndb01 tmp]# rpm -Uvh MySQL-Cluster-client-gpl-7.4.10-1.el7.x86_64.rpm
[root@ndb01 tmp]# rpm -Uvh MySQL-Cluster-server-gpl-7.4.10-1.el7.x86_64.rpm
[root@ndb01 tmp]# rpm -Uvh MySQL-Cluster-shared-gpl-7.4.10-1.el7.x86_64.rpm
{% endhighlight %} 

<br>

#### 2) Configure Data Node

{% highlight bash %}
[root@ndb01 tmp]# vi /etc/my.cnf
{% endhighlight %}

* Paste configuration below:

{% highlight bash %}
[mysqld]
ndbcluster
ndb-connectstring=192.168.1.57     # IP address of Management Node

[mysql_cluster]
ndb-connectstring=192.168.1.57     # IP address of Management Node
{% endhighlight %}

* Then create the new directory for the database data that we defined in the management node config file "config.ini" and then start data node.

{% highlight bash %}
[root@ndb01 tmp]# mkdir -p /var/lib/mysql-cluster
[root@ndb01 tmp]# ndbd
2016-06-02 01:38:34 [ndbd] INFO     -- Angel connected to '192.168.1.57:1186'
2016-06-02 01:38:34 [ndbd] INFO     -- Angel allocated nodeid: 2
{% endhighlight %}

* for Data Node 1 (192.168.1.50), do same things (1,2)

<br>

### Setup SQL(API) Node

#### 1) Download / install / remove packages

* login as root user to API node 1 (192.158.1.51)

{% highlight bash %}
[root@api01 ~]# cd /var/tmp
[root@api01 tmp]# wget http://dev.mysql.com/get/Downloads/MySQL-Cluster-7.4/MySQL-Cluster-gpl-7.4.10-1.el7.x86_64.rpm-bundle.tar
[root@api01 tmp]# tar xvf MySQL-Cluster-gpl-7.4.10-1.el7.x86_64.rpm-bundle.tar
[root@api01 tmp]# yum -y install perl-Data-Dumper
[root@api01 tmp]# yum -y remove mariadb-libs
[root@api01 tmp]# rpm -Uvh MySQL-Cluster-client-gpl-7.4.10-1.el7.x86_64.rpm
[root@api01 tmp]# rpm -Uvh MySQL-Cluster-server-gpl-7.4.10-1.el7.x86_64.rpm
[root@api01 tmp]# rpm -Uvh MySQL-Cluster-shared-gpl-7.4.10-1.el7.x86_64.rpm
{% endhighlight %}

<br>

#### 2) Configure the SQL Node

{% highlight bash %}
[root@api01 tmp]# vi /etc/my.cnf
{% endhighlight %}

* paste configuration below:

{% highlight bash %}
[mysqld]
ndbcluster
ndb-connectstring=192.168.1.57       # IP address for server management node
default_storage_engine=ndbcluster     # Define default Storage Engine used by MySQL

[mysql_cluster]
ndb-connectstring=192.168.1.57       # IP address for server management node
{% endhighlight %}

* Start SQL node

{% highlight bash %}
[root@api01 tmp]# service mysql start
Starting MySQL................................ SUCCESS!
{% endhighlight %}

* Check the status on Management Node. If api node is unable to connect as below, check the mysql err log on api.

{% highlight bash %}
[mysqld(API)] 2 node(s)
id=4 (not connected, accepting connect from any host)
{% endhighlight %}

* if you see below logs on /var/lib/mysql/{hostname}.err, you can try to stop mysql process and then start with below command line.

{% highlight text %}
2016-06-02 01:54:47 11362 [Note] NDB: NodeID is 4, management server '192.168.1.57:1186'
2016-06-02 01:55:17 11362 [Note] NDB[0]: NodeID: 4, no storage nodes connected (timed out)
2016-06-02 01:55:17 11362 [Warning] NDB: server id set to zero - changes logged to bin log with server id zero will be logged with another server id by slave mysqlds
{% endhighlight %}

{% highlight bash %}
[root@api01 ~]# service mysqld stop
[root@api01 ~]# /bin/sh /usr/bin/mysqld_safe --datadir=/var/lib/mysql --user=mysql --ndbcluster --ndb-connectstring=192.168.1.57:1186&
{% endhighlight %}

* For API Node 2 (192.168.1.62), do same things (1,2)

<br>

#### 3) check status on management node

{% highlight bash %}
ndb_mgm> show
Cluster Configuration
---------------------
[ndbd(NDB)]     2 node(s)
id=2    @192.168.1.12  (mysql-5.6.28 ndb-7.4.10, Nodegroup: 0, *)
id=3    @192.168.1.50  (mysql-5.6.28 ndb-7.4.10, Nodegroup: 0)

[ndb_mgmd(MGM)] 1 node(s)
id=1    @192.168.1.57  (mysql-5.6.28 ndb-7.4.10)

[mysqld(API)]   2 node(s)
id=4    @192.168.1.51  (mysql-5.6.28 ndb-7.4.10)
id=5    @192.168.1.62  (mysql-5.6.28 ndb-7.4.10)
{% endhighlight %}

<br>

### Testing the Cluster

* for security, it is better to change random root password on SQL (API) nodes.

{% highlight bash %}
[root@api01 ~]# cat /root/.mysql_secret
# The random password set for the root user at Thu Jun  2 01:53:40 2016 (local time): xxxxxxxxxxx

[root@api01 ~]# mysql_secure_installation
{% endhighlight %}

* After chaning root password, login to mysql and then create a nw user with host "@", so we will be able to access the MySQL from outside.

{% highlight bash %}
[root@api01 ~]# mysql -u root -p
Enter password: 
{% endhighlight %}

{% highlight sql %}
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 13
Server version: 5.6.28-ndb-7.4.10-cluster-gpl MySQL Cluster Community Server (GPL)

Copyright (c) 2000, 2016, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.


mysql> CREATE USER 'root'@'%' IDENTIFIED BY 'xxxxxxxx';
Query OK, 0 rows affected (0.00 sec)

mysql> 
mysql> select user, host, password from mysql.user;
+------+-----------+-------------------------------------------+
| user | host      | password                                  |
+------+-----------+-------------------------------------------+
| root | localhost | *3500FB4BC9AEFE28B00C228XXXXXXXXXXXXXXXXX |
| root | 127.0.0.1 | *3500FB4BC9AEFE28B00C228XXXXXXXXXXXXXXXXX |
| root | ::1       | *3500FB4BC9AEFE28B00C228XXXXXXXXXXXXXXXXX |
| root | %         | *3500FB4BC9AEFE28B00C228XXXXXXXXXXXXXXXXX |
+------+-----------+-------------------------------------------+
4 rows in set (0.00 sec)

mysql> GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' IDENTIFIED BY PASSWORD '*3500FB4BC9AEFE28B00C228XXXXXXXXXXXXXXXXX' WITH GRANT OPTION;
Query OK, 0 rows affected (0.00 sec)
{% endhighlight %}

* On api 02 node, do same thing and then try to create a database on api01 and then you can see same database on api02.

{% highlight sql %}
# on api01

mysql> show databases;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| mysql              |
| ndbinfo            |
| performance_schema |
+--------------------+
4 rows in set (0.03 sec)


# on api02

mysql> show databases;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| mysql              |
| ndbinfo            |
| performance_schema |
+--------------------+
4 rows in set (0.02 sec)


# on api01 again.

mysql> create database TEST_DB_API01;
Query OK, 1 row affected (0.06 sec)

mysql> show databases;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| TEST_DB_API01      |
| mysql              |
| ndbinfo            |
| performance_schema |
+--------------------+
5 rows in set (0.00 sec)


# check api02 node.

mysql> show databases;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| TEST_DB_API01      |
| mysql              |
| ndbinfo            |
| performance_schema |
+--------------------+
5 rows in set (0.00 sec)
{% endhighlight %}

<br>

### Reference

* https://www.howtoforge.com/tutorial/how-to-install-and-configure-mysql-cluster-on-centos-7/

* http://dev.mysql.com/doc/refman/5.7/en/mysql-cluster-installation.html

* http://dev.mysql.com/doc/refman/5.7/en/mysql-cluster-management.html
