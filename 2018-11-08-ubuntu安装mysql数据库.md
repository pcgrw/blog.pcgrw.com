---

title: ubuntu安装mysql数据库

categories:

- Linux
- Mysql

tags:

- Linux
- Mysql

abbrlink: af8df786

date: 2018-11-08 20:41:31

---

1. 执行安装命令：

	```
	sudo apt install mysql-server
	```

2. 使用命令登录mysql:

	```
	mysql -u root
	```

	**报错信息：Mysql:ERROR 1698 (28000): Access denied for user 'root'@'localhost'**

3. 使用cat命令查看默认用户名密码：

	```
	sudo cat /etc/mysql/debian.cnf
	```

	打印信息如下：

	```
	# Automatically generated for Debian scripts. DO NOT TOUCH!
	[client]
	host     = localhost
	user     = debian-sys-maint
	password = 8W1mSxvxakfmBfe0
	socket   = /var/run/mysqld/mysqld.sock
	[mysql_upgrade]
	host     = localhost
	user     = debian-sys-maint
	password = 8W1mSxvxakfmBfe0
	socket   = /var/run/mysqld/mysqld.sock
	```

4. 使用默认用户名密码登录：

	```
	panchao@panchao-GE62-6QF:~$ mysql -udebian-sys-maint -p
	Enter password: 
	Welcome to the MySQL monitor.  Commands end with ; or \g.
	Your MySQL connection id is 14
	Server version: 5.7.24-0ubuntu0.18.04.1 (Ubuntu)

	Copyright (c) 2000, 2018, Oracle and/or its affiliates. All rights reserved.

	Oracle is a registered trademark of Oracle Corporation and/or its
	affiliates. Other names may be trademarks of their respective
	owners.

	Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

	mysql> 
	```

5. 修改root用户密码：

	```
	mysql> update mysql.user set authentication_string=password('123456'), plugin='mysql_native_password' where user='root';
	mysql> flush privileges;
	mysql> quit
	```

6. 重新使用root用户登录：

	```
	panchao@panchao-GE62-6QF:~$ mysql -uroot -p
	Enter password: 
	Welcome to the MySQL monitor.  Commands end with ; or \g.
	Your MySQL connection id is 15
	Server version: 5.7.24-0ubuntu0.18.04.1 (Ubuntu)

	Copyright (c) 2000, 2018, Oracle and/or its affiliates. All rights reserved.

	Oracle is a registered trademark of Oracle Corporation and/or its
	affiliates. Other names may be trademarks of their respective
	owners.

	Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

	mysql> 
	```

7. 完成安装。
