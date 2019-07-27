# 乐观锁
乐观锁不是数据库自带的，需要我们自己去实现。乐观锁是指操作数据库时(更新操作)，想法很乐观，认为这次的操作不会导致冲突，在操作数据时，并不进行任何其他的特殊处理（也就是不加锁），而在进行更新后，再去判断是否有冲突了。

通常实现是这样的：在表中的数据进行操作时(更新)，先给数据表加一个版本(version)字段，每操作一次，将那条记录的版本号加1。也就是先查询出那条记录，获取出version字段,如果要对那条记录进行操作(更新),则先判断此刻version的值是否与刚刚查询出来时的version的值相等，如果相等，则说明这段期间，没有其他程序对其进行操作，则可以执行更新，将version字段的值加1；如果更新时发现此刻的version值与刚刚获取出来的version的值不相等，则说明这段期间已经有其他程序对其进行操作了，则不进行更新操作。

```sql
mysql> SELECT description,version FROM t WHERE id = 1;

mysql> UPDATE t SET description='update1', version = version + 1 WHERE id = 1 AND version = 1;
```

# 悲观锁
与乐观锁相对应的就是悲观锁了。悲观锁就是在操作数据时，认为此操作会出现数据冲突，所以在进行每次操作时都要通过获取锁才能进行对相同数据的操作，这点跟java中的synchronized很相似，所以悲观锁需要耗费较多的时间。另外与乐观锁相对应的，悲观锁是由数据库自己实现了的，要用的时候，我们直接调用数据库的相关语句就可以了。

**悲观锁有两种，共享锁（读锁）和排他锁（写锁）。**

## 共享锁（Shared Lock，也叫S锁）
共享锁也叫读锁(S)。

共享锁表示对数据进行读操作。因此多个事务可以同时为一个对象加共享锁。

语法
```
LOCK IN SHARE MODE
```

打开查询窗口1，开启一个事务，但不执行 commit：
```sql
mysql> BEGIN; #开启事务
mysql> SELECT * FROM dev_resource WHERE id = 1 LOCK IN SHARE MODE; #加上共享锁
+----+-------------+
| id | description |
+----+-------------+
|  1 | 123456      |
+----+-------------+
1 row in set
mysql> # COMMIT;
```

打开查询窗口2，执行 update：
```sql
mysql> UPDATE dev_resource SET description = 'des' WHERE id = 1;
1205 - Lock wait timeout exceeded; try restarting transaction
```
此时，操作进入了卡顿状态，过几秒后，提示错误信息。

说明对于 id=1 的记录加锁成功了，在上一条记录还没有 commit 之前，这条 id=1 的记录被锁住了，只有在上一个事务释放掉锁后才能进行操作，或用共享锁才能对此数据进行操作。

再实验一下：
```sql
mysql> UPDATE t SET description = '123' WHERE id = 1 LOCK IN SHARE MODE;
1064 - You have an error in your SQL syntax; check the manual that corresponds to your MySQL server version for the right syntax to use near 'LOCK IN SHARE MODE' at line 1
```
加上共享锁后，update 提示错误信息了，因为对于 update、insert、delete 操作，mysql 会自动加上排它锁。

再次实验：
```sql
mysql> SELECT * FROM t WHERE id = 1 LOCK IN SHARE MODE;
+----+-------------+
| id | description |
+----+-------------+
|  1 | 123456      |
+----+-------------+
1 row in set
```
加上共享锁之后，另一个 select 操作也成功了。

# 排它锁（Exclusive Lock，也叫X锁）
排他锁也叫写锁(X)。

排他锁表示对数据进行写操作。如果一个事务对对象加了排他锁，其他事务就不能再给它加任何锁了。

语法
```
FOR UPDATE
```

打开查询窗口1，开启一个事务，但不执行 commit：
```sql
mysql> BEGIN;
mysql> SELECT * FROM t WHERE id = 1 FOR UPDATE;
+----+-------------+
| id | description |
+----+-------------+
|  1 | 123456      |
+----+-------------+
1 row in set
mysql> # COMMIT;
```

打开查询窗口2，执行 update：
```sql
mysql> UPDATE t SET description = '123' WHERE id = 1;
1205 - Lock wait timeout exceeded; try restarting transaction
```

再实验一下：
```sql
mysql> BEGIN;
mysql> SELECT * FROM t WHERE id = 1 FOR UPDATE;
1205 - Lock wait timeout exceeded; try restarting transaction
```
再次说明，如果一个事务对对象加了排他锁，其他事务就不能再给它加任何锁了。
