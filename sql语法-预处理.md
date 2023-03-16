# sql 语法 - 预处理

## 语法

```sql
# 1. 定义预处理语句
PREPARE stmt_name FROM preparable_stmt;

# 2. 执行预处理语句
EXECUTE stmt_name [USING @var_name [, @var_name] ...];

# 3. 删除(释放)定义
{DEALLOCATE | DROP} PREPARE stmt_name;
```

***预处理编译 SQL 是占用资源的，所以在使用后注意及时使用 ```DEALLOCATE PREPARE``` 释放资源。***

## 使用字符串定义预处理 SQL

```sql
SET @a = 1;
SET @b = 2;
PREPARE stmt FROM 'SELECT SUM(? + ?) AS sum';
EXECUTE stmt USING @a, @b;
DEALLOCATE PREPARE stmt;
```

## 使用变量定义预处理 SQL

```sql
SET @a = 1;
SET @b = 2;
# SET @sql = CONCAT('SELECT SUM(? + ?)', ' AS sum');
SET @sql = 'SELECT SUM(? + ?) AS sum';
PREPARE stmt FROM @sql;
EXECUTE stmt USING @a, @b;
DEALLOCATE PREPARE stmt;
```

## 解决必须使用常量面不能使用变量的问题

尝试修改表的 ```AUTO_INCREMENT```，但是值必须是常量，不能使用变量，所以报错:

```sql
mysql> SET @max_id = (SELECT MAX(id) + 1 FROM t1);
Query OK, 0 rows affected (0.05 sec)

mysql> ALTER TABLE t1 AUTO_INCREMENT = @max_id;
1064 - You have an error in your SQL syntax; check the manual that corresponds to your MySQL server version for the right syntax to use near '@max_id' at line 1
```

可以使用预处理语句解决该问题:

```sql
SET @max_id = (SELECT MAX(id) + 1 FROM t1);
SET @sql = CONCAT('ALTER TABLE t1 AUTO_INCREMENT = ', @max_id);
PREPARE stmt FROM @sql;
EXECUTE stmt;
DEALLOCATE PREPARE stmt;
```
