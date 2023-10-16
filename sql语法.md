# sql语法

## 创建数据库并指定字符集
```sql
CREATE DATABASE IF NOT EXISTS dbname DEFAULT CHARSET utf8 COLLATE utf8_general_ci;
```

## json类型
| id | content |
| - | - |
| 1 | {"id": 1, "name": "filename", "type": "file", "value": "hello world!"} |

- 查询

```sql
SELECT content, content->"$.id"
FROM table_name
WHERE content->"$.id" > 1
ORDER BY content->"$.type";
```

- 删除

```sql
DELETE FROM table_name WHERE content->"$.id" = 1;
```

## datetime 与 timestamp
- **存储的方式不一样**

```
timestamp：添加的时候，把客户端的当前时区转化为UTC（世界标准时间）；查询的时候，把数据库里存储的时间转化为客户端的当前时区。

datetime：添加、查询的时候，不做任何改变，基本上是原样输入和输出。
```

- **存储的时间范围不一样**

```
timestamp：'1970-01-01 00:00:01.000000' 到 '2038-01-19 03:14:07.999999'

datetime：'1000-01-01 00:00:00.000000' 到 '9999-12-31 23:59:59.999999'
```

- **总结**
```
timestamp 和 datetime 除了存储方式和存储范围不一样之外，没有太大区别。当然，对于跨时区的业务，timestamp更为合适。
```

## datetime 与 timestamp 精确到毫秒
``` sql
CREATE TABLE `tab` (
  `id` int(11) DEFAULT NULL,
  `create_time` datetime(3) NOT NULL,
  `update_time` timestamp(3) NOT NULL DEFAULT CURRENT_TIMESTAMP(3) ON UPDATE CURRENT_TIMESTAMP(3)
) ENGINE=InnoDB DEFAULT CHARSET=utf8;
```

```
TIMESTAMP(3)与 DATETIME(3)意思是保留3为毫秒数

TIMESTAMP(6)与 DATETIME(6)意思是保留6为毫秒数
```

## 查询带毫秒的时间
``` sql
SELECT NOW(3);

SELECT CURRENT_TIMESTAMP(3);
```

## 日期转化为时间戳
``` sql
SELECT UNIX_TIMESTAMP('2018-08-11 15:32:25.123');

1533972745.123
```

## 时间戳转化为日期
``` sql
SELECT FROM_UNIXTIME(1533972745.123);

2018-08-11 15:32:25.123
```

## 格式化输出

### ```\g```
```\g``` 效果等同于加上定界符，一般默认的定界符是分号;

### ```\G```
```\G``` 表示将查询结果进行按列打印，可以使每个字段打印到单独的行。

## 重复记录

### 查找重复记录

``` sql
SELECT column1, column2, COUNT(*) as count FROM table_name
GROUP BY column1, column2 HAVING COUNT(*) > 1;
```

### 删除重复记录

``` sql
DELETE t1 FROM table_name t1
INNER JOIN (
  SELECT MIN(id) AS min_id, column1, column2 FROM table_name
  GROUP BY column1, column2 HAVING COUNT(*) > 1
) t2
ON t1.column1 = t2.column1 AND t1.column2 = t2.column2
WHERE t1.id > t2.min_id;
```

## 模糊查询

### like

全表搜索，未用到索引：

``` sql
SELECT `column` FROM `table` WHERE `field` like '%keyword%';
```

使用索引搜索：

``` sql
SELECT `column` FROM `table` WHERE `field` like 'keyword%';
``` 

### 函数

#### LOCATE

全表搜索，未用到索引：

``` sql
SELECT `column` FROM `table` WHERE LOCATE('keyword', `field`) > 0;
```

#### POSITION

全表搜索，未用到索引：

``` sql
SELECT `column` FROM `table` WHERE POSITION('keyword' IN `filed`);
```

#### INSTR

全表搜索，未用到索引：

``` sql
SELECT `column` FROM `table` WHERE INSTR(`field`, 'keyword' ) > 0;
```

#### FIND_IN_SET

全表搜索，未用到索引，内容必须以 ```,``` 分割：

``` sql
SELECT `column` FROM `table` WHERE FIND_IN_SET('keyword',`field`);
```
