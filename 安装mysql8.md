# 官网
https://dev.mysql.com/downloads/repo/yum/

# 安装
~~~
# mkdir -p /usr/local/mysql

# cd /usr/local/mysql

# rpm -ivh mysql80-community-release-el7-2.noarch.rpm

# cd /etc/yum.repos.d

# ls
CentOS-Base.repo       CentOS-fasttrack.repo  CentOS-Vault.repo
CentOS-CR.repo         CentOS-Media.repo      mysql-community.repo
CentOS-Debuginfo.repo  CentOS-Sources.repo    mysql-community-source.repo

# yum repolist all | grep mysql
mysql-cluster-7.5-community/x86_64 MySQL Cluster 7.5 Community   disabled
mysql-cluster-7.5-community-source MySQL Cluster 7.5 Community - disabled
mysql-cluster-7.6-community/x86_64 MySQL Cluster 7.6 Community   disabled
mysql-cluster-7.6-community-source MySQL Cluster 7.6 Community - disabled
mysql-connectors-community/x86_64  MySQL Connectors Community    enabled:     86
mysql-connectors-community-source  MySQL Connectors Community -  disabled
mysql-tools-community/x86_64       MySQL Tools Community         enabled:     79
mysql-tools-community-source       MySQL Tools Community - Sourc disabled
mysql-tools-preview/x86_64         MySQL Tools Preview           disabled
mysql-tools-preview-source         MySQL Tools Preview - Source  disabled
mysql55-community/x86_64           MySQL 5.5 Community Server    disabled
mysql55-community-source           MySQL 5.5 Community Server -  disabled
mysql56-community/x86_64           MySQL 5.6 Community Server    disabled
mysql56-community-source           MySQL 5.6 Community Server -  disabled
mysql57-community/x86_64           MySQL 5.7 Community Server    disabled
mysql57-community-source           MySQL 5.7 Community Server -  disabled
mysql80-community/x86_64           MySQL 8.0 Community Server    enabled:     66
mysql80-community-source           MySQL 8.0 Community Server -  disabled

# yum -y install mysql-community-server
~~~

# 查看mysql版本
```
# mysql -V
mysql  Ver 8.0.14 for Linux on x86_64 (MySQL Community Server - GPL)
```

# 修改root初始化密码
~~~
# systemctl start mysqld

# cat /var/log/mysqld.log | grep password
2019-01-28T04:31:47.898383Z 5 [Note] [MY-010454] [Server] A temporary password is generated for root@localhost: -;XcNeXkE5OP

# mysql -u root -p
Enter password: -;XcNeXkE5OP
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 9
Server version: 8.0.14

Copyright (c) 2000, 2019, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql> ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'Root@123';
Query OK, 0 rows affected (0.87 sec)

mysql> exit;
Bye

# mysql -u root -pRoot@123
~~~

# 设置允许远程连接
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

# windows安装mysql
1. 解压mysql安装包之后，先删除data文件夹

2. 配置环境变量

```
在path参数下增加配置 D:\mysql\bin;
```

3. 新建my.ini文件

```
打开D:\mysql目录，新建my.ini文件
[mysqld]
# 设置3306端口
port=3306
# 设置mysql的安装目录
basedir=D:\\mysql
# 设置mysql数据库的数据的存放目录
datadir=D:\\mysql\data
# 允许最大连接数
max_connections=200
# 允许连接失败的次数。这是为了防止有人从该主机试图攻击数据库系统
max_connect_errors=10
# 服务端使用的字符集默认为UTF8
character-set-server=utf8
# 创建新表时将使用的默认存储引擎
default-storage-engine=INNODB
# 默认使用“mysql_native_password”插件认证
default_authentication_plugin=mysql_native_password
[mysql]
# 设置mysql客户端默认字符集
default-character-set=utf8
[client]
# 设置mysql客户端连接服务端时默认使用的端口
port=3306
default-character-set=utf8
```

4. 删除服务

```
>mysqld -remove MySQL
```

5. 初始化

```
>mysqld --console --initialize
2019-01-28T02:37:40.284140Z 0 [System] [MY-013169] [Server] D:\mysql\bin\mysqld.exe (mysqld 8.0.13) initializing of server in progress
as process 8672
2019-01-28T02:37:40.285140Z 0 [Warning] [MY-013242] [Server] --character-set-server: 'utf8' is currently an alias for the character set UTF8MB3, but will be an alias for UTF8MB4 in a future release. Please consider using UTF8MB4 in order to be unambiguous.
2019-01-28T02:37:57.442121Z 5 [Note] [MY-010454] [Server] A temporary password is generated for root@localhost: KPyMrb5%TP=y
2019-01-28T02:38:08.513754Z 0 [System] [MY-013170] [Server] D:\mysql\bin\mysqld.exe (mysqld 8.0.13) initializing of server has completed
```
    记下root的初始化密码KPyMrb5%TP=y

**此时D:\mysql目录上会出现data文件夹里，并且文件夹里会生成mysql的默认数据库文件**

6. 安装服务

```
>mysqld --install
```

7. 启动服务

```
>net start mysql
```

8. 修改root密码

```
>mysql -u root -pKPyMrb5%TP=y

mysql> ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'root';

mysql> exit;
Bye

>mysql -u root -proot
```

9. 设置允许远程连接

```
mysql> use mysql;

mysql> update user set user.Host='%' where user.User='root';

mysql> flush privileges;
```
