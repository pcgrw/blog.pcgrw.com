#### 一、Sun Jdk监控与故障处理工具列表：

名称 | 主要作用
---|---
jps | JVM Process Status Tool,显示指定系统内所有的HotSpot虚拟机进程
jstat | JVM Statistics Monitoring Tool,用于收集HotSpot虚拟机各方面的运行数据
jinfo | Configuration Info for Java,显示虚拟机配置信息
jmap | Memory Map for java,生成虚拟机的内存转储快照(heapdump文件)
jhat | JVM Heap Dump Browser,用于分析heapdump文件，它会建立一个HTTP/HTML服务器,让用户可以在浏览器上查看分析结果
jstack | stack Trace for Java,显示虚拟机的线程快照

> LVMID：全称Local Virtual Machine Identifier,本地虚拟机唯一ID

#### 二、jps:虚拟机进程状况工具

##### 1.jps命令格式：
```bash
jps [options] [hostid]
```
##### 2.jps工具主要选项：

选项 | 作用
---|---
-q | 只输出LVMID,省略主类的名称
-m | 输出虚拟机进程启动时传递给主类main()函数的参数
-l | 输出主类的全名，如果进程执行的是jar包，输出jar路径
-v | 输出虚拟机进程启动时JVM参数

##### 3.jps实战操作：

```bash
ubuntu@ip-172-31-34-22:~$ jps
30305 Jps
31352 jar
ubuntu@ip-172-31-34-22:~$ jps -q
31352
30315
ubuntu@ip-172-31-34-22:~$ jps -m
11332 Test 参数一 参数二
30325 Jps -m
31352 jar
ubuntu@ip-172-31-34-22:~$ jps -l
31352 website-0.0.1-SNAPSHOT.jar
30335 sun.tools.jps.Jps
ubuntu@ip-172-31-34-22:~$ jps -v
31352 jar -Xms128m -Xmx512m
30346 Jps -Denv.class.path=.:/opt/jdk/jdk1.8.0_191/lib/dt.jar:/opt/jdk/jdk1.8.0_191/lib/tools.jar -Dapplication.home=/opt/jdk/jdk1.8.0_191 -Xms8m
```

#### 三、jstat:虚拟机统计信息监视工具

##### 1.jstat命令格式：
```bash
jstat [options vmid [interval[s|ms] [count]]]
```

> 对于命令格式中的VMID和LVMID需要特别说明一下：如果是本地虚拟机进程，VMID与LVMID是一致的，如果是远程虚拟机进程，那VMID的格式应为：
`[protocol:][//]lvmid[@hostname[:port]/servername]`

参数interval和count分别代表查询间隔和次数，如果省略这连个参数，说明只查询一次。

**例如：**
```bash
jstat -gc 31352 250 20
```

是指每250毫秒查询一次进程31352垃圾收集状况，一共查询20次。

##### 2.jsat工具主要选项：
> 选项options代表着用户需要查询的虚拟机信息，主要分为3类：类装载、垃圾收集、运行期编译状况，具体选项及作用请参考下表。

选项 | 作用
---|---
-class | 监视类装载、卸载数量、总空间以及类装载所耗费的时间
-gc | 监视Java堆状况。包括Eden区、连个Survivor、老年代、永久代等的容量、已用空间、GC时间合计等信息
-gccapacity | 监视内容与-gc基本相同，但输出主要关注Java堆各个区域使用到的最大、最小空间
-gcutil | 监视内容与-gc基本相同，但输出主要关注已使用空间占总空间的百分比
-gccause | 与-gcutil功能一样，但是会额外输出导致上一次GC产生的原因
-gcnew | 监视新生代GC状况
-gcnewcapacity | 监视内容与-gcnew基本相同，输出主要关注使用到的最大、最小空间
-gcold | 监视老年代GC状况
-gcoldcapacity | 监视内容与-gcold基本相同，输出主要关注使用到的最大、最小空间
-gcpermcapacity | 输出永久代使用到的最大、最小空间
-compiler | 输出JIT编译器编译过的方法、耗时等信息
-printcompilation | 输出已经被JIT编译的方法

