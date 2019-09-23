# The user specified as a definer ('root'@'%') does not exist
调用存储过程时，总是提示错误：java.sql.SQLException: The user specified as a definer ('root'@'%') does not exist

- 原因

在创建存储过程的时候定义了DEFINER，如
```
CREATE DEFINER=`root`@`%` PROCEDURE ...
```

- 解决方法

添加对存储过程的访问权限，即指定DEFINER
```
mysql> UPDATE mysql.proc SET definer='root@localhost' WHERE name='' AND db='';

mysql> FLUSH PRIVILEGES;
```
- mysql.proc是系统常量
- definer是"用户名@IP"
- name是存储过程名称
- db是数据库名称

# 使用 root 用户远程连接 mysql 后只能看到 information_schema 数据库
# Access denied for user 'root'@'192.168.0.10' to database 'mysql'

- 原因

这是权限不足导致的，尽管是用 root 账户登录，navicat 登录相当于是远程登录数据库，仍会出现权限不足只能看到 information_schema 临时库。

- 解决方法
1. 登录远程数据库

```
> mysql -u root -proot -h remote_ip -P3306
```

2. 切换数据库

```
mysql> use mysql;
```

3. 查看user表中root用户的权限

```
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

```
mysql> grant all privileges on *.* to 'root'@'20.0.0.48' identified by 'root' with grant option;
```

5. 查看 host 为 192.168.0.10 的 root 用户的权限

```
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
