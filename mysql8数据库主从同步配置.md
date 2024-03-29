# mysql8 数据库主从同步配置

## mysql 主从同步的原理

![主从同步的原理](./images/master-slave.png "master-slave")

**注: 当且仅当以下三个条件都成立的时候，才表示从库启动成功:**

- ```Slave_IO_State: Waiting for master to send event```
- ```Slave_IO_Running: Yes```
- ```Slave_SQL_Running: Yes```

## 主从同步

### 主库

#### 1. 修改主服务器的配置文件 my.cnf

```bash
# vim /etc/my.cnf
[mysqld]
server-id=200 #取本机 ip 的末端即可

#binlog-ignore-db=db_name   # 不同步的数据库,如果指定了 binlog-do-db 这里应该可以不用指定的
binlog-do-db=db_name # 要同步的数据库，如果不指定则同步全部数据库

log-bin=mysql-bin # 开启二进制日志，这一点决定了数据同步的成败，mysql-bin 是自定义的二进制日志名称
```

#### 2. 重启主服务器的 mysql 服务

```bash
# systemctl restart mysqld
```

#### 3. 在主库添加用户并授权

```bash
mysql> use mysql;

mysql> create user 'username'@'slave ip' identified by 'password';

mysql> grant replication slave on *.* to 'username'@'slave ip';

mysql> alter user 'username'@'slave ip' identified with mysql_native_password BY 'password';

mysql> flush privileges; 

mysql> select * from user where user = 'username' \G;
Repl_slave_priv: Y
```

***注: 设置密码的时候需要遵照密码策略***

```bash
mysql> SHOW VARIABLES LIKE 'validate_password%';
+--------------------------------------+--------+
| Variable_name                        | Value  |
+--------------------------------------+--------+
| validate_password_check_user_name    | OFF    |
| validate_password_dictionary_file    |        |
| validate_password_length             | 8      |
| validate_password_mixed_case_count   | 1      |
| validate_password_number_count       | 1      |
| validate_password_policy             | MEDIUM |
| validate_password_special_char_count | 1      |
+--------------------------------------+--------+
```

#### 4. 删除用户及授权

```bash
mysql> revoke all on *.* from 'username'@'slave ip'; # 删除授权

mysql> use mysql;

mysql> delete from user where user='username' and host='slave ip'; # 删除用户

mysql> flush privileges; # 刷新
```

#### 5. 记录主库 File 和 Position 项对应的值

```bash
mysql> show master status \G;
File: mysql-bin.000002
Position: 156
```

### 从库

#### 1. 修改从服务器的配置文件 my.cnf

```bash
# vim /etc/my.cnf
[mysqld]
server-id=210

#replicate-wild-do-table=db_name.table_name
replicate-do-db=db_name

log-bin=mysql-bin
```

#### 2. 重启从服务器的 mysql 服务

```bash
# systemctl restart mysqld
```

#### 3. 配置从库连接主库所需的信息

```bash
mysql> stop slave;

mysql> change master to master_host='master ip',master_port=3306,master_user='username',master_password='password',master_log_file='mysql-bin.000002',master_log_pos=156;

mysql> set global sql_slave_skip_counter=1;
```

参数说明:

- master_log_file 对应主库 File 项值
- master_log_pos 对应主库 Position 项值

#### 4. 开启从库的同步功能

```bash
mysql> start slave;
```

#### 5. 查看从库状态

```bash
mysql> show slave status \G;
Slave_IO_State: Waiting for master to send event # slave 连接到 master 的状态，Waiting for master to send event 表示已经成功连接到 master，正等待二进制日志事件的到达
Master_Log_File: mysql-bin.000002 # 主库 file 项对应的值
Read_Master_Log_Pos: 156 # 主库 position 项对应的值
Slave_IO_Running: Yes  # IO/thread 是否启动，连接到主库，并读取主库的日志到本地，生成本地日志文件，Yes 表示正常
Slave_SQL_Running: Yes # SQL thread 是否启动，读取本地日志文件，并执行日志里的 SQL 命令，Yes 表示正常
```

#### 6. 在主库执行 CRUD 操作，从库会同步更新

**注意：**

- 主从库间的数据不是实时同步的。 
- 如果主从库之间的网络断开，则从库会在网络正常后批量同步。 
- 如果在从库修改数据，就很可能造成从库在执行主库的 bin-log 时出现错误而停止同步，一般不建议在从库进行 CRUD 操作。

## 主主同步

**在上述操作的基础上，再把主从同步逆向配置一遍。**

### 从库

#### 1. 修改从服务器的配置文件 my.cnf

也就是在原有配置的基础上添加 "binlog-do-db=db_name"。

```bash
# vim /etc/my.cnf
server-id=107
replicate-do-db=test
log-bin=mysql-bin

binlog-do-db=test
```

