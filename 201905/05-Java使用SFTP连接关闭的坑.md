#### 一、问题描述：
最近生产服务器上隔三岔五就会报错：too many open files(打开的文件过多)在网上找了一圈，说是由于Linux系统设置的最大文件打开数是1024个，将此设置调大即可解决该问题，但这样操作并不能从根本上解决问题。
#### 二、问题分析：
通过命令ulimit -a可以查看当前系统设置的最大句柄数是多少：
```bash
panchao@panchao-virtual-machine:~$ ulimit -a
core file size          (blocks, -c) 0
data seg size           (kbytes, -d) unlimited
scheduling priority             (-e) 0
file size               (blocks, -f) unlimited
pending signals                 (-i) 15517
max locked memory       (kbytes, -l) 16384
max memory size         (kbytes, -m) unlimited
open files                      (-n) 1024
pipe size            (512 bytes, -p) 8
POSIX message queues     (bytes, -q) 819200
real-time priority              (-r) 0
stack size              (kbytes, -s) 8192
cpu time               (seconds, -t) unlimited
max user processes              (-u) 15517
virtual memory          (kbytes, -v) unlimited
file locks                      (-x) unlimited
```
设置Linux系统的最大文件打开数为2048：
```bash
ulimit -n 2048
```
但这样并不能从根本上解决问题，Linux的默认设置已经够大了，如果系统报错too many open files(打开的文件过多)，我们首先应该查找为什么会打开这么多的文件，而不是改变系统配置。

使用netstat命令
```bash
netstat -anp|grep 192.168.17.140:22
```
查看sfpt连接数，发现连接数就有近1000个，问题找到，是由于没有及时关闭sftp、释放掉连接而导致系统报错的。

然后我查找代码，发现所有的sftp连接都调用了通用关闭方法，但是连接就是没有释放，经过一段时间分析，发现工具类中的通用关闭方法是个大坑，只关闭了sftp通道，而没有关闭Session会话，问题找到，得已解决。

#### 三、问题解决：
由于开启sftp通道的过程中需要先打开一个Session会话，然后再根据Session会话开启一个sftp通道。
```java
ChannelSftp sftp = null;
JSch jsch = new JSch();
Session sshSession = jsch.getSession(username, host, port);
sshSession.setPassword(password);
Properties sshConfig = new Properties();
sshConfig.put("StrictHostKeyChecking", "no");
sshSession.setConfig(sshConfig);
sshSession.connect();
Channel channel = sshSession.openChannel("sftp");
channel.connect();
sftp = (ChannelSftp) channel;
```
所以我们在对sftp进行关闭是需要将sftp通道和Session会话一起关闭，否则不能完全关闭sftp连接。
```java
sftp.disconnect();
channelSftp.getSession().disconnect();
```