##### 3.jstat实战操作：

```bash
root@ip-172-31-34-22:/home/ubuntu# jstat -gcutil 31352
  S0     S1     E      O      M     CCS    YGC     YGCT    FGC    FGCT     GCT   
  0.00   0.19   5.48  24.27  95.71  92.77   2549    5.764     3    0.133    5.897
```

**解释：**
- S0：表示新生代中第一个Survivor（幸存区）已使用的占当前容量百分比
- S1：表示新生代中第二个Survivor（幸存区）已使用的占当前容量百分比
- E：表示新生代中Eden（伊甸园）已使用的占当前容量百分比
- O：表示老年代Old区已使用的占当前容量百分比
- M：元数据区使用比例
- P ：表示Permanent永久代已使用的占当前容量百分比
- YGC：表示程序运行以来共发生Minor GC(YGC,表示Young GC)的次数
- YGCT：表示程序运行以来共发生Minor GC(YGC,表示Young GC)的总耗时(单位：秒)
- FGC：表示程序运行以来共发Full GC的次数
- FGCT：表示程序运行以来共发Full GC的总耗时(单位：秒)
- GCT：表示(GC Time)程序运行以来所有GC总耗时

#### 四、jinfo:Java配置信息工具

##### 1.jinfo命令格式：
```bash
jinfo [option] pid
```

##### 2.jinfo实战操作：

