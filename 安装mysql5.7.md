# 下载地址

官网： https://downloads.mysql.com/archives/community/

下载 mysql-5.7.27-linux-glibc2.12-x86_64.tar.gz

# 解压
```
# tar -zxvf mysql-5.7.27-linux-glibc2.12-x86_64.tar.gz

# mv mysql-5.7.27-linux-glibc2.12-x86_64 mysql-5.7.27
```

# 创建data文件夹
```
# cd mysql-5.7.27

# mkdir data
```

# 初始化
```
# /usr/local/mysql/mysql-5.7.27/bin/mysqld --initialize --user=root --datadir=/usr/local/mysql/mysql-5.7.27/data --basedir=/usr/local/mysql/mysql-5.7.27
```

# 复制启动脚本到资源目录
```
# cd /usr/local/mysql/mysql-5.7.27

# cp ./support-files/mysql.server /etc/rc.d/init.d/mysqld
```

# 将mysqld服务加入到系统服务
```
# chkconfig --add mysqld

# chkconfig --list mysqld
mysqld         	0:off	1:off	2:on	3:on	4:on	5:on	6:off
```

# 启动mysql
```
# systemctl start mysqld
```

# 登录mysql
```
# vim /etc/my.cnf
skip-grant-tables

# systemctl restart mysqld

# mysql -u root
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 2
Server version: 5.7.27 MySQL Community Server - (GPL), wsrep_25.19

Copyright (c) 2000, 2019, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql> use mysql;

Reading table information for completion of table and column names
You can turn off this feature to get a quicker startup with -A

Database changed
mysql> update user set authentication_string=password('root') where user='root';
Query OK, 4 rows affected, 1 warning (0.00 sec)
Rows matched: 4  Changed: 4  Warnings: 1

mysql> exit;
Bye

# vim /etc/my.cnf
# skip-grant-tables

# systemctl restart mysqld

# mysql -u root -p
Enter password: 
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 3
Server version: 5.7.27 MySQL Community Server - (GPL), wsrep_25.19

Copyright (c) 2000, 2019, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql> 
```
