# 安装php
```
# yum -y update

# yum -y install php php-mysql php-fpm

# php -v
PHP 7.1.11 (cli) (built: Oct 25 2017 10:24:28) ( NTS )
Copyright (c) 1997-2017 The PHP Group
Zend Engine v3.1.0, Copyright (c) 1998-2017 Zend Technologies
```

```
CGI是个协议，为了保证web server传递过来的数据是标准格式的，方便CGI程序的编写者。

FastCGI是用来提高CGI程序性能的。

PHP-FPM是一个实现了FastCGI的程序，被PHP官方收购了。
```
        
# 配置apache环境
```
# echo -e "<?php\nphpinfo();\n?>" > /var/www/html/info.php
```

# 配置nginx环境，需要开启php-fpm服务
```
# systemctl start php-fpm

# netstat -tln | grep 9000
tcp        0      0 127.0.0.1:9000          0.0.0.0:*               LISTEN

# cd /usr/local/nginx

# vim conf/nginx.conf
        # pass the PHP scripts to FastCGI server listening on 127.0.0.1:9000
        #
        location ~ \.php$ {
            root           html;
            fastcgi_pass   127.0.0.1:9000;
            fastcgi_index  index.php;
            fastcgi_param  SCRIPT_FILENAME  $document_root$fastcgi_script_name;
            include        fastcgi_params;
        }

# ./sbin/nginx -s reload

# echo -e "<?php\nphpinfo();\n?>" > html/info.php
```

# 访问info.php
http://127.0.0.1/info.php

# 关闭SELinux
```
不关闭SELinux的话，php连接mysql会提示"Permission Denied"。
```

```
# getenforce
Enabled

# vim /etc/selinux/config
SELINUX=disabled
```

# 重启
```
# shutdown -r 0

# getenforce
Disabled
```

# 测试mysql连接
```
# cd /var/www/html | # cd /usr/local/nginx/html

# vim mysql.php
<?php
try {
    $conn = mysqli_connect("localhost","root","root","mysql");
} catch (Exception $e) {
    echo $e->getMessage();
}
if (mysqli_connect_errno($conn)) {
    echo "连接 MySQL 失败: " . mysqli_connect_error();
} else {
    echo "连接 MySQL 成功.";
}
?>
```

# 访问mysql.php
http://127.0.0.1/mysql.php
