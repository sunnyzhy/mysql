# sql 语法 - 索引

## 查看索引

```sql
SHOW INDEX FROM table_name
```

## 索引的创建

1. PRIMARY KEY（主键索引）
   ```sql
   ALTER TABLE table_name ADD PRIMARY KEY ( column )
   ```

2. UNIQUE(唯一索引)
   ```sql
   ALTER TABLE table_name ADD UNIQUE (column )
   ```

3. INDEX(普通索引)
   ```sql
   ALTER TABLE table_name ADD INDEX index_name ( column )
   ```

4. FULLTEXT(全文索引)
   ```sql
   ALTER TABLE table_name ADD FULLTEXT ( column )
   ```

5. 多列索引
   ```sql
   ALTER TABLE table_name ADD INDEX index_name ( column1, column2, column3 )
   ```

## 删除索引

1. 使用 ```ALTER TABLE``` 语句删除索引
   ```sql
   ALTER TABLE table_name DROP INDEX index_name;
   ```

2. 使用 ```DROP INDEX``` 语句删除索引
   ```sql
   DROP INDEX index_name ON table_name;
   ```
