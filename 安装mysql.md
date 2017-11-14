# mariadb
```
MariaDB数据库管理系统是MySQL的一个分支，主要由开源社区在维护，采用GPL授权许可。开发这个分支的原因之一是：甲骨文公司收购了MySQL后，有将MySQL闭源的潜在风险，因此社区采用分支的方式来避开这个风险。MariaDB的目的是完全兼容MySQL，包括API和命令行，使之能轻松成为MySQL的代替品。
```

# 卸载mysql
```
# rpm -qa | grep -i mysql
mysql-community-client-5.7.20-1.el7.x86_64
mysql-community-server-5.7.20-1.el7.x86_64
mysql-community-common-5.7.20-1.el7.x86_64
mysql-community-libs-compat-5.7.20-1.el7.x86_64
mysql-community-libs-5.7.20-1.el7.x86_64
mysql57-community-release-el7-11.noarch

# yum -y remove mysql-community mysql-community-server mysql-community-libs mysql-community-common

# rpm -qa |grep -i mysql
mysql57-community-release-el7-11.noarch

# yum -y remove mysql57-community-release

# rpm -qa |grep -i mysql
# 

# find / -name mysql
/run/lock/subsys/mysql
/etc/selinux/targeted/active/modules/100/mysql
/etc/selinux/targeted/tmp/modules/100/mysql
/root/.local/share/Trash/files/mysql1/bin/mysql
/root/.local/share/Trash/files/mysql1/include/mysql
/root/.local/share/Trash/files/mysql1/data/mysql
/var/spool/mail/mysql
/usr/share/mysql
/usr/share/grafana/public/app/plugins/datasource/mysql
/usr/local/mysql
/usr/local/mysql/data/mysql
/home/mysql

# rm -rf /run/lock/subsys/mysql
# rm -rf /etc/selinux/targeted/active/modules/100/mysql
# rm -rf /etc/selinux/targeted/tmp/modules/100/mysql
# rm -rf /root/.local/share/Trash/files/mysql1/bin/mysql
# rm -rf /root/.local/share/Trash/files/mysql1/include/mysql
# rm -rf /root/.local/share/Trash/files/mysql1/data/mysql
# rm -rf /var/spool/mail/mysql
# rm -rf /usr/share/mysql
# rm -rf /usr/share/grafana/public/app/plugins/datasource/mysql
# rm -rf /usr/local/mysql
# rm -rf /usr/local/mysql/data/mysql
# rm -rf /home/mysql

# find / -name mysql
# 
```

# mariadb官网仓库
https://downloads.mariadb.org/mariadb/repositories/

# 卸载旧版的mariadb
```
# rpm -qa |grep -i mariadb
mariadb-libs-5.5.56-2.el7.x86_64
mariadb-5.5.56-2.el7.x86_64
mariadb-server-5.5.56-2.el7.x86_64

# yum -y remove mariadb mariadb-libs mariadb-server

# rpm -qa |grep -i mariadb
#
```

# 安装mariadb
```
按照官网上的教程操作。
```

```
# cd /etc/yum.repos.d/

# vim MariaDB.repo
# MariaDB 10.2 CentOS repository list - created 2017-11-13 03:16 UTC
# http://downloads.mariadb.org/mariadb/repositories/
[mariadb]
name = MariaDB
baseurl = http://yum.mariadb.org/10.2/centos7-amd64
gpgkey=https://yum.mariadb.org/RPM-GPG-KEY-MariaDB
gpgcheck=1

# yum -y install MariaDB-server MariaDB-client

# rpm --import https://yum.mariadb.org/RPM-GPG-KEY-MariaDB

# systemctl start mariadb
```

# 修改root密码
```
# mysqladmin -u root -p password root
Enter password: root
```

# 连接mariadb
```
# mysql -u root -proot
Welcome to the MariaDB monitor.  Commands end with ; or \g.
Your MariaDB connection id is 9
Server version: 10.2.10-MariaDB MariaDB Server

Copyright (c) 2000, 2017, Oracle, MariaDB Corporation Ab and others.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

MariaDB [(none)]> show databases;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| mysql              |
| performance_schema |
| test               |
+--------------------+
4 rows in set (0.00 sec)

MariaDB [(none)]> use mysql;
Reading table information for completion of table and column names
You can turn off this feature to get a quicker startup with -A

Database changed
MariaDB [mysql]> show tables;
+---------------------------+
| Tables_in_mysql           |
+---------------------------+
| columns_priv              |
| db                        |
| event                     |
| func                      |
| general_log               |
| help_category             |
| help_keyword              |
| help_relation             |
| help_topic                |
| host                      |
| ndb_binlog_index          |
| plugin                    |
| proc                      |
| procs_priv                |
| proxies_priv              |
| servers                   |
| slow_log                  |
| tables_priv               |
| time_zone                 |
| time_zone_leap_second     |
| time_zone_name            |
| time_zone_transition      |
| time_zone_transition_type |
| user                      |
+---------------------------+
24 rows in set (0.00 sec)

MariaDB [mysql]> select Host,User from user;
+-----------------------+------+
| Host                  | User |
+-----------------------+------+
| 127.0.0.1             | root |
| ::1                   | root |
| localhost             |      |
| localhost             | root |
| localhost.localdomain |      |
| localhost.localdomain | root |
+-----------------------+------+
6 rows in set (0.00 sec)

MariaDB [mysql]> quit
Bye
```

# 数据存放目录
```
MariaDB 的默认数据存放在 /var/lib/mysql/ 目录下
```

```
# cd /var/lib/mysql/
# ls
aria_log.00000001  ibdata1      ibtmp1             mysql               tc.log
aria_log_control   ib_logfile0  localhost.pid      mysql.sock          test
ib_buffer_pool     ib_logfile1  multi-master.info  performance_schema
```
