# mysql 解析 json 字符串/数组

## 创建表
```sql
SET FOREIGN_KEY_CHECKS=0;

-- ----------------------------
-- Table structure for t_json
-- ----------------------------
DROP TABLE IF EXISTS `t_json`;
CREATE TABLE `t_json` (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `content` json DEFAULT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB AUTO_INCREMENT=8 DEFAULT CHARSET=utf8;

-- ----------------------------
-- Records of t_json
-- ----------------------------
INSERT INTO `t_json` VALUES ('1', '[{\"id\": 1, \"age\": 20, \"name\": \"aa\"}, {\"id\": 2, \"age\": 21, \"name\": \"bb\"}]');
INSERT INTO `t_json` VALUES ('2', '[{\"id\": 3, \"age\": 20, \"name\": \"cc\"}, {\"id\": 4, \"age\": 22, \"name\": \"bb\"}]');
INSERT INTO `t_json` VALUES ('3', '[{\"id\": 5, \"age\": 23, \"name\": \"cc\"}]');
INSERT INTO `t_json` VALUES ('6', '{\"rows\": [{\"id\": 6, \"age\": 23, \"name\": \"dd\"}, {\"id\": 7, \"age\": 22, \"name\": \"ee\"}]}');
INSERT INTO `t_json` VALUES ('7', '{\"rows\": [{\"id\": 8, \"age\": 21, \"name\": \"ff\"}]}');
```

## 查询记录

### 1. 查询 name='cc' 的记录
```bash
mysql> SELECT * FROM t_json WHERE JSON_CONTAINS(content,JSON_OBJECT('name', 'cc'));
+----+--------------------------------------------------------------------------+
| id | content                                                                  |
+----+--------------------------------------------------------------------------+
|  2 | [{"id": 3, "age": 20, "name": "cc"}, {"id": 4, "age": 22, "name": "bb"}] |
|  3 | [{"id": 5, "age": 23, "name": "cc"}]                                     |
+----+--------------------------------------------------------------------------+
2 rows in set
```

### 2. 查询 name='cc' 并且 id=5 的记录
```bash
mysql> SELECT * FROM t_json WHERE JSON_CONTAINS(content,JSON_OBJECT('name', 'cc', 'id', 5));
+----+--------------------------------------+
| id | content                              |
+----+--------------------------------------+
|  3 | [{"id": 5, "age": 23, "name": "cc"}] |
+----+--------------------------------------+
1 row in set
```

### 3. 查询 id=1 或 id=5 的记录
```bash
mysql> SELECT * FROM t_json WHERE JSON_CONTAINS(content,JSON_OBJECT('id', 1)) OR JSON_CONTAINS(content,JSON_OBJECT('id', 5));
+----+--------------------------------------------------------------------------+
| id | content                                                                  |
+----+--------------------------------------------------------------------------+
|  1 | [{"id": 1, "age": 20, "name": "aa"}, {"id": 2, "age": 21, "name": "bb"}] |
|  3 | [{"id": 5, "age": 23, "name": "cc"}]                                     |
+----+--------------------------------------------------------------------------+
2 rows in set
```

### 4. 查询 rows 字段里 name='ff' 的记录
```bash
mysql> SELECT * FROM t_json WHERE JSON_CONTAINS(JSON_EXTRACT(content,'$.rows'),JSON_OBJECT('name', 'ff'));
+----+------------------------------------------------+
| id | content                                        |
+----+------------------------------------------------+
|  7 | {"rows": [{"id": 8, "age": 21, "name": "ff"}]} |
+----+------------------------------------------------+
1 row in set
```
