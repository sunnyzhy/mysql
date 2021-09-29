# mysqlbinlog 用法

## 以 SQL_ERROR 1032 为例

SQL_ERROR 1032 的原因是由于 ```主主（主从）同步```，两边数据库数据不一致引起的。

现有两台主服务器:

- node01(master)
- node02(master)

### 1 先查看 log_error 文件的位置

```bash
mysql> show variables like 'log_error';
+---------------+---------------------+
| Variable_name | Value               |
+---------------+---------------------+
| log_error     | /var/log/mysqld.log |
+---------------+---------------------+
```

### 2 查看主服务器 node01 的 mysqld.log

输出 ```主服务器 node01``` 的  mysqld.log 文件的后 200 行中包含 1032 的日志内容， ```-n 200``` 里的参数 ```200``` 可以根据实际的日志内容自行调整。

```bash
# tail -n 200 /var/log/mysqld.log | grep "1032"
2021-09-29T01:20:12.344981Z 2 [Note] Slave SQL for channel '': Could not execute Update_rows event on table st_quartz.qrtz_scheduler_state; Can't find record in 'qrtz_scheduler_state', Error_code: 1032; handler error HA_ERR_KEY_NOT_FOUND; the event's master log node02-bin.000001, end_log_pos 813, Error_code: 1032
2021-09-29T01:20:12.345307Z 2 [Note] Slave SQL for channel '': Could not execute Update_rows event on table st_quartz.qrtz_scheduler_state; Can't find record in 'qrtz_scheduler_state', Error_code: 1032; handler error HA_ERR_KEY_NOT_FOUND; the event's master log node02-bin.000001, end_log_pos 1194, Error_code: 1032
2021-09-29T01:20:12.345444Z 2 [Note] Slave SQL for channel '': Could not execute Update_rows event on table st_quartz.qrtz_scheduler_state; Can't find record in 'qrtz_scheduler_state', Error_code: 1032; handler error HA_ERR_KEY_NOT_FOUND; the event's master log node02-bin.000001, end_log_pos 1575, Error_code: 1032
2021-09-29T01:20:12.345548Z 2 [Note] Slave SQL for channel '': Could not execute Delete_rows event on table st_quartz.qrtz_scheduler_state; Can't find record in 'qrtz_scheduler_state', Error_code: 1032; handler error HA_ERR_KEY_NOT_FOUND; the event's master log node02-bin.000001, end_log_pos 1909, Error_code: 1032
2021-09-29T01:20:12.345651Z 2 [Note] Slave SQL for channel '': Could not execute Update_rows event on table st_quartz.qrtz_scheduler_state; Can't find record in 'qrtz_scheduler_state', Error_code: 1032; handler error HA_ERR_KEY_NOT_FOUND; the event's master log node02-bin.000001, end_log_pos 2290, Error_code: 1032
2021-09-29T01:20:12.345749Z 2 [Note] Slave SQL for channel '': Could not execute Update_rows event on table st_quartz.qrtz_scheduler_state; Can't find record in 'qrtz_scheduler_state', Error_code: 1032; handler error HA_ERR_KEY_NOT_FOUND; the event's master log node02-bin.000001, end_log_pos 2671, Error_code: 1032
2021-09-29T01:20:12.345845Z 2 [Note] Slave SQL for channel '': Could not execute Update_rows event on table st_quartz.qrtz_scheduler_state; Can't find record in 'qrtz_scheduler_state', Error_code: 1032; handler error HA_ERR_KEY_NOT_FOUND; the event's master log node02-bin.000001, end_log_pos 3052, Error_code: 1032
```

从输出的日志中，继续细化并查找所有包含 HA_ERR_KEY_NOT_FOUND 的日志内容。

