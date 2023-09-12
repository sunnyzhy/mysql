# sql 语法 - 重命名

## 重命名数据库

在 mysql8.0 及后续版本中，rename 命令已不再适用:

```sql
rename database data1 to data2;
```

正确的做法是：

1. 创建新的数据库：
    ```sql
    mysql> CREATE DATABASE IF NOT EXISTS `test1` DEFAULT CHARACTER SET utf8;
    ```
2. 将数据库 test 的表和数据复制到新数据库 test1 中：
    ```bash
    # mysqldump test -u root -p123456  | mysql test1 -u root -p123456
    ```
3. 删除数据库 test
    ```sql
    mysql> drop database test;
    ```

## 重命名数据表

### rename

```sql
rename table 旧表名 to 新表名;
```

### alter

```sql
alter table 旧表名 rename [as] 新表名;
```

### shell 脚本

重命名表名，把小写的表名转换为大写：

```sh
#/bin/sh
#set -x

database='数据库'
continue_table_prefix='忽略的表名前缀'
arr=$(mysql -uroot -p密码 -e "select table_name from information_schema.tables where table_schema='${database}' \G" | awk 'BEGIN{IGNORECASE=1} /table_name:/ {print $2}')
for a in $arr
do
  if [ ${a:0:3} = ${continue_table_prefix} ]; then
    continue
  fi
  mysql -uroot -psaftop9854@ -e "rename table ${database}.${a} to ${database}.${a^^};"
done
```
