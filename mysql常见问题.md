# The user specified as a definer ('root'@'%') does not exist
调用存储过程时，总是提示错误：java.sql.SQLException: The user specified as a definer ('root'@'%') does not exist
解决方法：
```
mysql> update mysql.proc set DEFINER='root@localhost' WHERE NAME='' AND db='';
```
- mysql.proc是系统常量
- definer是"用户名@IP"
- name是存储过程名称
- db是数据库名称
