# 安装 mysql8
## linux 下安装 mysql8
[mysql8 yum](https://dev.mysql.com/downloads/repo/yum/ 'mysql8 yum')

### 1. 安装 mysql8 的 yum 源
```bash
# ls /etc/yum.repos.d
CentOS-Base.repo         CentOS-Debuginfo.repo  CentOS-Sources.repo
CentOS-Base.repo.backup  CentOS-fasttrack.repo  CentOS-Vault.repo
CentOS-CR.repo           CentOS-Media.repo      CentOS-x86_64-kernel.repo

# wget https://dev.mysql.com/get/mysql80-community-release-el7-3.noarch.rpm

# yum localinstall mysql80-community-release-el7-3.noarch.rpm

# ls /etc/yum.repos.d
CentOS-Base.repo         CentOS-fasttrack.repo  CentOS-x86_64-kernel.repo
CentOS-Base.repo.backup  CentOS-Media.repo      mysql-community.repo
CentOS-CR.repo           CentOS-Sources.repo    mysql-community-source.repo
CentOS-Debuginfo.repo    CentOS-Vault.repo
```

### 2. 安装 mysql
```bash
# yum -y install mysql-community-server
```

### 3. 查看mysql版本
```bash
# mysql -V
mysql  Ver 8.0.22 for Linux on x86_64 (MySQL Community Server - GPL)
```

### 4. 修改root初始化密码
```bash
# systemctl start mysqld

# cat /var/log/mysqld.log | grep password
2019-01-28T04:31:47.898383Z 5 [Note] [MY-010454] [Server] A temporary password is generated for root@localhost: -;XcNeXkE5OP

# mysql -u root -p
Enter password: -;XcNeXkE5OP
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 9
Server version: 8.0.22

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
```

### 5. 设置允许远程连接
```bash
mysql> use mysql;

mysql> update user set user.Host='%' where user.User='root';

mysql> flush privileges;
```

### 6. 启动、停止、重启mysql服务
```bash
# systemctl start mysqld
# systemctl stop mysqld
# systemctl restart mysqld
```

## windows 下安装 mysql8
[mysql8](https://dev.mysql.com/downloads/mysql/ 'mysql8')

### 1. 下载并解压 mysql 压缩文件

### 2. 配置环境变量
1. 新建系统变量"MYSQL_HOME"，值为"D:\MySql\mysql-8.0.22-winx64"
2. 在 path 中添加 "%;MYSQL_HOME%\bin;"

### 3. 新建 my.ini 文件
打开 D:\MySql\mysql-8.0.22-winx64 目录，新建 my.ini 文件:

```
[mysqld]
# 设置3306端口
port=3306
# 设置mysql的安装目录
basedir=D:\\MySql\\mysql-8.0.22-winx64
# 设置mysql数据库的数据的存放目录
datadir=D:\\MySql\\mysql-8.0.22-winx64\\data
# 允许最大连接数
max_connections=200
# 允许连接失败的次数。这是为了防止有人从该主机试图攻击数据库系统
max_connect_errors=10
# 服务端使用的字符集默认为UTF8
character-set-server=utf8
# 创建新表时将使用的默认存储引擎
default-storage-engine=INNODB
# 默认使用"mysql_native_password"插件认证
default_authentication_plugin=mysql_native_password
[mysql]
# 设置mysql客户端默认字符集
default-character-set=utf8
[client]
# 设置mysql客户端连接服务端时默认使用的端口
port=3306
default-character-set=utf8
```

### 4. 删除服务
```bash
>mysqld -remove MySQL
```

### 5. 初始化
以管理员的身份打开 cmd 窗口:

```bash
>d:

>cd D:\MySql\mysql-8.0.22-winx64

>mysqld --console --initialize
2021-01-06T07:57:25.778504Z 0 [System] [MY-013169] [Server] D:\MySql\mysql-8.0.22-winx64\bin\mysqld.exe (mysqld 8.0.22) initializing of server in progress as process 19588
2021-01-06T07:57:25.779502Z 0 [Warning] [MY-013242] [Server] --character-set-server: 'utf8' is currently an alias for the character set UTF8MB3, but will be analias for UTF8MB4 in a future release. Please consider using UTF8MB4 in order to be unambiguous.
2021-01-06T07:57:25.791502Z 1 [System] [MY-013576] [InnoDB] InnoDB initialization has started.
2021-01-06T07:57:35.303046Z 1 [System] [MY-013577] [InnoDB] InnoDB initialization has ended.
2021-01-06T07:57:56.479258Z 6 [Note] [MY-010454] [Server] A temporary password is generated for root@localhost: absR<=yrB4IT
```

此时 D:\MySql\mysql-8.0.22-winx64 目录下会出现 data 文件夹，并且在文件夹里会生成 mysql 的默认数据库文件。

**记下 root 的初始化密码"absR<=yrB4IT"**

### 6. 安装服务

```bash
>mysqld --install
```

### 7. 启动服务
```bash
>net start mysql
```

### 8. 修改root密码
```bash
>mysql -uroot -p
Enter password: absR<=yrB4IT

mysql> ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'root';

mysql> exit;
Bye

>mysql -uroot -p
Enter password: root
```

### 9. 设置允许远程连接
```bash
mysql> use mysql;

mysql> update user set user.Host='%' where user.User='root';

mysql> flush privileges;
```