```bash
# cat /var/log/mysqld.log | grep -C 0 -in 'HA_ERR_KEY_NOT_FOUND'
18136:2021-09-28T09:40:24.991134Z 2 [ERROR] Slave SQL for channel '': Could not execute Update_rows event on table st_quartz.qrtz_scheduler_state; Can't find record in 'qrtz_scheduler_state', Error_code: 1032; handler error HA_ERR_KEY_NOT_FOUND; the event's master log node02-bin.000003, end_log_pos 9148, Error_code: 1032
--
18281:2021-09-28T09:55:56.044287Z 2 [ERROR] Slave SQL for channel '': Could not execute Update_rows event on table st_quartz.qrtz_scheduler_state; Can't find record in 'qrtz_scheduler_state', Error_code: 1032; handler error HA_ERR_KEY_NOT_FOUND; the event's master log node02-bin.000003, end_log_pos 9148, Error_code: 1032
--
18431:2021-09-28T14:29:02.220161Z 2 [ERROR] Slave SQL for channel '': Could not execute Update_rows event on table st_quartz.qrtz_scheduler_state; Can't find record in 'qrtz_scheduler_state', Error_code: 1032; handler error HA_ERR_KEY_NOT_FOUND; the event's master log node02-bin.000003, end_log_pos 9148, Error_code: 1032
--
18612:2021-09-28T15:35:27.416286Z 2 [ERROR] Slave SQL for channel '': Could not execute Update_rows event on table st_quartz.qrtz_scheduler_state; Can't find record in 'qrtz_scheduler_state', Error_code: 1032; handler error HA_ERR_KEY_NOT_FOUND; the event's master log node02-bin.000001, end_log_pos 813, Error_code: 1032
--
18741:2021-09-29T01:20:12.344981Z 2 [Note] Slave SQL for channel '': Could not execute Update_rows event on table st_quartz.qrtz_scheduler_state; Can't find record in 'qrtz_scheduler_state', Error_code: 1032; handler error HA_ERR_KEY_NOT_FOUND; the event's master log node02-bin.000001, end_log_pos 813, Error_code: 1032
18742:2021-09-29T01:20:12.345307Z 2 [Note] Slave SQL for channel '': Could not execute Update_rows event on table st_quartz.qrtz_scheduler_state; Can't find record in 'qrtz_scheduler_state', Error_code: 1032; handler error HA_ERR_KEY_NOT_FOUND; the event's master log node02-bin.000001, end_log_pos 1194, Error_code: 1032
18743:2021-09-29T01:20:12.345444Z 2 [Note] Slave SQL for channel '': Could not execute Update_rows event on table st_quartz.qrtz_scheduler_state; Can't find record in 'qrtz_scheduler_state', Error_code: 1032; handler error HA_ERR_KEY_NOT_FOUND; the event's master log node02-bin.000001, end_log_pos 1575, Error_code: 1032
18744:2021-09-29T01:20:12.345548Z 2 [Note] Slave SQL for channel '': Could not execute Delete_rows event on table st_quartz.qrtz_scheduler_state; Can't find record in 'qrtz_scheduler_state', Error_code: 1032; handler error HA_ERR_KEY_NOT_FOUND; the event's master log node02-bin.000001, end_log_pos 1909, Error_code: 1032
18745:2021-09-29T01:20:12.345651Z 2 [Note] Slave SQL for channel '': Could not execute Update_rows event on table st_quartz.qrtz_scheduler_state; Can't find record in 'qrtz_scheduler_state', Error_code: 1032; handler error HA_ERR_KEY_NOT_FOUND; the event's master log node02-bin.000001, end_log_pos 2290, Error_code: 1032
18746:2021-09-29T01:20:12.345749Z 2 [Note] Slave SQL for channel '': Could not execute Update_rows event on table st_quartz.qrtz_scheduler_state; Can't find record in 'qrtz_scheduler_state', Error_code: 1032; handler error HA_ERR_KEY_NOT_FOUND; the event's master log node02-bin.000001, end_log_pos 2671, Error_code: 1032
18747:2021-09-29T01:20:12.345845Z 2 [Note] Slave SQL for channel '': Could not execute Update_rows event on table st_quartz.qrtz_scheduler_state; Can't find record in 'qrtz_scheduler_state', Error_code: 1032; handler error HA_ERR_KEY_NOT_FOUND; the event's master log node02-bin.000001, end_log_pos 3052, Error_code: 1032
```

