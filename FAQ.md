# mysql 常见问题

## The user specified as a definer ('root'@'%') does not exist

调用存储过程时，总是提示错误：java.sql.SQLException: The user specified as a definer ('root'@'%') does not exist

- 原因

在创建存储过程的时候定义了DEFINER，如

```sql
CREATE DEFINER=`root`@`%` PROCEDURE ...
```

- 解决方法

添加对存储过程的访问权限，即指定DEFINER

```bash
mysql> UPDATE mysql.proc SET definer='root@localhost' WHERE name='' AND db='';

mysql> FLUSH PRIVILEGES;
```

- mysql.proc是系统常量
- definer是"用户名@IP"
- name是存储过程名称
- db是数据库名称

## 使用 root 用户远程连接 mysql 后只能看到 information_schema 数据库

## Access denied for user 'root'@'192.168.0.10' to database 'mysql'

- 原因

这是权限不足导致的，尽管是用 root 账户登录，navicat 登录相当于是远程登录数据库，仍会出现权限不足只能看到 information_schema 临时库。

- 解决方法
- 
1. 登录远程数据库

```bash
> mysql -u root -proot -h remote_ip -P3306
```

2. 切换数据库

```bash
mysql> use mysql;
```

3. 查看user表中root用户的权限

```bash
mysql> select * from user where user='root' \G;
*************************** 1. row ***************************
                  Host: 192.168.0.10
                  User: root
           Select_priv: N
           Insert_priv: N
           Update_priv: N
           Delete_priv: N
           Create_priv: N
             Drop_priv: N
           Reload_priv: N
         Shutdown_priv: N
          Process_priv: N
             File_priv: N
            Grant_priv: N
       References_priv: N
            Index_priv: N
            Alter_priv: N
          Show_db_priv: N
            Super_priv: N
 Create_tmp_table_priv: N
      Lock_tables_priv: N
          Execute_priv: N
       Repl_slave_priv: Y
      Repl_client_priv: N
      Create_view_priv: N
        Show_view_priv: N
   Create_routine_priv: N
    Alter_routine_priv: N
      Create_user_priv: N
            Event_priv: N
          Trigger_priv: N
Create_tablespace_priv: N
              ssl_type:
            ssl_cipher:
           x509_issuer:
          x509_subject:
         max_questions: 0
           max_updates: 0
       max_connections: 0
  max_user_connections: 0
                plugin: mysql_native_password
 authentication_string: *81F5E21E35407D884A6CD4A731AEBFB6AF209E1B
      password_expired: N
 password_last_changed: 2019-07-19 10:19:32
     password_lifetime: NULL
        account_locked: N
```
root 用户的 host 为 192.168.0.10 的权限都是 'N'，表示 host 为 192.168.0.10 的 root 用户不具有访问权限。

4. 修改 host 为 192.168.0.10 的 root 用户的权限

```bash
mysql> grant all privileges on *.* to 'root'@'20.0.0.48' identified by 'root' with grant option;
```

5. 查看 host 为 192.168.0.10 的 root 用户的权限

```bash
mysql> select * from user where user='root' \G;
*************************** 1. row ***************************
                  Host: 192.168.0.10
                  User: root
           Select_priv: Y
           Insert_priv: Y
           Update_priv: Y
           Delete_priv: Y
           Create_priv: Y
             Drop_priv: Y
           Reload_priv: Y
         Shutdown_priv: Y
          Process_priv: Y
             File_priv: Y
            Grant_priv: Y
       References_priv: Y
            Index_priv: Y
            Alter_priv: Y
          Show_db_priv: Y
            Super_priv: Y
 Create_tmp_table_priv: Y
      Lock_tables_priv: Y
          Execute_priv: Y
       Repl_slave_priv: Y
      Repl_client_priv: Y
      Create_view_priv: Y
        Show_view_priv: Y
   Create_routine_priv: Y
    Alter_routine_priv: Y
      Create_user_priv: Y
            Event_priv: Y
          Trigger_priv: Y
Create_tablespace_priv: Y
              ssl_type:
            ssl_cipher:
           x509_issuer:
          x509_subject:
         max_questions: 0
           max_updates: 0
       max_connections: 0
  max_user_connections: 0
                plugin: mysql_native_password
 authentication_string: *81F5E21E35407D884A6CD4A731AEBFB6AF209E1B
      password_expired: N
 password_last_changed: 2019-09-23 10:02:14
     password_lifetime: NULL
        account_locked: N
```
此时，host 为 192.168.0.10 的 root 用户已经具有访问权限。

**无需重启远程mysql服务，192.168.0.10 的权限就已经生效了。**

## mysql5.7 [Err]1055

- 报错信息

```
[Err] 1055 - Expression #1 of ORDER BY clause is not in GROUP BY clause and contains nonaggregated column
'information_schema.PROFILING.SEQ' which is not functionally dependent on columns in GROUP BY clause;
this is incompatible with sql_mode=only_full_group_by
```

- 问题分析

mysql 从 5.7.5 开始默认开启 ONLY_FULL_GROUP_BY。

```sql
mysql> show variables like 'sql_mode'\G;
*************************** 1. row ***************************
Variable_name: sql_mode
        Value: ONLY_FULL_GROUP_BY,STRICT_TRANS_TABLES,NO_ZERO_IN_DATE,NO_ZERO_DATE,ERROR_FOR_DIVISION_BY_ZERO,NO_ENGINE_SUBSTITUTION
```

