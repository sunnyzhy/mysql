# 升级 mysql5.7 到 8.0

***数据库升级并不会删除原来的账号和数据库。***

## 1. 下载 mysql 8.0

[mysql 8.0](https://dev.mysql.com/downloads/mysql/ 'mysql 8.0')

## 2. 卸载之前安装的 mysql

```bash
# rpm -qa | grep mysql
mysql-community-libs-5.7.38-1.el7.x86_64
mysql-community-client-5.7.38-1.el7.x86_64
mysql-community-server-5.7.38-1.el7.x86_64
mysql-community-common-5.7.38-1.el7.x86_64

# rpm -e --nodeps mysql-community-libs-5.7.38-1.el7.x86_64

# rpm -e --nodeps mysql-community-client-5.7.38-1.el7.x86_64

# rpm -e --nodeps mysql-community-server-5.7.38-1.el7.x86_64

# rpm -e --nodeps mysql-community-common-5.7.38-1.el7.x86_64

# rpm -qa | grep mysql
```

## 3. 安装 mysql 8.0

```bash
# tar -xvf mysql-8.0.33-1.el7.x86_64.rpm-bundle.tar
mysql-community-client-8.0.33-1.el7.x86_64.rpm
mysql-community-client-plugins-8.0.33-1.el7.x86_64.rpm
mysql-community-common-8.0.33-1.el7.x86_64.rpm
mysql-community-debuginfo-8.0.33-1.el7.x86_64.rpm
mysql-community-devel-8.0.33-1.el7.x86_64.rpm
mysql-community-embedded-compat-8.0.33-1.el7.x86_64.rpm
mysql-community-icu-data-files-8.0.33-1.el7.x86_64.rpm
mysql-community-libs-8.0.33-1.el7.x86_64.rpm
mysql-community-libs-compat-8.0.33-1.el7.x86_64.rpm
mysql-community-server-8.0.33-1.el7.x86_64.rpm
mysql-community-server-debug-8.0.33-1.el7.x86_64.rpm
mysql-community-test-8.0.33-1.el7.x86_64.rpm

# whereis mysql
mysql: /usr/lib64/mysql /usr/share/mysql

# rm -rf /usr/lib64/mysql /usr/share/mysql

# rpm -ivh mysql-community-common-8.0.33-1.el7.x86_64.rpm

# rpm -ivh mysql-community-client-plugins-8.0.33-1.el7.x86_64.rpm

# rpm -ivh mysql-community-libs-8.0.33-1.el7.x86_64.rpm

# rpm -ivh mysql-community-client-8.0.33-1.el7.x86_64.rpm

# rpm -ivh mysql-community-icu-data-files-8.0.33-1.el7.x86_64.rpm

# rpm -ivh mysql-community-server-8.0.33-1.el7.x86_64.rpm

# rpm -qa | grep mysql
mysql-community-icu-data-files-8.0.33-1.el7.x86_64
mysql-community-client-plugins-8.0.33-1.el7.x86_64
mysql-community-client-8.0.33-1.el7.x86_64
mysql-community-common-8.0.33-1.el7.x86_64
mysql-community-server-8.0.33-1.el7.x86_64
mysql-community-libs-8.0.33-1.el7.x86_64
```

## 4. 初始化 mysql 8.0

```bash
# mysqld --initialize

# chown -R mysql:mysql /var/lib/mysql/

# systemctl start mysqld

# systemctl enable mysqld
```

## 5. 登录 mysql 8.0

- 用之前版本的账号密码登录
- 之前版本的数据库都还在
- 数据库升级并不会删除原来的账号和数据库

```bash
# mysql -uroot -p
Enter password: 
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 27
Server version: 8.0.33 MySQL Community Server - GPL

Copyright (c) 2000, 2023, Oracle and/or its affiliates.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql> 
```
