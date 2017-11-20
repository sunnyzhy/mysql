# 安装
```
# yum -y install phpmyadmin
```

# 访问phpMyAdmin
- apache环境 http://127.0.0.1/phpMyAdmin/
- nginx环境 http://127.0.0.1/phpMyAdmin/index.php

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
