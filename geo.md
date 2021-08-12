# MYSQL 几何数据类型

## 1 几何数据的存储格式

- WKT(Well-known text) 是 OGC 制定的空间数据的组织规范，WKT 用文本形式描述。

- WKB(well-known binary) 是 OGC 制定的空间数据的组织规范，WKB 用二进制形式描述。

- 用 JSON 格式存储

**以下以 WKT 存储格式为例。**

## 2 WKT 数据类型及格式

[WKT](https://github.com/sunnyzhy/elasticsearch/blob/master/geo_shape.md 'WKT')

### 2.1 Geometry

Geometry 是几何类型的基类，Point、LineString、Polygon 都是 Geometry 的子类。Geometry的属性包括:

- type: 类型(Point, LineString, Polygon, MultiPoint, MultiLineString, MultiPolygon, GeometryCollection)

- SRID: 该值确定了用于描述定义几何对象的坐标空间的空间坐标系统

- coordinates: 坐标值

- interior/boundary/exterior: 
     - interior 是几何对象所展空间的部分
     - boundary 是几何对象的边界
     - exterior 是几何对象未占有的空间

- MBR: 能够覆盖几何对象的最小矩形，可以想象成信封，它由几何对象中最大最小的坐标值组合而成: ((MINX MINY, MAXX MINY, MAXX MAXY, MINX MAXY, MINX MINY))

- simple/nonsimple: 几何对象是否简单

- closed/not closed: 几何对象是否封闭

- dimension: 维度数
     - 0: Point
     - 1: LineString
     - 2: Polygon

### 2.2 Point

点，有一个坐标值，没有长度、面积、边界。

### 2.3 LineString

线，由一系列点连接而成。

如果线从头至尾没有交叉，就是简单的(simple)

如果起点和终点重叠，就是封闭的(closed)

### 2.4 Polygon

多边形，可以是一个实心平面形，即没有内部边界; 也可以有空洞，类似纽扣。

### 2.5 MultiPoint, MultiLineString, MultiPolygon, GeometryCollection

集合类，由多个 Point、LineString 或 Polygon 组合而成。

## 3 WKT 几何对象创建函数

- GeomFromText: 创建一个任意类型的 Geometry 对象
- PointFromText: 创建一个 Point 对象
- LineStringFromText: 创建一个 LineString 对象
- PolygonFromText: 创建一个 Polygon 对象

## 4 创建表

```sql
SET FOREIGN_KEY_CHECKS=0;

-- ----------------------------
-- Table structure for t_geo
-- ----------------------------
DROP TABLE IF EXISTS `t_geo`;
CREATE TABLE `t_geo` (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `name` varchar(255) DEFAULT NULL,
  `geometry` geometry DEFAULT NULL,
  `point` point DEFAULT NULL,
  `line_string` linestring DEFAULT NULL,
  `polygon` polygon DEFAULT NULL,
  `multi_point` multipoint DEFAULT NULL,
  `multi_line_string` multilinestring DEFAULT NULL,
  `multi_polygon` multipolygon DEFAULT NULL,
  `geometry_collection` geometrycollection DEFAULT NULL,
  PRIMARY KEY (`id`) USING BTREE
) ENGINE=InnoDB AUTO_INCREMENT=2 DEFAULT CHARSET=utf8 ROW_FORMAT=DYNAMIC;

-- ----------------------------
-- Records of t_geo
-- ----------------------------
INSERT INTO `t_geo` VALUES ('1', 'GEO', GeomFromText('POINT(-77.03653 38.897676)'), GeomFromText('POINT(-77.03653 38.897676)'), GeomFromText('LINESTRING(-77.03653 38.897676, -77.009051 38.889939)'), GeomFromText('POLYGON((100 0, 101 0, 101 1, 100 1, 100 0))'), GeomFromText('MULTIPOINT(102 2, 103 2)'), GeomFromText('MULTILINESTRING((102 2, 103 2, 103 3, 102 3), (100 0, 101 0, 101 1, 100 1), (100.2 0.2, 100.8 0.2, 100.8 0.8, 100.2 0.8))'), GeomFromText('MULTIPOLYGON(((102 2, 103 2, 103 3, 102 3, 102 2)), ((100 0, 101 0, 101 1, 100 1, 100 0), (100.2 0.2, 100.8 0.2, 100.8 0.8, 100.2 0.8, 100.2 0.2)))'), GeomFromText('GEOMETRYCOLLECTION(POINT(100 0), LINESTRING(101 0, 102 1))'));
```

## 5 基础函数

### 5.1 Geometry

#### Dimension(g)

返回几何的维度数

```sql
mysql> SELECT Dimension(geometry) FROM t_geo WHERE id = 1;
+---------------------+
| Dimension(geometry) |
+---------------------+
|                   0 |
+---------------------+
1 row in set
```

#### Envelope(g)

返回几何的最小边界矩形(MBR)。

```sql
mysql> SELECT Envelope(polygon) FROM t_geo WHERE id = 1;
+----------------------------------------------+
| Envelope(polygon)                            |
+----------------------------------------------+
| POLYGON((100 0, 101 0, 101 1, 100 1, 100 0)) |
+----------------------------------------------+
1 row in set
```

#### GeometryType(g)

以字符串形式返回几何类型的名称，如 POINT, LINESTRING, ...

```sql
mysql> SELECT GeometryType(line_string) FROM t_geo WHERE id = 1;
+---------------------------+
| GeometryType(line_string) |
+---------------------------+
| LINESTRING                |
+---------------------------+
1 row in set
```

#### IsClosed(g)

返回几何是否封闭。

```sql
mysql> SELECT IsClosed(polygon) FROM t_geo WHERE id = 1;
+-------------------+
| IsClosed(polygon) |
+-------------------+
| NULL              |
+-------------------+
1 row in set
```

#### IsSimple(g)

返回几何是否简单。

```sql
mysql> SELECT IsSimple(multi_polygon) FROM t_geo WHERE id = 1;
+-------------------------+
| IsSimple(multi_polygon) |
+-------------------------+
|                       1 |
+-------------------------+
1 row in set
```

### 5.2 Point

#### X(p)

返回点的 X 坐标值(经度)。

```sql
mysql> SELECT X(point) FROM t_geo WHERE id = 1;
+-----------+
| X(point)  |
+-----------+
| -77.03653 |
+-----------+
1 row in set
```

#### Y(p)

返回点的 Y 坐标值(纬度)。

```sql
mysql> SELECT Y(point) FROM t_geo WHERE id = 1;
+-----------+
| Y(point)  |
+-----------+
| 38.897676 |
+-----------+
1 row in set
```

### 5.3 LineString

#### EndPoint(line)

返回线的最后一个点。

```sql
mysql> SELECT EndPoint(line_string) FROM t_geo WHERE id = 1;
+-----------------------------+
| EndPoint(line_string)       |
+-----------------------------+
| POINT(-77.009051 38.889939) |
+-----------------------------+
1 row in set
```

#### StartPoint(line)

返回线的第一个点。

```sql
mysql> SELECT StartPoint(line_string) FROM t_geo WHERE id = 1;
+----------------------------+
| StartPoint(line_string)    |
+----------------------------+
| POINT(-77.03653 38.897676) |
+----------------------------+
1 row in set
```

#### PointN(line, N)

返回线中第 N 个点，N 从 1 开始。

```sql
mysql> SELECT PointN(line_string, 2) FROM t_geo WHERE id = 1;
+-----------------------------+
| PointN(line_string, 2)      |
+-----------------------------+
| POINT(-77.009051 38.889939) |
+-----------------------------+
1 row in set
```

### 5.4 Polygon

#### ExteriorRing(poly)

返回多边形的外环，类型为 LineString

```sql
mysql> SELECT ExteriorRing(polygon) FROM t_geo WHERE id = 1;
+-----------------------------------------------+
| ExteriorRing(polygon)                         |
+-----------------------------------------------+
| LINESTRING(100 0, 101 0, 101 1, 100 1, 100 0) |
+-----------------------------------------------+
1 row in set
```

#### InteriorRingN(poly, N)

返回多边形的第 N 个内环，N 从 1 开始。

```sql
mysql> SELECT InteriorRingN(polygon, 3) FROM t_geo WHERE id = 1;
+---------------------------+
| InteriorRingN(polygon, 3) |
+---------------------------+
| NULL                      |
+---------------------------+
1 row in set
```

#### NumInteriorRings(poly)

返回多边形的内环个数。

```sql
mysql> SELECT NumInteriorRings(polygon) FROM t_geo WHERE id = 1;
+---------------------------+
| NumInteriorRings(polygon) |
+---------------------------+
|                         0 |
+---------------------------+
1 row in set
```

## 6 集合函数

### 6.1 st_union(g1, g2)

将 g1 和 g2 合并为一个集合对象。

```sql
mysql> SELECT st_union(polygon, multi_polygon) FROM t_geo WHERE id = 1;
+--------------------------------------------------------------------------------------------+
| st_union(polygon, multi_polygon)                                                           |
+--------------------------------------------------------------------------------------------+
| MULTIPOLYGON(((102 2, 103 2, 103 3, 102 3, 102 2)), ((100 1, 100 0, 101 0, 101 1, 100 1))) |
+--------------------------------------------------------------------------------------------+
1 row in set
```

### 6.2 st_difference(g1, g2)

返回 g1 与 g2 的集合差异。

```sql
mysql> SELECT st_difference(polygon, multi_polygon) FROM t_geo WHERE id = 1;
+------------------------------------------------------------------+
| st_difference(polygon, multi_polygon)                            |
+------------------------------------------------------------------+
| POLYGON((100.2 0.2, 100.8 0.2, 100.8 0.8, 100.2 0.8, 100.2 0.2)) |
+------------------------------------------------------------------+
1 row in set
```

### 6.3 st_intersection(g1, g2)

返回 g1 与 g2 的集合交集。

```sql
mysql> SELECT st_intersection(polygon, multi_polygon) FROM t_geo WHERE id = 1;
+-------------------------------------------------------------------------------------------------------+
| st_intersection(polygon, multi_polygon)                                                               |
+-------------------------------------------------------------------------------------------------------+
| POLYGON((100 1, 100 0, 101 0, 101 1, 100 1), (100.2 0.2, 100.2 0.8, 100.8 0.8, 100.8 0.2, 100.2 0.2)) |
+-------------------------------------------------------------------------------------------------------+
1 row in set
```

## 7 相离函数

### 7.1 st_disjoint(g1, g2)

g1 与 g2 是否不相交，返回值:

- 1: 不相交
- 0: 相交

```sql
mysql> SELECT st_disjoint(polygon, multi_polygon) FROM t_geo WHERE id = 1;
+-------------------------------------+
| st_disjoint(polygon, multi_polygon) |
+-------------------------------------+
|                                   0 |
+-------------------------------------+
1 row in set
```

## 8 相切函数

### 8.1 st_touches(g1, g2)

g1 与 g2 是否相切，返回值:

- 1: 相切
- 0: 不相切

```sql
mysql> SELECT st_touches(line_string, polygon) FROM t_geo WHERE id = 1;
+----------------------------------+
| st_touches(line_string, polygon) |
+----------------------------------+
|                                0 |
+----------------------------------+
1 row in set
```

## 9 相交函数

### 9.1 st_intersects(g1, g2)

g1 与 g2 是否相交，返回值:

- 1: 相交
- 0: 不相交

与 st_disjoint(g1, g2) 功能相反。

```sql
mysql> SELECT st_intersects(polygon, multi_polygon) FROM t_geo WHERE id = 1;
+---------------------------------------+
| st_intersects(polygon, multi_polygon) |
+---------------------------------------+
|                                     1 |
+---------------------------------------+
1 row in set
```

### 9.2 st_crosses(g1, g2)

用于判断线-面交叉关系，当线的一部分在面内，一部分在面外时为真。

```sql
mysql> SELECT st_crosses(line_string, polygon) FROM t_geo WHERE id = 1;
+----------------------------------+
| st_crosses(line_string, polygon) |
+----------------------------------+
|                                0 |
+----------------------------------+
1 row in set
```

### 9.3 st_overlaps(g1, g2)

主要用于判断面-面交叉关系，当两个面有部分重叠时为真。

```sql
mysql> SELECT st_overlaps(polygon, multi_polygon) FROM t_geo WHERE id = 1;
+-------------------------------------+
| st_overlaps(polygon, multi_polygon) |
+-------------------------------------+
|                                   1 |
+-------------------------------------+
1 row in set
```

## 10 包含函数

### 10.1 st_within(g1, g2)

主要用于判断 g1 是否在 g2 内或点-线、线-线包含关系，允许有交点在面的边界上，但不允许所有的交点都在边界上，即不允许点在线的端点上，线完全在面的边界上。

```sql
mysql> SELECT st_within(point, polygon) FROM t_geo WHERE id = 1;
+---------------------------+
| st_within(point, polygon) |
+---------------------------+
|                         0 |
+---------------------------+
1 row in set
```

### 10.2 st_contains(g1, g2)

参数顺序与 st_within() 颠倒，其他同 st_within()

```sql
mysql> SELECT st_contains(polygon, point) FROM t_geo WHERE id = 1;
+-----------------------------+
| st_contains(polygon, point) |
+-----------------------------+
|                           0 |
+-----------------------------+
1 row in set
```

### 10.3 st_equals(g1, g2)

当 g1 与 g2 完全重合时为真。

```sql
mysql> SELECT st_equals(polygon, polygon) FROM t_geo WHERE id = 1;
+-----------------------------+
| st_equals(polygon, polygon) |
+-----------------------------+
|                           1 |
+-----------------------------+
1 row in set
```
