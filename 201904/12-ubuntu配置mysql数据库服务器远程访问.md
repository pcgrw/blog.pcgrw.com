### ubuntu配置mysql数据库服务器远程访问步骤：
#### 1.修改配置文件/etc/mysql/mysql.conf.d/mysqld.cnf
```bash
vim /etc/mysql/mysql.conf.d/mysqld.cnf
```
注释掉下面内容：
```bash
#
# Instead of skip-networking the default is now to listen only on
# localhost which is more compatible and is not less secure.
#bind-address           = 127.0.0.1
```
保存并退出

#### 2.重启mysql服务器：
```bash
service mysql restart
```

#### 3.使用root用户登录数据库，更新mysql库中的user表数据：

```
mysql> use mysql
Reading table information for completion of table and column names
You can turn off this feature to get a quicker startup with -A

Database changed

mysql> select host,user from user;
+-----------+------------------+
| host      | user             |
+-----------+------------------+
| localhost | debian-sys-maint |
| localhost | mysql.session    |
| localhost | mysql.sys        |
| localhost | root             |
| localhost | test             |
+-----------+------------------+
5 rows in set (0.00 sec)
mysql> update user set host='%' where user='test';
Query OK, 1 row affected (0.00 sec)
Rows matched: 1  Changed: 1  Warnings: 0

mysql> select host,user from user;
+-----------+------------------+
| host      | user             |
+-----------+------------------+
| %         | test             |
| localhost | debian-sys-maint |
| localhost | mysql.session    |
| localhost | mysql.sys        |
| localhost | root             |
+-----------+------------------+
5 rows in set (0.00 sec)

mysql> FLUSH PRIVILEGES;
Query OK, 0 rows affected (0.00 sec)

mysql> quit
```
**字段解释：**

- host：是指用哪一台主机进行连接，可以是具体IP地址，也可以为'%',是指所有远程机器都可以进行访问

#### 4.在其他主机上远程连接test用户进行测试，测试成功。