使用jinfo查看配置信息，提示没有权限
```bash
ubuntu@ip-172-31-34-22:/opt/jdk$ jinfo 31352
Attaching to process ID 31352, please wait...
Error attaching to process: sun.jvm.hotspot.debugger.DebuggerException: Can't attach to the process: ptrace(PTRACE_ATTACH, ..) failed for 31352: Operation not permitted
sun.jvm.hotspot.debugger.DebuggerException: sun.jvm.hotspot.debugger.DebuggerException: Can't attach to the process: ptrace(PTRACE_ATTACH, ..) failed for 31352: Operation not permitted
	at sun.jvm.hotspot.debugger.linux.LinuxDebuggerLocal$LinuxDebuggerLocalWorkerThread.execute(LinuxDebuggerLocal.java:163)
	at sun.jvm.hotspot.debugger.linux.LinuxDebuggerLocal.attach(LinuxDebuggerLocal.java:278)
	at sun.jvm.hotspot.HotSpotAgent.attachDebugger(HotSpotAgent.java:671)
	at sun.jvm.hotspot.HotSpotAgent.setupDebuggerLinux(HotSpotAgent.java:611)
	at sun.jvm.hotspot.HotSpotAgent.setupDebugger(HotSpotAgent.java:337)
	at sun.jvm.hotspot.HotSpotAgent.go(HotSpotAgent.java:304)
	at sun.jvm.hotspot.HotSpotAgent.attach(HotSpotAgent.java:140)
	at sun.jvm.hotspot.tools.Tool.start(Tool.java:185)
	at sun.jvm.hotspot.tools.Tool.execute(Tool.java:118)
	at sun.jvm.hotspot.tools.JInfo.main(JInfo.java:138)
	at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
	at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:62)
	at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
	at java.lang.reflect.Method.invoke(Method.java:498)
	at sun.tools.jinfo.JInfo.runTool(JInfo.java:108)
	at sun.tools.jinfo.JInfo.main(JInfo.java:76)
Caused by: sun.jvm.hotspot.debugger.DebuggerException: Can't attach to the process: ptrace(PTRACE_ATTACH, ..) failed for 31352: Operation not permitted
	at sun.jvm.hotspot.debugger.linux.LinuxDebuggerLocal.attach0(Native Method)
	at sun.jvm.hotspot.debugger.linux.LinuxDebuggerLocal.access$100(LinuxDebuggerLocal.java:62)
	at sun.jvm.hotspot.debugger.linux.LinuxDebuggerLocal$1AttachTask.doit(LinuxDebuggerLocal.java:269)
	at sun.jvm.hotspot.debugger.linux.LinuxDebuggerLocal$LinuxDebuggerLocalWorkerThread.run(LinuxDebuggerLocal.java:138)

```
解决办法，执行下面代码:
```bash
echo 0 | sudo tee /proc/sys/kernel/yama/ptrace_scope
```
再次执行时就可以得到结果了
```bash
ubuntu@ip-172-31-34-22:/opt/jdk$ jinfo 31352
Attaching to process ID 31352, please wait...
Debugger attached successfully.
Server compiler detected.
JVM version is 25.191-b12
Java System Properties:

java.runtime.name = Java(TM) SE Runtime Environment
java.vm.version = 25.191-b12
sun.boot.library.path = /opt/jdk/jdk1.8.0_191/jre/lib/amd64
java.protocol.handler.pkgs = org.springframework.boot.loader
java.vendor.url = http://java.oracle.com/
java.vm.vendor = Oracle Corporation
path.separator = :
file.encoding.pkg = sun.io
java.vm.name = Java HotSpot(TM) 64-Bit Server VM
sun.os.patch.level = unknown
sun.java.launcher = SUN_STANDARD
user.dir = /home/ubuntu/website
java.vm.specification.name = Java Virtual Machine Specification
PID = 31352
java.runtime.version = 1.8.0_191-b12
java.awt.graphicsenv = sun.awt.X11GraphicsEnvironment
os.arch = amd64
java.endorsed.dirs = /opt/jdk/jdk1.8.0_191/jre/lib/endorsed
line.separator = 

java.io.tmpdir = /tmp
java.vm.specification.vendor = Oracle Corporation
os.name = Linux
sun.jnu.encoding = UTF-8
java.library.path = /usr/java/packages/lib/amd64:/usr/lib64:/lib64:/lib:/usr/lib
spring.beaninfo.ignore = true
java.specification.name = Java Platform API Specification
java.class.version = 52.0
sun.management.compiler = HotSpot 64-Bit Tiered Compilers
os.version = 4.15.0-1021-aws
user.home = /home/ubuntu
user.timezone = Etc/UTC
catalina.useNaming = false
java.awt.printerjob = sun.print.PSPrinterJob
file.encoding = UTF-8
java.specification.version = 1.8
catalina.home = /tmp/tomcat.2147445540332286833.8088
user.name = ubuntu
java.class.path = website-0.0.1-SNAPSHOT.jar
java.vm.specification.version = 1.8
sun.arch.data.model = 64
sun.java.command = website-0.0.1-SNAPSHOT.jar
java.home = /opt/jdk/jdk1.8.0_191/jre
user.language = en
java.specification.vendor = Oracle Corporation
awt.toolkit = sun.awt.X11.XToolkit
java.vm.info = mixed mode
java.version = 1.8.0_191
java.ext.dirs = /opt/jdk/jdk1.8.0_191/jre/lib/ext:/usr/java/packages/lib/ext
sun.boot.class.path = /opt/jdk/jdk1.8.0_191/jre/lib/resources.jar:/opt/jdk/jdk1.8.0_191/jre/lib/rt.jar:/opt/jdk/jdk1.8.0_191/jre/lib/sunrsasign.jar:/opt/jdk/jdk1.8.0_191/jre/lib/jsse.jar:/opt/jdk/jdk1.8.0_191/jre/lib/jce.jar:/opt/jdk/jdk1.8.0_191/jre/lib/charsets.jar:/opt/jdk/jdk1.8.0_191/jre/lib/jfr.jar:/opt/jdk/jdk1.8.0_191/jre/classes
java.awt.headless = true
java.vendor = Oracle Corporation
catalina.base = /tmp/tomcat.2147445540332286833.8088
file.separator = /
java.vendor.url.bug = http://bugreport.sun.com/bugreport/
sun.io.unicode.encoding = UnicodeLittle
sun.cpu.endian = little
sun.cpu.isalist = 

VM Flags:
Non-default VM flags: -XX:CICompilerCount=2 -XX:InitialHeapSize=134217728 -XX:MaxHeapSize=536870912 -XX:MaxNewSize=178913280 -XX:MinHeapDeltaBytes=196608 -XX:NewSize=44695552 -XX:OldSize=89522176 -XX:+UseCompressedClassPointers -XX:+UseCompressedOops 
Command line:  -Xms128m -Xmx512m
```
**原因**
这是因为新版的Linux系统加入了 ptrace-scope 机制. 这种机制为了防止用户访问当前正在运行的进程的内存和状态, 而一些调试软件本身就是利用 ptrace 来进行获取某进程的内存状态的(包括GDB),所以在新版本的Linux系统, 默认情况下不允许再访问了. 可以临时开启. 如:`echo 0 > /proc/sys/kernel/yama/ptrace_scope`

