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
# mysql_secure_installation

NOTE: RUNNING ALL PARTS OF THIS SCRIPT IS RECOMMENDED FOR ALL MariaDB
      SERVERS IN PRODUCTION USE!  PLEASE READ EACH STEP CAREFULLY!

In order to log into MariaDB to secure it, we'll need the current
password for the root user.  If you've just installed MariaDB, and
you haven't set the root password yet, the password will be blank,
so you should just press enter here.

Enter current password for root (enter for none): 
OK, successfully used password, moving on...

Setting the root password ensures that nobody can log into the MariaDB
root user without the proper authorisation.

Set root password? [Y/n] y
New password: 
Re-enter new password: 
Password updated successfully!
Reloading privilege tables..
 ... Success!


By default, a MariaDB installation has an anonymous user, allowing anyone
to log into MariaDB without having to have a user account created for
them.  This is intended only for testing, and to make the installation
go a bit smoother.  You should remove them before moving into a
production environment.

Remove anonymous users? [Y/n] y
 ... Success!

Normally, root should only be allowed to connect from 'localhost'.  This
ensures that someone cannot guess at the root password from the network.

Disallow root login remotely? [Y/n] y
 ... Success!

By default, MariaDB comes with a database named 'test' that anyone can
access.  This is also intended only for testing, and should be removed
before moving into a production environment.

Remove test database and access to it? [Y/n] y
 - Dropping test database...
 ... Success!
 - Removing privileges on test database...
 ... Success!

Reloading the privilege tables will ensure that all changes made so far
will take effect immediately.

Reload privilege tables now? [Y/n] y
 ... Success!

Cleaning up...

All done!  If you've completed all of the above steps, your MariaDB
installation should now be secure.

Thanks for using MariaDB!
```

# 连接mariadb
```
# mysql -u root -proot
Welcome to the MariaDB monitor.  Commands end with ; or \g.
Your MariaDB connection id is 16
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
+--------------------+
3 rows in set (0.00 sec)

MariaDB [(none)]> use mysql;
Reading table information for completion of table and column names
You can turn off this feature to get a quicker startup with -A

Database changed
MariaDB [mysql]> show tables;
+---------------------------+
| Tables_in_mysql           |
+---------------------------+
| column_stats              |
| columns_priv              |
| db                        |
| event                     |
| func                      |
| general_log               |
| gtid_slave_pos            |
| help_category             |
| help_keyword              |
| help_relation             |
| help_topic                |
| host                      |
| index_stats               |
| innodb_index_stats        |
| innodb_table_stats        |
| plugin                    |
| proc                      |
| procs_priv                |
| proxies_priv              |
| roles_mapping             |
| servers                   |
| slow_log                  |
| table_stats               |
| tables_priv               |
| time_zone                 |
| time_zone_leap_second     |
| time_zone_name            |
| time_zone_transition      |
| time_zone_transition_type |
| user                      |
+---------------------------+
30 rows in set (0.00 sec)

MariaDB [mysql]> select Host,User from user;
+-----------+------+
| Host      | User |
+-----------+------+
| 127.0.0.1 | root |
| ::1       | root |
| localhost | root |
+-----------+------+
3 rows in set (0.01 sec)

MariaDB [mysql]> quit
Bye
```

# 设置允许远程连接数据库
```
MariaDB [mysql]> GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' IDENTIFIED BY 'root' WITH GRANT OPTION;
Query OK, 0 rows affected (0.00 sec)

MariaDB [mysql]> flush privileges;
Query OK, 0 rows affected (0.00 sec)
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
