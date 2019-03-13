# COUNT(*)、COUNT(1)、COUNT(0)、COUNT(列名)、COUNT(DISTINCT 列名)的区别

## mysql
### 创建测试数据
```sql
DROP TABLE IF EXISTS `users`;
CREATE TABLE `users` (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `name` varchar(255) DEFAULT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB AUTO_INCREMENT=4 DEFAULT CHARSET=utf8;

INSERT INTO `users` VALUES ('1', 'aa');
INSERT INTO `users` VALUES ('2', 'bb');
INSERT INTO `users` VALUES ('3', null);
INSERT INTO `users` VALUES ('4', 'aa');
```

### 执行COUNT(*)
```sql
mysql> SELECT COUNT(*) FROM users;
+----------+
| COUNT(*) |
+----------+
|        4 |
+----------+

mysql> EXPLAIN SELECT COUNT(*) FROM users;
+----+-------------+-------+------------+-------+---------------+---------+---------+------+------+----------+-------------+
| id | select_type | table | partitions | type  | possible_keys | key     | key_len | ref  | rows | filtered | Extra       |
+----+-------------+-------+------------+-------+---------------+---------+---------+------+------+----------+-------------+
|  1 | SIMPLE      | users | NULL       | index | NULL          | PRIMARY | 4       | NULL |    3 |   100.00 | Using index |
+----+-------------+-------+------------+-------+---------------+---------+---------+------+------+----------+-------------+
```
    type字段是index，使用了索引优化查询，默认主键PRIMARY索引

### 执行COUNT(1)
```sql
mysql> SELECT COUNT(1) FROM users;
+----------+
| COUNT(1) |
+----------+
|        4 |
+----------+

mysql> EXPLAIN SELECT COUNT(1) FROM users;
+----+-------------+-------+------------+-------+---------------+---------+---------+------+------+----------+-------------+
| id | select_type | table | partitions | type  | possible_keys | key     | key_len | ref  | rows | filtered | Extra       |
+----+-------------+-------+------------+-------+---------------+---------+---------+------+------+----------+-------------+
|  1 | SIMPLE      | users | NULL       | index | NULL          | PRIMARY | 4       | NULL |    3 |   100.00 | Using index |
+----+-------------+-------+------------+-------+---------------+---------+---------+------+------+----------+-------------+
```
    type字段是index，使用了索引优化查询，默认主键PRIMARY索引

### 执行COUNT(0)
```sql
mysql> SELECT COUNT(0) FROM users;
+----------+
| COUNT(0) |
+----------+
|        4 |
+----------+

mysql> EXPLAIN SELECT COUNT(0) FROM users;
+----+-------------+-------+------------+-------+---------------+---------+---------+------+------+----------+-------------+
| id | select_type | table | partitions | type  | possible_keys | key     | key_len | ref  | rows | filtered | Extra       |
+----+-------------+-------+------------+-------+---------------+---------+---------+------+------+----------+-------------+
|  1 | SIMPLE      | users | NULL       | index | NULL          | PRIMARY | 4       | NULL |    3 |   100.00 | Using index |
+----+-------------+-------+------------+-------+---------------+---------+---------+------+------+----------+-------------+
```
    type字段是index，使用了索引优化查询，默认主键PRIMARY索引

### 执行COUNT(name)
```sql
mysql> SELECT COUNT(name) FROM users;
+-------------+
| COUNT(name) |
+-------------+
|           3 |
+-------------+

mysql> EXPLAIN SELECT COUNT(name) FROM users;
+----+-------------+-------+------------+------+---------------+------+---------+------+------+----------+-------+
| id | select_type | table | partitions | type | possible_keys | key  | key_len | ref  | rows | filtered | Extra |
+----+-------------+-------+------------+------+---------------+------+---------+------+------+----------+-------+
|  1 | SIMPLE      | users | NULL       | ALL  | NULL          | NULL | NULL    | NULL |    3 |   100.00 | NULL  |
+----+-------------+-------+------------+------+---------------+------+---------+------+------+----------+-------+
```
    type字段是ALL，使用了全表扫描

