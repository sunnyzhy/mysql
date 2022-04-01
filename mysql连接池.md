# mysql 连接池

连接池参数:

- type: 数据源的类型，如 ```com.alibaba.druid.pool.DruidDataSource```

- driverClassName: 数据库驱动的名称，如 ```com.mysql.cj.jdbc.Driver```

- url: 数据库的连接信息，如 ```jdbc:mysql://localhost:3306/database```

- username: 登陆数据库的用户名

- password: 登陆数据库的密码

- maxActive: 最大连接数，如果取值为 20，表示同时最多只能有 20 个数据库连接。设为 ```0``` 表示无限制。

- maxIdle: 最大的空闲连接数，如果取值为 20，表示即使没有数据库连接时，依然可以保持 20 个空闲的连接，而不被清除。设为 ```0``` 表示无限制。

- maxWait: 建立连接时的最大等待时间, 单位为 ```ms```。设为 ```-1``` 表示无限制。
