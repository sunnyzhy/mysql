# sql语法-字段

## 显示表的字段

```sql
SELECT COLUMN_NAME,column_comment FROM information_schema.COLUMNS WHERE table_name = 't1' AND table_schema = 'd1';
```

```sql
SHOW FULL COLUMNS FROM t1;
```

## 添加字段

把字段添加到指定字段的后面:

```sql
ALTER TABLE t1 ADD COLUMN field_name VARCHAR(255) NULL DEFAULT '' COMMENT '备注' AFTER field_name;
```

把字段添加到第一个位置:

```sql
ALTER TABLE t1 ADD COLUMN field_name VARCHAR(255) NULL DEFAULT '' COMMENT '备注' first;
```

把字段添加到最末尾的位置（默认）:

```sql
ALTER TABLE t1 ADD COLUMN field_name VARCHAR(255) NULL DEFAULT '' COMMENT '备注';
```

## 修改字段名

```sql
ALTER TABLE t1 CHANGE old_field new_field INT DEFAULT 0 COMMENT '字段描述';
```

## 修改字段类型

```sql
ALTER TABLE t1 MODIFY COLUMN field_name INT;
```

## 修改字段长度

```sql
ALTER TABLE t1 MODIFY COLUMN field_name VARCHAR(30);
```

## 删除字段

```sql
alter table t1 drop column field_name;
```