### 执行COUNT(DISTINCT name)
```sql
mysql> SELECT COUNT(DISTINCT name) FROM users;
+----------------------+
| COUNT(DISTINCT name) |
+----------------------+
|                    2 |
+----------------------+

mysql> EXPLAIN SELECT COUNT(DISTINCT name) FROM users;
+----+-------------+-------+------------+------+---------------+------+---------+------+------+----------+-------+
| id | select_type | table | partitions | type | possible_keys | key  | key_len | ref  | rows | filtered | Extra |
+----+-------------+-------+------------+------+---------------+------+---------+------+------+----------+-------+
|  1 | SIMPLE      | users | NULL       | ALL  | NULL          | NULL | NULL    | NULL |    3 |   100.00 | NULL  |
+----+-------------+-------+------------+------+---------------+------+---------+------+------+----------+-------+
```
    type字段是ALL，使用了全表扫描

## sql server
### 创建测试数据
```sql
CREATE TABLE [dbo].[users](
	[id] [int] IDENTITY(1,1) NOT NULL,
	[name] [varchar](50) NULL,
 CONSTRAINT [PK_t] PRIMARY KEY CLUSTERED 
(
	[id] ASC
)WITH (PAD_INDEX  = OFF, STATISTICS_NORECOMPUTE  = OFF, IGNORE_DUP_KEY = OFF, ALLOW_ROW_LOCKS  = ON, ALLOW_PAGE_LOCKS  = ON) ON [PRIMARY]
) ON [PRIMARY]

INSERT [dbo].[users] ([id], [name]) VALUES (1, N'aa')
INSERT [dbo].[users] ([id], [name]) VALUES (2, N'bb')
INSERT [dbo].[users] ([id], [name]) VALUES (3, NULL)
INSERT [dbo].[users] ([id], [name]) VALUES (4, N'aa')
```

### 开启查询计划
```sql
SET SHOWPLAN_TEXT ON;
```

### 执行COUNT(*)
```sql
SELECT COUNT(*) FROM users; -- 4

  |--Compute Scalar(DEFINE:([Expr1003]=CONVERT_IMPLICIT(int,[Expr1004],0)))
       |--Stream Aggregate(DEFINE:([Expr1004]=Count(*)))
            |--Clustered Index Scan(OBJECT:([test].[dbo].[users].[PK_t]))
```

### 执行COUNT(1)
```sql
SELECT COUNT(1) FROM users; -- 4

  |--Compute Scalar(DEFINE:([Expr1003]=CONVERT_IMPLICIT(int,[Expr1004],0)))
       |--Stream Aggregate(DEFINE:([Expr1004]=Count(*)))
            |--Clustered Index Scan(OBJECT:([test].[dbo].[users].[PK_t]))
```
    COUNT(1)转化成了COUNT(*)

### 执行COUNT(0)
```sql
SELECT COUNT(0) FROM users; -- 4

  |--Compute Scalar(DEFINE:([Expr1003]=CONVERT_IMPLICIT(int,[Expr1004],0)))
       |--Stream Aggregate(DEFINE:([Expr1004]=Count(*)))
            |--Clustered Index Scan(OBJECT:([test].[dbo].[users].[PK_t]))
```
    COUNT(0)转化成了COUNT(*)

### 执行COUNT(name)
```sql
SELECT COUNT(name) FROM users; -- 3

  |--Compute Scalar(DEFINE:([Expr1003]=CONVERT_IMPLICIT(int,[Expr1004],0)))
       |--Stream Aggregate(DEFINE:([Expr1004]=COUNT([test].[dbo].[users].[name])))
            |--Clustered Index Scan(OBJECT:([test].[dbo].[users].[PK_t]))
```

### 执行COUNT(DISTINCT name)
```sql
SELECT COUNT(DISTINCT name) FROM users; -- 2

  |--Compute Scalar(DEFINE:([Expr1003]=CONVERT_IMPLICIT(int,[Expr1006],0)))
       |--Stream Aggregate(DEFINE:([Expr1006]=COUNT([test].[dbo].[users].[name])))
            |--Sort(DISTINCT ORDER BY:([test].[dbo].[users].[name] ASC))
                 |--Clustered Index Scan(OBJECT:([test].[dbo].[users].[PK_t]))
```

## 结论
- count(0)=count(1)=count(*)

  1. count(指定的有效值)--执行计划都会转化为count(*)
  
  2. 如果指定的是列名，会判断是否有null，null不计算

- 列表说明

|类别|作用|
|--|--|
|COUNT(*)|统计总行数，含NULL值|
|COUNT(n)|统计总行数，可以是COUNT(任何整数或小数)，含NULL值|
|COUNT(列名)|统计某一列非NULL的行数|
|COUNT(DISTINCT 列名)|统计某一列非NULL且不相同的行数|
