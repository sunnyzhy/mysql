# 安装
~~~
# yum -y install httpd
~~~

# 启动
~~~
# systemctl start httpd
~~~

# 开机启动apache
```
# systemctl enable httpd
```

# 开机不启动apache
```
# systemctl disable httpd
```

# 访问
http://127.0.0.1/

# 目录介绍
~~~
服务目录 	/etc/httpd
主配置文件 	/etc/httpd/conf/httpd.conf
网站数据目录 	/var/www/html
访问日志 	/var/log/httpd/access_log
错误日志 	/var/log/httpd/error_log
~~~
