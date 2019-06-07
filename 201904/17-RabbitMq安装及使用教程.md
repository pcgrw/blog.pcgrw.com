#### 一、RabbitMQ简介
RabbitMQ是实现了高级消息队列协议（AMQP）的开源消息代理软件（亦称面向消息的中间件）。RabbitMQ服务器是用Erlang语言编写的，而群集和故障转移是构建在开放电信平台框架上的。所有主要的编程语言均有与代理接口通讯的客户端库。

RabbitMQ是一套开源（MPL）的消息队列服务软件，是由 LShift 提供的一个 Advanced Message Queuing Protocol (AMQP) 的开源实现，由以高性能、健壮以及可伸缩性出名的 Erlang 写成。

AMQP，即Advanced Message Queuing Protocol,一个提供统一消息服务的应用层标准高级消息队列协议,是应用层协议的一个开放标准,为面向消息的中间件设计。基于此协议的客户端与消息中间件可传递消息，并不受客户端/中间件不同产品，不同的开发语言等条件的限制。Erlang中的实现有 RabbitMQ等。

#### 二、RabbitMQ优点：
- 开源、性能优秀，稳定性保障
- 与SpringAMQP完美整合、API丰富
- 集群模式丰富、表达式配置，HA模式，镜像队列模型
保证数据不丢失的前提做到高可靠性、可用性

#### 三、RabbitMQ核心概念：
- **Server**：又称Broker，接受客户端的连接，实现AMQP实体服务
- **Connection**：连接，应用程序与Broker的网络连接
- **Channel**：网络信道，几乎所有的操作都在Channel中进行，Channel是进行消息读写的通道。客户端可建立多个Channel，每个Channel代表一个会话任务。
- **Message**：消息，服务器和应用程序之间传递的数据，由Properties和Body组成。Properties可以对消息进行修饰，比如消息的优先级、延迟等高级特性；Body则就是消息体内容。
- **Virtual host**：虚拟主机地址，用于进行逻辑隔离，最上层的消息路由。一个Virtual host里面可以有若干个Exchange和Queue，同一个Virtual host里面不能有相同名称的Exchange和Queue
- **Exchange**：交换机，接收消息，根据路由键转发消息到绑定的队列
- **Binding**：Exchange和Queue之间的虚拟连接，binding中可以包含routing key
- **Routing key**：一个路由规则，虚拟机可用它来确定如何路由一个特定的消息
- **Queue**：也称Message Queue，消息队列，保存消息并将它们转发给消费者

#### 四、RabbitMQ安装（ubuntu服务器安装）
1.安装Erlang：
```bash
sudo apt install erlang
```

2.安装RabbitMQ服务：
```bash
sudo apt install rabbitmq-server
```

3.查看安装状态：
```bash
service rabbitmq-server status
```
显示如下表示安装成功：
```bash
● rabbitmq-server.service - RabbitMQ Messaging Server
   Loaded: loaded (/lib/systemd/system/rabbitmq-server.service; enabled; vendor preset: enabled)
   Active: active (running) since Thu 2019-04-04 15:26:58 CST; 1min 41s ago
 Main PID: 38001 (rabbitmq-server)
    Tasks: 86 (limit: 4655)
   CGroup: /system.slice/rabbitmq-server.service
           ├─38001 /bin/sh /usr/sbin/rabbitmq-server
           ├─38010 /bin/sh /usr/lib/rabbitmq/bin/rabbitmq-server
           ├─38166 /usr/lib/erlang/erts-9.2/bin/epmd -daemon
           ├─38277 /usr/lib/erlang/erts-9.2/bin/beam.smp -W w -A 64 -P 1048576 -t 5000000 -stbt db -zdbbl 32000 -K true -B i -- -root /usr/lib/erlang -progname erl -- -home /var
           ├─38383 erl_child_setup 65536
           ├─38442 inet_gethost 4
           └─38443 inet_gethost 4
```
#### 五、开启允许远程访问:
打开文件
```bash
vim /etc/rabbitmq/rabbitmq-env.conf
```
添加以下内容：
```bash
[{rabbit,[{loopback_users,[]}]}].
```
#### 六、安装延迟插件:
**第一步**：下载插件

下载地址：http://www.rabbitmq.com/community-plugins.html

**第二步**：把下载好的插件放到指定目录/usr/lib/rabbitmq/lib/rabbitmq_server-3.6.10/plugins

