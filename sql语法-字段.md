# sql语法-字段

## 显示表的字段

```sql
SELECT COLUMN_NAME,column_comment FROM information_schema.COLUMNS WHERE table_name = 't1' AND table_schema = 'd1';
```

```sql
SHOW FULL COLUMNS FROM t1;
```

## 修改字段名

```sql
ALTER TABLE t1 CHANGE old_field new_field INT DEFAULT 0 COMMENT '字段描述';
```
