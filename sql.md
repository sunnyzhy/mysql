# json类型
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

# datetime 与 timestamp 精确到毫秒
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

# 查询带毫秒的时间
``` sql
SELECT NOW(3);

SELECT CURRENT_TIMESTAMP(3);
```

# 日期转化为时间戳
``` sql
SELECT UNIX_TIMESTAMP('2018-08-11 15:32:25.123');

1533972745.123
```

# 时间戳转化为日期
``` sql
SELECT FROM_UNIXTIME(1533972745.123);

2018-08-11 15:32:25.123
```
