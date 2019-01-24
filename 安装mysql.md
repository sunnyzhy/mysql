# 官网
https://dev.mysql.com/downloads/repo/yum/

# 安装
~~~
# mkdir -p /usr/local/mysql

# cd /usr/local/mysql

# yum -y localinstall mysql57-community-release-el7-11.noarch.rpm

# yum search mysql

# yum -y install mysql-community-server.x86_64 mysql-community-client.x86_64
~~~

# 查看mysql版本
```
# mysql -V
```

# 启动
~~~
# vim /etc/my.cnf
skip-grant-tables

# systemctl restart mysqld

# mysql -u root
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 3
Server version: 5.7.20 MySQL Community Server (GPL)

Copyright (c) 2000, 2017, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql> use mysql;
Reading table information for completion of table and column names
You can turn off this feature to get a quicker startup with -A

Database changed
mysql> update user set authentication_string=password('root') where user='root';
Query OK, 1 row affected, 1 warning (0.00 sec)
Rows matched: 1  Changed: 1  Warnings: 1

mysql> exit;
Bye

# vim /etc/my.cnf
# skip-grant-tables

# systemctl restart mysqld

# mysql -u root -proot
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 3
Server version: 5.7.20 MySQL Community Server (GPL)

Copyright (c) 2000, 2017, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql> 
~~~

# You must reset your password using ALTER USER statement before executing this statement.
```
mysql> SET PASSWORD = PASSWORD('new password');

mysql> ALTER USER 'root'@'localhost' PASSWORD EXPIRE NEVER;

mysql> flush privileges;
```

# Your password does not satisfy the current policy requirements
~~~
mysql>  set global validate_password_policy=0;
Query OK, 0 rows affected (0.00 sec)

mysql> set global validate_password_length=1;
Query OK, 0 rows affected (0.00 sec)

mysql> SET PASSWORD = PASSWORD('root');
Query OK, 0 rows affected, 1 warning (0.00 sec)

mysql> exit;
Bye
~~~

# 设置允许远程连接数据库
~~~
mysql> use mysql;

mysql> update user set user.Host='%' where user.User='root';

mysql> flush privileges;
~~~

# 配置说明
~~~
mysql> grant all privileges on *.* to 'root'@'%' identified by 'root' with grant option;
~~~

~~~
'root'@'%' identified by 'root'，允许任何IP地址（%）的电脑用帐户（root）和密码（root）来访问MySQL Server
'root'@'192.168.0.1'，%号可以用IP地址替换，意思是仅允许指定的IP地址访问MySQL Server
~~~

# 启动、停止、重启mysql服务
~~~
# systemctl start mysqld
# systemctl stop mysqld
# systemctl restart mysqld
~~~

# windows下安装mysql8
1. 解压mysql安装包之后，先删除data文件夹

2. 配置环境变量

在path参数下增加配置 D:\mysql\bin;

3. 新建my.ini文件

```
[mysql]
# 设置mysql客户端默认字符集
default-character-set=utf8 
[mysqld]
#设置3306端口
port = 3306 
# 设置mysql的安装目录
basedir=D:\mysql
# 设置mysql数据库的数据的存放目录
datadir=D:\mysql\data
# 允许最大连接数
max_connections=200
# 服务端使用的字符集默认为8比特编码的latin1字符集
character-set-server=utf8
# 创建新表时将使用的默认存储引擎
default-storage-engine=INNODB 

#skip_grant_tables
```

4. 删除服务

```
>mysqld -remove MySQL
```

5. 初始化

```
>mysqld --initialize-insecure
```
此时data文件夹里会多出mysql的默认数据库文件

6. 安装服务

```
>mysqld --install
```

7. 启动服务

```
>net start mysql
```
