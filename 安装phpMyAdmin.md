# 安装
```
# yum -y install phpmyadmin
```

# 修改host
```
# vim /etc/phpMyAdmin/config.inc.php
$cfg['Servers'][$i]['host']          = '127.0.0.1';
```

# loaclhost和127.0.0.1是不能等价的
```
loaclhost是一个name,这个name一般情况下代表了127.0.0.1这个IP地址，也就是说，当访问loaclhost的时候，计算机会通过DNS或者NBNS或者hosts这个文件将它翻译成127.0.0.1.

如果使用loaclhost去链接数据库，则Mysql会使用unixsocket去链接。如果指定为127.0.0.1，则会使用TCP/IP来链接。

所以，尽量使用127.0.0.1.
```

# 访问phpMyAdmin
http://127.0.0.1/phpMyAdmin/

# 允许远程访问
```
# vim /etc/httpd/conf.d/phpMyAdmin.conf
<Directory /usr/share/phpMyAdmin/>
   AddDefaultCharset UTF-8

   <IfModule mod_authz_core.c>
     # Apache 2.4
     <RequireAny>
       #Require ip 127.0.0.1
       #Require ip ::1
        Require all granted
     </RequireAny>
   </IfModule>
   <IfModule !mod_authz_core.c>
     # Apache 2.2
     Order Deny,Allow
     Deny from All
     #Allow from 127.0.0.1
     #Allow from ::1
      Allow from all granted
   </IfModule>
</Directory>

# systemctl restart httpd
```