#### 2. 重启从服务器的 mysql 服务

```bash
# systemctl restart mysqld
```

#### 3. 在从库添加用户并授权

```bash
mysql> use mysql;

mysql> create user 'username'@'master ip' identified by 'password';

mysql> grant replication slave on *.* to 'username'@'master ip';

mysql> alter user 'username'@'master ip' identified with mysql_native_password BY 'password';

mysql> flush privileges; 

mysql> select * from user where user = 'username' \G;
Repl_slave_priv: Y
```

#### 4. 记录从库 File 和 Position 项对应的值

```bash
mysql> show master status \G;
File: mysql-bin.000006
Position: 156
```

### 主库

#### 1. 修改主服务器的配置文件 my.cnf

也就是在原有配置的基础上添加 "replicate-do-db=db_name"。

```bash
# vim /etc/my.cnf
[mysqld]
server-id=106
binlog-do-db=test
log-bin=mysql-bin

replicate-do-db=test
```

#### 2. 重启主服务器的 mysql 服务

```bash
# systemctl restart mysqld
```

#### 3. 配置主库连接从库所需的信息

```bash
mysql> stop slave;

mysql> change master to master_host='slave ip',master_port=3306,master_user='username',master_password='password',master_log_file='mysql-bin.000006',master_log_pos=156;

mysql> set global sql_slave_skip_counter=1;
```

参数说明:

- master_log_file 对应主库 File 项值
- master_log_pos 对应主库 Position 项值

#### 4. 开启主库的同步功能

```bash
mysql> start slave;
```

#### 5. 查看主库状态

```bash
mysql> show slave status \G;
Slave_IO_State: Waiting for master to send event
Master_Log_File: mysql-bin.000006 #从库 file 项对应的值
Read_Master_Log_Pos: 156 #从库 position 项对应的值
Slave_IO_Running: Yes  #连接到从库，并读取从库的日志到本地，生成本地日志文件
Slave_SQL_Running: Yes #读取本地日志文件，并执行日志里的 SQL 命令
```

#### 6. 在从库执行 CRUD 操作，主库会同步更新

## 同步参数说明

### 主库同步参数

- binlog-do-db: 二进制日志记录的数据库，如果不指定则同步全部数据库

- binlog-ignore-db: 二进制日志中忽略数据库

### 从库同步参数

- replicate-do-db: 配置需要同步的数据库，如果需要同步多个数据库，写法如下：

   ```bash
   replicate-do-db=db_name1
   replicate-do-db=db_name2
   ```

- replicate-ignore-db: 配置需要忽略的同步数据库

- replicate-do-table: 配置需要同步的表，如果需要同步多个表，写法如下：

   ```
   replicate-do-table=db_name.table_name1
   replicate-do-table=db_name.table_name2
   ```

- replicate-ignore-table: 配置需要忽略的同步表

- replicate-wild-do-table: 同步表的时候，**建议使用该项配置**。同 replication-do-table 功能一样，但是可以通配符，如 db_name.%

- replicate-wild-ignore-table: 同 replication-ignore-table 功能一样，但是可以加通配符

## FAQ

### Last_IO_Error: error connecting to master 'root@master ip:3306' - retry-time: 60 retries: 1 message: Authentication plugin 'caching_sha2_password' reported error: Authentication requires secure connection.

- 错误原因
   密码加密方式不支持

- 解决方法，在**主服务器**执行以下命令：
   ```bash
   mysql> alter user 'username'@'slave ip' identified with mysql_native_password BY 'password';
   
   mysql> flush privileges; 
   ```

### Last_IO_Error: Fatal error: The slave I/O thread stops because master and slave have equal MySQL server UUIDs; these UUIDs must be different for replication to work.

- 错误原因
   主从服务器的 mysql 使用了相同的 UUID

   - 解决方法
   ```bash
   # cd /var/lib/mysql
   # vim auto.cnf
   [auto]
   server-uuid=修改成唯一的uuid
   ```

### Slave_SQL_Running: No

***需要执行 ```show slave status \G;```，然后根据错误日志 ```Last_Error``` 来分析具体问题。***

问题之一：```Last_Error: Error 'Can't drop database 'xx'; database doesn't exist' on query. Default database: 'xx'. Query: 'drop database xx''```

原因：由于在主库执行了删除数据库的操作，而这个数据库在从库中并不存在（比如先在主库创建了数据库，后配置的主从同步，这样一来从库就不存在这个数据库），从而导致从库报错，进而导致 ```Slave_SQL_Running``` 进程停止。

解决方法：跳过当前出错的语句：

```bash
mysql> stop slave;

mysql> set global sql_slave_skip_counter=1;

mysql> start slave;
```

### 启动 slave 时报错 ```Slave failed to initialize relay log info structure from the repository```

执行以下语句：

```bash
mysql> reset slave;
```
