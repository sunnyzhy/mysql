# The user specified as a definer ('root'@'%') does not exist
调用存储过程时，总是提示错误：java.sql.SQLException: The user specified as a definer ('root'@'%') does not exist
解决方法：
```
mysql> UPDATE mysql.proc SET definer='root@localhost' WHERE name='' AND db='';

mysql> FLUSH PRIVILEGES;
```
- mysql.proc是系统常量
- definer是"用户名@IP"
- name是存储过程名称
- db是数据库名称
