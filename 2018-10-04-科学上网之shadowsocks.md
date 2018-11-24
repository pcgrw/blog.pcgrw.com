---

title: 科学上网之shadowsocks

categories:

- 常用工具使用
- shadowsocks

tags:

- shadowsocks

abbrlink: 3f041d08

date: 2018-10-04 22:32:17

---

> 中国有座防火长城(英文名称Great Firewall of China，简写为Great Firewall，缩写GFW),墙外的人想进来，墙内的人想出去。

<!-- more -->

# 科学上网之shadowsocks #

**科学上网之shadowsocks，什么是科学上网？**

科学上网，即在中国国内，利用一些手段或者软件，可以访问国外的网络的一种方式，也俗称“翻墙”。

对于软件开发人员来讲，掌握科学上网方式应该人手必备，毕竟在计算机领域先进的技术都是从国外引进的，长期待在GFW之内会导致与先进技术脱节问题，所以掌握科学上网技术是IT从业者必备技能。废话不多讲了，让我们来看看如何科学上网。

## 科学上网之shadowsocks步骤： ##

### 1.首先要有一个可以访问外网(国外网站)的vps服务器； ###

### 2.在vps服务器上安装shadowsocks服务端： ###

#### 2.1.安装shadowsocks服务端 ####

```bash
sudo apt update

sudo apt-get update

sudo apt-get install python-pip

sudo pip install shadowsocks
```

#### 2.2.创建配置文件并配置 ####

```bash
vi /etc/shadowsocks.json
```

**在/etc/shadowsocks.json中配置：**  

```bash
{ 
"server":"my_server_ip", 
"server_port":25, 
"local_address": "127.0.0.1", 
"local_port":1080, 
"password":"mypassword",
"timeout":300, 
"method":"aes-256-cfb", 
"fast_open": false
}
```

填好以后保存退出。

**配置项列表：**

配置项键 | 配置项值
--- | ---
server | 你的VPS服务器的IP地址
server_port | 你的shadowsocks服务端口。一般可以填一个1025到49151之间的数字。不过如果使用一个知名端口，比如25（电子邮件）、21（FTP），“可能”会更安全，因为GFW对这些基础互联网服务下手的可能性似乎会小一些。注意不要和你的VPS上已经有的服务冲突。
local_address | 本地IP地址，作为服务器使用的时候可以不用关注，填127.0.0.1即可。
local_port | 本地端口，也不用关注。
password | 你的shadowsocks服务密码，客户端连接时需要填写的。
timeout | 超时时间
method | 加密方式，建议填写aes-256-cfb，安全性比较高。
fast_open | 默认false

#### 2.3.后台启动和停止shadowsocks ####

```bash
#后台启动
ssserver -c /etc/shadowsocks.json -d start
#后台停止
ssserver -c /etc/shadowsocks.json -d stop
```

查看shadowsocks的日志：

```bash
vi /var/log/shadowsocks.log
```

### 3.在本地安装shadowsocks客户端： ###

- [Windows客户端下载](https://github.com/shadowsocks/shadowsocks-windows/releases?utm_source=textarea.com&utm_medium=textarea.com&utm_campaign=article)
- [macOS客户端下载](https://github.com/shadowsocks/ShadowsocksX-NG/releases?utm_source=textarea.com&utm_medium=textarea.com&utm_campaign=article)
- [Android客户端下载](https://github.com/shadowsocks/shadowsocks-android/releases?utm_source=textarea.com&utm_medium=textarea.com&utm_campaign=article)

**客户端配置：**

配置名 | 配置值
--- | ---
服务器IP | shadowsocks服务端IP地址
服务器端口 | shadowsocks服务端服务器端口
密码 | 服务端配置的密码
加密 | 服务端配置的加密方式
代理端口 | 1080

配置好后启动客户端。

打开浏览器访问国外网站，例如 www.google.com ，如果配置无误，就可以正常访问google网站了。