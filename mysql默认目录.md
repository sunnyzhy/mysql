# mysql 默认目录

## 1 my.cnf 文件

```bash
# vim /etc/my.cnf
```

## 2 数据库目录

```bash
# cd /var/lib/mysql
```

## 3 mysql 系统配置文件

```bash
# cd /usr/share/mysql
```

## 4 mysql 命令

```bash
# cd /usr/bin

# ls mysql*
mysql                mysqld_pre_systemd  mysql_plugin               mysql_ssl_rsa_setup
mysqladmin           mysqldump           mysqlpump                  mysql_tzinfo_to_sql
mysqlbinlog          mysqldumpslow       mysql_secure_installation  mysql_upgrade
mysqlcheck           mysqlimport         mysqlshow
mysql_config_editor  mysql_install_db    mysqlslap
```

## 5 日志文件路径

```bash
mysql> show variables like 'general_log_file';
+------------------+---------------------------+
| Variable_name    | Value                     |
+------------------+---------------------------+
| general_log_file | /var/lib/mysql/node01.log |
+------------------+---------------------------+
```

## 6 错误日志文件路径

```bash
mysql> show variables like 'log_error';
+---------------+---------------------+
| Variable_name | Value               |
+---------------+---------------------+
| log_error     | /var/log/mysqld.log |
+---------------+---------------------+
```

## 7 慢查询日志文件路径

```bash
mysql> show variables like 'slow_query_log_file';
+---------------------+--------------------------------+
| Variable_name       | Value                          |
+---------------------+--------------------------------+
| slow_query_log_file | /var/lib/mysql/node01-slow.log |
+---------------------+--------------------------------+
```

## 8 log_bin 文件路径

```bash
mysql> show variables like 'log_bin%';
+---------------------------------+---------------------------------+
| Variable_name                   | Value                           |
+---------------------------------+---------------------------------+
| log_bin                         | ON                              |
| log_bin_basename                | /var/lib/mysql/node02-bin       |
| log_bin_index                   | /var/lib/mysql/node02-bin.index |
| log_bin_trust_function_creators | OFF                             |
| log_bin_use_v1_row_events       | OFF                             |
+---------------------------------+---------------------------------+
```
