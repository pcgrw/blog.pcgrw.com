---

title: ubuntu使用wget下载jdk

categories:

- Linux

tags:

- Linux 

abbrlink: af8df786

date: 2018-11-10 20:41:31

---

使用wget方法去jdk官方网站下载jdk时报错，无法下载：

```
wget "https://download.oracle.com/otn-pub/java/jdk/8u191-b12/2787e4a523244c269598db4e85c51e0c/jdk-8u191-linux-x64.tar.gz"
```

应该使用wget --no-check-certificate --no-cookies --header "Cookie: oraclelicense=accept-securebackup-cookie" 后跟下载地址来下载：

```
wget --no-check-certificate --no-cookies --header "Cookie: oraclelicense=accept-securebackup-cookie" "https://download.oracle.com/otn-pub/java/jdk/8u191-b12/2787e4a523244c269598db4e85c51e0c/jdk-8u191-linux-x64.tar.gz"
```

这样就可以下载成功了。

