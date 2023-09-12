# 升级 mysql5.7 到 8.0

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

### 4.1. 忽略表名大小写

***数据库升级并不会删除原来的账号和数据库。***

```bash
# mysqld --initialize

# chown -R mysql:mysql /var/lib/mysql/

# systemctl start mysqld

# systemctl enable mysqld
```

### 4.2. 区分表名大小写

***数据库升级必须删除原来的账号和数据库 ```rm -rf /var/lib/mysql```。***

官方 mysql8.0 版本新增的说明：

```
lower_case_table_names can only be configured when initializing the server. Changing the lower_case_table_names setting after the server is initialized is prohibited.
```

翻译：

```
LOWER_CASE_TABLE_NAMES只能在初始化服务器时配置。禁止在服务器初始化后更改LOWER_CASE_TABLE_NAMES设置。
```

在 8.0 版本，如果 mysql 已经初始化过，就不支持后续修改 ```lower_case_table_names``` 参数了。

也就是说，如果已经使用 ```mysqld --initialize``` 初始化过 mysql，后续再尝试通过以下方法修改 ```lower_case_table_names``` 都是无效的：

- 在 ```/etc/my.cnf``` 里添加配置项 ```lower_case_table_names=1```，重启服务时会发现 mysqld 无法启动；该解决方案只能在 ```5.6/5.7``` 低版本的 mysql 中有效
- 使用 ```mysqld --initialize --lower-case-table-names=1``` 再次初始化，登录 mysql 之后，查询 ```show Variables like 'lower_case_table_names';``` 会发现 ```lower_case_table_names``` 变量的值依然是 ```0```

***如果要区分表名大小写的话，就必须在初始化 mysql 之前修改 ```/etc/my.cnf``` 里的配置项 ```lower_case_table_names=1```。***

1. 备份 mysql 数据库
2. 初始化 mysql
    ```bash
    # rm -rf /var/lib/mysql
    
    # vim /etc/my.cnf
    lower_case_table_names=1
    
    # mysqld --initialize
    
    # chown -R mysql:mysql /var/lib/mysql/
    
    # systemctl start mysqld
    
    # systemctl enable mysqld
    ```
3. 修改密码
    ```bash
    # mysql -u root -p
    Enter password:
    
    mysql> ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'Root@123';
    
    mysql> exit;
    
    # mysql -u root -pRoot@123
    ```
4. 还原 mysql 数据库

## 5. 登录 mysql 8.0

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