- 解决方法

关闭 ONLY_FULL_GROUP_BY。

```bash
# vim /etc/my.cnf
[mysqld]
sql_mode=STRICT_TRANS_TABLES,NO_ZERO_IN_DATE,NO_ZERO_DATE,ERROR_FOR_DIVISION_BY_ZERO,NO_ENGINE_SUBSTITUTION

# systemctl restart mysqld
```

## Reading table information for completion of table and column names You can turn off this feature to get a quicker startup with -A

```bash
# mysql -uroot -ppassword -h192.168.0.100 -A
```

## 主主(主从)同步出现 SQL_ERROR 1032

### 方法1 跳过错误 Event

跳过这一条错误(event)，让主从同步恢复正常。（或者 N 条 event，一条一条地跳过）。

```bash
mysql> stop slave;

mysql> set global sql_slave_skip_counter=1;

mysql> start slave;
```

### 方法2 跳过所有的 1032 错误

在 my.cnf 文件中添加以下配置:

```bash
# vim /etc/my.cnf
slave-skip-errors=1032

# systemctl restart mysqld
```

***注意：因为要重启数据库，所以不推荐，除非错误事件太多。***

### 方法3 还原被删除的数据

[还原被删除的数据](https://github.com/sunnyzhy/mysql/blob/master/mysqlbinlog%E7%94%A8%E6%B3%95.md '还原被删除的数据')

## You can't specify target table <table_name> for update in FROM clause

原因：不能在同一语句中，先 ```select``` 出表中的某些值，再 ```update/delete``` 这个表，比如执行以下语句时就会报该错误:

```sql
DELETE FROM <table_name> WHERE id = (SELECT max(id) FROM <table_name>);

UPDATE <table_name> SET <field> = 100 WHERE id = (SELECT max(id) FROM <table_name>);
```

解决方法：```select``` 的结果再通过一个中间表 ```select``` 一次，就可以避免这个错误:

```sql
UPDATE <table_name> SET <field> = 100 WHERE id = (SELECT id FROM (SELECT max(id) AS id FROM <table_name>) AS d);

DELETE FROM <table_name> WHERE id = (SELECT id FROM (SELECT max(id) AS id FROM <table_name>) AS d);
```

## quartz 报错：Failure obtaining db row lock: Table 'xxxx.QRTZ_LOCKS' doesn't exist

spring-boot 连接 quartz 的时候，启动报错：```Failure obtaining db row lock: Table 'xxxx.QRTZ_LOCKS' doesn't exist```

原因：quartz 数据库里是有 ```qrtz_locks``` 表的，但是表名都是小写，而访问 quartz 的时候用的是大写，所以认为 ```xxxx.QRTZ_LOCKS``` 表不存在。

有以下两种解决方法：

1. 把 quartz 数据库里的表名都换成大写
2. 添加或修改配置属性 ```lower_case_table_names```
   - 1: 忽略大小写
   - 0: 区分大小写

解决方法2:

1. 先查看 ```lower_case_table_names```:
```sql
mysql> show variables like '%lower_case_table_names%';
+------------------------+-------+
| Variable_name          | Value |
+------------------------+-------+
| lower_case_table_names | 0     |
+------------------------+-------+
```
2. 在 ```my.cnf``` 文件的末尾加上 ```lower_case_table_names=1```:
```bash
# vim /etc/my.cnf
lower_case_table_names=1

# systemctl restart mysqld
```
3. 再查看 ```lower_case_table_names```:
```sql
mysql> show variables like '%lower_case_table_names%';
+------------------------+-------+
| Variable_name          | Value |
+------------------------+-------+
| lower_case_table_names | 1     |
+------------------------+-------+
```

## ERROR 1819 (HY000): Your password does not satisfy the current policy requirements

原因：修改密码时，密码不满足安全策略。

解决方法1：设置满足安全策略的密码***【推荐】***

解决方法2：修改密码的安全策略

1. 查看密码的安全策略，默认为 ```MEDIUM```，密码长度默认为 ```8```
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
2. 修改密码的安全策略
    ```bash
    mysql> set global validate_password_policy = LOW;
    ```
3. 修改密码的长度
    ```bash
    mysql> set global validate_password_length = 6;
    ```
4. 查看密码的安全策略
    ```bash
    mysql> SHOW VARIABLES LIKE 'validate_password%';
    +--------------------------------------+-------+
    | Variable_name                        | Value |
    +--------------------------------------+-------+
    | validate_password_check_user_name    | OFF   |
    | validate_password_dictionary_file    |       |
    | validate_password_length             | 6     |
    | validate_password_mixed_case_count   | 1     |
    | validate_password_number_count       | 1     |
    | validate_password_policy             | LOW   |
    | validate_password_special_char_count | 1     |
    +--------------------------------------+-------+
    ```
5. 修改密码
    ```bash
    mysql> ALTER USER 'root'@'%' IDENTIFIED WITH mysql_native_password BY '123456';
    ```
6. 恢复密码的安全策略
    ```bash
    mysql> set global validate_password_policy = MEDIUM;
    
    mysql> set global validate_password_length = 8;
    
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