### 3 依次分析错误日志

先分析 ```主服务器 node01``` 的第一条日志:

```
18136:2021-09-28T09:40:24.991134Z 2 [ERROR] Slave SQL for channel '': Could not execute Update_rows event on table st_quartz.qrtz_scheduler_state; Can't find record in 'qrtz_scheduler_state', Error_code: 1032; handler error HA_ERR_KEY_NOT_FOUND; the event's master log node02-bin.000003, end_log_pos 9148, Error_code: 1032
```

此事件在 ```主服务器 node02``` 中的位置是 ```node02-bin.000003, end_log_pos 9148```

#### 3.1 查看主服务器 node02 的 log_bin 日志

在 ```主服务器 node02``` 用 mysqlbinlog 找到该条数据 event SQL:

```bash
# /usr/bin/mysqlbinlog --no-defaults --base64-output=decode-rows -vv /var/lib/mysql/node02-bin.000003 --stop-position=9148 | tail -25
# at 1713
#210929  9:39:02 server id 2  end_log_pos 1788 CRC32 0x43048e89 	Table_map: `st_quartz`.`qrtz_scheduler_state` mapped to number 108
# at 1788
#210929  9:39:02 server id 2  end_log_pos 1916 CRC32 0x77341573 	Update_rows: table id 108 flags: STMT_END_F
### UPDATE `st_quartz`.`qrtz_scheduler_state`
### WHERE
###   @1='st-job' /* VARSTRING(360) meta=360 nullable=0 is_null=0 */
###   @2='node021632843338711' /* VARSTRING(600) meta=600 nullable=0 is_null=0 */
###   @3=1632879532228 /* LONGINT meta=0 nullable=0 is_null=0 */
###   @4=10000 /* LONGINT meta=0 nullable=0 is_null=0 */
### SET
###   @1='st-job' /* VARSTRING(360) meta=360 nullable=0 is_null=0 */
###   @2='node021632843338711' /* VARSTRING(600) meta=600 nullable=0 is_null=0 */
###   @3=1632879542243 /* LONGINT meta=0 nullable=0 is_null=0 */
###   @4=10000 /* LONGINT meta=0 nullable=0 is_null=0 */
# at 1916
#210929  9:39:02 server id 2  end_log_pos 1947 CRC32 0x97e4573d 	Xid = 14626
COMMIT/*!*/;
# at 1947
#210929  9:39:04 server id 2  end_log_pos 1970 CRC32 0xdcf9eb17 	Stop
SET @@SESSION.GTID_NEXT= 'AUTOMATIC' /* added by mysqlbinlog */ /*!*/;
DELIMITER ;
# End of log file
/*!50003 SET COMPLETION_TYPE=@OLD_COMPLETION_TYPE*/;
/*!50530 SET @@SESSION.PSEUDO_SLAVE_MODE=0*/;
```

#### 3.2 还原丢失的数据

从步骤 3.1 中检索出来的结果，其 sql 语句为:

```sql
UPDATE `st_quartz`.`qrtz_scheduler_state` SET @1='st-job',@2='node021632843338711,@3=1632879542243,@4=10000 WHERE @1='st-job' AND @2='node021632843338711' AND @3=1632879532228 AND @4=10000;
```

其中 ```@1 @2 @3...``` 分别对应表 ```st_quartz.qrtz_scheduler_state``` 的列名。

此时可以逆向此 SQL, 把 UPDATE SQL 改成 INSERT SQL, 然后手动在 ```主库 node02``` 上执行此 INSERT SQL, 最后重启 node02 数据库。