- 0表示允许
- 1表示不允许

查询InitialHeapSize参数值：
```bash
ubuntu@ip-172-31-34-22:/opt/jdk$ jinfo -flag InitialHeapSize 31352
-XX:InitialHeapSize=134217728
```

#### 五、jmap:Java内存映像工具

> jmap的作用并不仅仅是为了获取dump文件，它还可以查询finalize执行队列、Java堆和永久代的详细信息，如空间使用率、当前用的是哪种收集器等。

##### 1.jmap命令格式：
```bash
jmap [option] vmid
```

##### 2.jmap工具主要选项：

选项 | 作用
---|---
-dump | 生成Java堆转储快照。格式为：-dump:[live, ]format=b,file=<filename>,其中live子参数说明是否只dump出存活的对象
-finalizerinfo | 显示在F-Queue中等待Finalizer线程执行finalize方法的对象。只在Linux/Solaris平台下有效
-heap | 显示Java堆详细信息，如使用那种回收器、参数配置、分代状况等。只在Linux/Solaris平台下有效
-histo | 显示堆中对象统计信息，包括类、实例数量、合计容量
-permstat | 以ClassLoader为统计口径显示永久代内存状态。只在Linux/Solaris平台下有效
-F | 当虚拟机进程对-dump选项没有响应时，可使用这个选项强制生成dump快照。只在Linux/Solaris平台下有效

##### 3.jmap实战操作：

使用jmap生成dump文件
```bash
jmap -dump:format=b,file=1.dump 31352
```

#### 六、jhat:虚拟机堆转储快照分析工具

> jhat命令与jmap搭配使用，来分析jmap生成的堆转储快照。

##### 1.jhat命令格式：
```bash
jhat dumpFile
```

##### 2.jhat实战操作：

使用jhat分析dump文件
```bash
ubuntu@ip-172-31-34-22:~$ jhat 1.dump 
Reading from 1.dump...
Dump file created Fri Apr 19 08:04:56 UTC 2019
Snapshot read, resolving...
Resolving 486522 objects...
Chasing references, expect 97 dots.................................................................................................
Eliminating duplicate references.................................................................................................
Snapshot resolved.
Started HTTP server on port 7000
Server is ready.
```
打开浏览器访问http://localhost:7000,查看分析结果。

*分析结果默认是以包为单位进行分组显示，分析内存泄漏问题主要会使用到其中的“Heap Histogram”（与jmap-histo功能一样）与OQL页签的功能，前者可以找到内存中总容量最大的对象，后者是标准的对象查询语言，使用类似SQL的语法对内存中的对象进行查询统计。*

#### 七、jstack:Java堆栈跟踪工具

> jstack（Stack Trace for Java）命令用于生成虚拟机当前时刻的线程快照（一般称为threaddump或者javacore文件）。线程快照就是当前虚拟机内每一条线程正在执行的方法堆栈的集合，生成线程快照的主要目的是定位线程出现长时间停顿的原因，如线程间死锁、死循环、请求外部资源导致的长时间等待等都是导致线程长时间停顿的常见原因。线程出现停顿的时候通过jstack来查看各个线程的调用堆栈，就可以知道没有响应的线程到底在后台做些什么事情，或者等待着什么资源。

##### 1.jstack命令格式：
```bash
jstack [option] vmid
```
##### 2.jstack工具主要选项：

