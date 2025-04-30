# 解决方法

## 方法1

**在[mysqld]节点下添加 lower_case_table_names=1**

```
# vim /etc/my.cnf
[mysqld]
datadir=/var/lib/mysql
socket=/var/lib/mysql/mysql.sock
character-set-server=utf8
default-time_zone = '+8:00'
# Disabling symbolic-links is recommended to prevent assorted security risks
symbolic-links=0
lower_case_table_names=1
log-error=/var/log/mysqld.log
pid-file=/var/run/mysqld/mysqld.pid

# systemctl restart mysqld
```

**注意**
1. lower_case_table_names=1 参数缺省地在 Windows 中值为 1 ，在 Unix 中为 0，因此在window中不会遇到的问题，而在linux中就会出问题的原因。
2. 一定要先修改my.cnf配置，然后再部署数据库，否则无效（表名依旧区分大小写）。

## 方法2

把 Quartz 表名从小写改为大写，以匹配 Quartz 的默认表名规范。

```sql
ALTER TABLE `qrtz_blob_triggers` RENAME TO `QRTZ_BLOB_TRIGGERS`;
ALTER TABLE `qrtz_calendars` RENAME TO `QRTZ_CALENDARS`;
ALTER TABLE `qrtz_cron_triggers` RENAME TO `QRTZ_CRON_TRIGGERS`;
ALTER TABLE `qrtz_fired_triggers` RENAME TO `QRTZ_FIRED_TRIGGERS`;
ALTER TABLE `qrtz_job_details` RENAME TO `QRTZ_JOB_DETAILS`;
ALTER TABLE `qrtz_locks` RENAME TO `QRTZ_LOCKS`;
ALTER TABLE `qrtz_paused_trigger_grps` RENAME TO `QRTZ_PAUSED_TRIGGER_GRPS`;
ALTER TABLE `qrtz_scheduler_state` RENAME TO `QRTZ_SCHEDULER_STATE`;
ALTER TABLE `qrtz_simple_triggers` RENAME TO `QRTZ_SIMPLE_TRIGGERS`;
ALTER TABLE `qrtz_simprop_triggers` RENAME TO `QRTZ_SIMPROP_TRIGGERS`;
ALTER TABLE `qrtz_triggers` RENAME TO `QRTZ_TRIGGERS`;
```