**第三步**：启动插件
```bash
sudo rabbitmq-plugins enable rabbitmq_delayed_message_exchange
```
#### 七、启动WEB管理插件：
```bash
sudo rabbitmq-plugins enable rabbitmq_management
```
重启服务：
```bash
service rabbitmq-server restart
```
访问IP地址：15672可进入后台，默认用户名、密码均为guest。

#### 八、附：常用命令
```bash
sudo chkconfig rabbitmq-server on  #添加开机启动（chkconfig一般只有redhat系统有）RabbitMQ服务
sudo service rabbitmq-server start  # 启动服务
sudo service rabbitmq-server status  # 查看服务状态
sudo service rabbitmq-server stop   # 停止服务
sudo rabbitmqctl stop   # 停止服务
sudo rabbitmqctl status  # 查看服务状态
sudo rabbitmqctl list_users # 查看当前所有用户
sudo rabbitmqctl list_user_permissions guest # 查看默认guest用户的权限
sudo rabbitmqctl delete_user guest# 删掉默认用户(由于RabbitMQ默认的账号用户名和密码都是guest。为了安全起见, 可以删掉默认用户）
sudo rabbitmqctl add_user username password # 添加新用户
sudo rabbitmqctl set_user_tags username administrator# 设置用户tag
sudo rabbitmqctl set_permissions -p / username ".*" ".*" ".*" # 赋予用户默认vhost的全部操作权限
sudo rabbitmqctl list_user_permissions username # 查看用户的权限
```

#### 九、RabbitMq账号级别：
1. 超级管理员administrator，可以登录控制台，查看所有信息可以对用户和策略进行操作
2. 监控者monitoring，可以登录控制台，可以查看节点的相关信息，比如进程数，内存磁盘使用情况
3. 策略定制者policymaker，可以登录控制台，制定策略，但是无法查看节点信息
4. 普通管理员management，仅能登录控制台
5. 其他，无法登录控制台，一般指的是提供者和消费者

#### 十、添加用户账号：
**1.命令模式：**
```bash
#添加账号 admin 密码是 admin
sudo rabbitmqctl add_user admin admin
#设置 admin 为 administrator 级别
sudo rabbitmqctl set_user_tags admin administrator
```
**2.web方式：**
在web管理界面上进行添加

**3.添加虚拟主机**（类似于数据库中的库概念）：
可在web界面进行操作

#### 十一、RabbitMq常用消息类型：

##### 简单模式
提供一个消息队列，生产者和消费者之间进行一对一通讯
##### work模式
提供一个消息队列，生产者和消费者之间进行一对多通讯，生产者发送的消息会按要求分配给各个消费者，一个消息只能被消费一次
##### 发布订阅模式(fanout)
生产者声明一个交换机，各消费者声明一个队列，将队列绑定到指定的交换机上，一个消息可以被所有绑定在该交换机上的消费者同时消费
##### route模式(direct)
在发布订阅模式上的细化
##### topic模式(topic)
routingkey可以进行通配符操作

**通配符**：将路由键和某模式匹配，此时队列需要绑定到一个模式上。符号'#'匹配一个或多个词，符号'*'匹配一个词。

**route模式和topic模式的区别**：route模式routingkey是完全匹配，topic模式routingkey是模糊匹配

#### 十二、rabbitmq与spring整合：
[点击查看源码](https://github.com/pcgrw/mq-demo/tree/master/rabbitmq/src/main/java/com/pcgrw/mqdemo/rabbitmq/spring)
#### 十三、rabbitmq与SpringBoot2.X整合：
[点击查看源码(生产者)](https://github.com/pcgrw/mq-demo/tree/master/sbrabbit-producer)

[点击查看源码(消费者)](https://github.com/pcgrw/mq-demo/tree/master/sbrabbit-consumer)
#### 十四、保障100%的消息可靠性投递方案落地实现
[![RabbitMq安装及使用教程-1](https://wx2.sinaimg.cn/mw690/006lEgJtly1g25tqdi78yj30th0htn1y.jpg "RabbitMq安装及使用教程-1")](https://wx2.sinaimg.cn/mw690/006lEgJtly1g25tqdi78yj30th0htn1y.jpg "Redis五种数据结构学习-1")
[点击查看源码](https://github.com/pcgrw/mq-demo/tree/master/sbrabbit-reliable-producer)
