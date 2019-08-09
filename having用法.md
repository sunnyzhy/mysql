# having 示例

1. **显示每个地区的总人口数和总面积**

```sql
SELECT region, SUM(population), SUM(area)
FROM bbc
GROUP BY region
```

先以 region 把返回记录分成多个组，这就是 GROUP BY 的字面含义。分完组后，然后用聚合函数对每组中的不同字段（一或多条记录）作运算。

2. **显示每个地区的总人口数和总面积．仅显示那些面积超过 1000000 的地区**

```sql
SELECT region, SUM(population), SUM(area)
FROM bbc
GROUP BY region
HAVING SUM(area)>1000000
```

```
在这里，我们不能用 where 来筛选超过 1000000 的地区，因为表中不存在这样一条记录。
相反，HAVING 子句可以让我们筛选成组后的各组数据。
```

3. **查询 CUSTOMER 和 ORDER 表中用户的订单数**

```sql
select c.name, count(order_number) as count from orders o,customer c where c.id=o.customer_id group by customer_id;

+--------+-------+
| name   | count |
+--------+-------+
| d      |     9 |
| cc     |     6 |
| cccccc |     2 |
+--------+-------+
```

增加 HAVING 过滤

```sql
select c.name, count(order_number) as count from orders o,customer c where c.id=o.customer_id group by customer_id having count(order_number)>5;

+------+-------+
| name | count |
+------+-------+
| d    |     9 |
| cc   |     6 |

+------+-------+
```

4. **其他示例**
```sql
select * from sc;

+-----+-----+-------+
| SNO | PNO | GRADE |
+-----+-----+-------+
|   1 | YW  |    95 |
|   1 | SX  |    98 |
|   1 | YY  |    90 |
|   2 | YW  |    89 |
|   2 | SX  |    91 |
|   2 | YY  |    92 |
|   3 | YW  |    85 |
|   3 | SX  |    88 |
|   3 | YY  |    96 |
|   4 | YW  |    95 |
|   4 | SX  |    89 |
|   4 | YY  |    88 |
+-----+-----+-------+
```

这个表所描述的是 4 个学生对应每科学习成绩的记录，其中SNO（学生号）、PNO（课程名）、GRADE（成绩）。


- 显示 90 分以上学生的课程名和成绩

```sql
select sno, pno, grade from sc where grade >= 90;

+-----+-----+-------+
| sno | pno | grade |
+-----+-----+-------+
|   1 | YW  |    95 |
|   1 | SX  |    98 |
|   1 | YY  |    90 |
|   2 | SX  |    91 |
|   2 | YY  |    92 |
|   3 | YY  |    96 |
|   4 | YW  |    95 |
+-----+-----+-------+
```

- 显示每个学生的成绩在 90 分以上的各有多少门

```sql
select sno, count(*) from sc where grade >= 90 group by sno;

+-----+----------+
| sno | count(*) |
+-----+----------+
|   1 |        3 |
|   2 |        2 |
|   3 |        1 |
|   4 |        1 |
+-----+----------+
```

- 这里我们并没有使用 having 语句，接下来如果我们要评选三好学生，条件是至少有两门课程在 90 分以上才能有资格，列出有资格的学生号及 90 分以上的课程数。

```sql
select sno,count(*) from sc where grade>=90 group by sno having count(*)>=2;

+-----+----------+
| sno | count(*) |
+-----+----------+
|   1 |        3 |
|   2 |        2 |
+-----+----------+
```

- 学校评选先进学生，要求平均成绩大于 90 分的学生都有资格，并且语文课必须在 95 分以上，请列出有资格的学生

```sql
select sno,avg(grade) from sc where SNO IN (SELECT SNO FROM SC WHERE GRADE>=95 AND PNO='YW') group by sno having avg(grade)>=90;

+-----+------------+
| sno | avg(grade) |
+-----+------------+
|   1 | 94.3333    |
|   4 | 90.6667    |
+-----+------------+
```

- 查询比平均成绩至少比学号是 3 的平均成绩高的学生学号以及平均分数

```sql
select sno,avg(grade) from sc
group by sno
having avg(grade) > (select avg(grade) from sc where sno=3);

+-----+------------+
| sno | avg(grade) |
+-----+------------+
|   1 | 94.3333    |
|   2 | 90.6667    |
|   4 | 90.6667    |
+-----+------------+
```

# having 与 where 的区别
1. 区别1
   - having 在分组后对数据进行过滤

   - where 在分组前对数据进行过滤

2. 区别2
   - having 后面可以使用聚合函数

   - where 后面不可以使用聚合函数