选项 | 作用
---|---
-F | 当正常输出的请求不被响应，强制输出线程堆栈
-l | 除堆栈外，显示关于锁的附加信息
-m | 如果调用到本地方法的话，可以显示C/C++的堆栈

##### 3.jstack实战操作：

使用jstack查看线程堆栈
```bash
panchao@panchao-virtual-machine:~$ jstack -l 11131
2019-04-19 17:54:12
Full thread dump Java HotSpot(TM) 64-Bit Server VM (25.171-b11 mixed mode):

"Attach Listener" #8 daemon prio=9 os_prio=0 tid=0x00007f9d1c001000 nid=0x2c1d runnable [0x0000000000000000]
   java.lang.Thread.State: RUNNABLE

   Locked ownable synchronizers:
	- None

"Service Thread" #7 daemon prio=9 os_prio=0 tid=0x00007f9d400c8800 nid=0x2b83 runnable [0x0000000000000000]
   java.lang.Thread.State: RUNNABLE

   Locked ownable synchronizers:
	- None

"C1 CompilerThread1" #6 daemon prio=9 os_prio=0 tid=0x00007f9d400b3000 nid=0x2b82 waiting on condition [0x0000000000000000]
   java.lang.Thread.State: RUNNABLE

   Locked ownable synchronizers:
	- None

"C2 CompilerThread0" #5 daemon prio=9 os_prio=0 tid=0x00007f9d400b1000 nid=0x2b81 waiting on condition [0x0000000000000000]
   java.lang.Thread.State: RUNNABLE

   Locked ownable synchronizers:
	- None

"Signal Dispatcher" #4 daemon prio=9 os_prio=0 tid=0x00007f9d400af800 nid=0x2b80 runnable [0x0000000000000000]
   java.lang.Thread.State: RUNNABLE

   Locked ownable synchronizers:
	- None

"Finalizer" #3 daemon prio=8 os_prio=0 tid=0x00007f9d4007c000 nid=0x2b7f in Object.wait() [0x00007f9d30c22000]
   java.lang.Thread.State: WAITING (on object monitor)
	at java.lang.Object.wait(Native Method)
	- waiting on <0x00000000c2a08ed0> (a java.lang.ref.ReferenceQueue$Lock)
	at java.lang.ref.ReferenceQueue.remove(ReferenceQueue.java:143)
	- locked <0x00000000c2a08ed0> (a java.lang.ref.ReferenceQueue$Lock)
	at java.lang.ref.ReferenceQueue.remove(ReferenceQueue.java:164)
	at java.lang.ref.Finalizer$FinalizerThread.run(Finalizer.java:212)

   Locked ownable synchronizers:
	- None

"Reference Handler" #2 daemon prio=10 os_prio=0 tid=0x00007f9d40077000 nid=0x2b7e in Object.wait() [0x00007f9d30d23000]
   java.lang.Thread.State: WAITING (on object monitor)
	at java.lang.Object.wait(Native Method)
	- waiting on <0x00000000c2a06bf8> (a java.lang.ref.Reference$Lock)
	at java.lang.Object.wait(Object.java:502)
	at java.lang.ref.Reference.tryHandlePending(Reference.java:191)
	- locked <0x00000000c2a06bf8> (a java.lang.ref.Reference$Lock)
	at java.lang.ref.Reference$ReferenceHandler.run(Reference.java:153)

   Locked ownable synchronizers:
	- None

"main" #1 prio=5 os_prio=0 tid=0x00007f9d4000b000 nid=0x2b7c waiting on condition [0x00007f9d480da000]
   java.lang.Thread.State: TIMED_WAITING (sleeping)
	at java.lang.Thread.sleep(Native Method)
	at Test.main(Test.java:13)

   Locked ownable synchronizers:
	- None

"VM Thread" os_prio=0 tid=0x00007f9d4006f800 nid=0x2b7d runnable 

"VM Periodic Task Thread" os_prio=0 tid=0x00007f9d400cd800 nid=0x2b84 waiting on condition 

JNI global references: 5
```