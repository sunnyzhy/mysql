# sql 语法 - 注释

## 创建注释

```sql
CREATE TABLE table_name
(
   field_name field_type COMMENT '字段的注释'
)COMMENT = '表的注释';
```

示例:

```sql
CREATE TABLE t1
(
   id INT COMMENT '字段的注释'
)COMMENT = '表的注释';
```

## 查看表的所有字段的注释

```sql
SHOW FULL COLUMNS FROM table_name;
```

示例:

```sql
SHOW FULL COLUMNS FROM t1;
```

## 修改表的注释

```sql
ALTER TABLE table_name COMMENT '修改后的表的注释';
```

示例:

```sql
ALTER TABLE t1 COMMENT '修改后的表的注释';
```

## 修改字段的注释

```sql
ALTER TABLE table_name MODIFY COLUMN field_name change_type COMMENT '修改后的字段注释';
```

示例:

```sql
ALTER TABLE t1 MODIFY COLUMN id VARCHAR(255) COMMENT '修改后的字段注释';
```
