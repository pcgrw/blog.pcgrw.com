##### 实现多线程的两种方式：
1. 继承Thread类
2. 实现Runnable接口

Thread方法isAlive()的作用是测试线程是否处于活动状态。

##### 什么是线程活动状态？

活动状态就是线程已经启动且尚未终止。线程处于正在运行或准备开始运行的状态，就认为线程是存活的。

##### Java中有三种方法可以终止正在运行的线程：
- 使用退出标志，使线程正常退出，也就是当run方法完成后线程终止。
- 使用stop方法强制终止线程，但是不推荐使用这种方法，因为stop和suspend及resume一样，都是作废过期的方法，使用他们可能产生不可预料的结果。
- 使用interrupt方法中断线程。

##### 测试线程是否中断：

- interrupted()方法：测试当前线程是否已经是中断状态，执行后具有将状态标志清除为false的功能。
- isInterrupted()方法：测试线程Thread对象是否已经是中断状态，但不清除状态标志。

**interrupt方法仅仅是为当前线程标记了一个中断标识，并不能中断线程，必须配合isInterrupted()方法手动抛出InterruptedException异常，才可以中断线程。**

##### 如何停止线程：
1. 异常法：在线程中使用isInterrupted()判断线程状态是否中断，如果中断，则手动抛出InterruptedException异常，即可停止线程。
2. sleep方法停止：在线程运行期间，当线程被执行了interrupt方法后，遇到sleep方法后会停止线程，并抛出InterruptedException异常。
3. stop方法暴力停止：stop方法会抛出ThreadDeath异常，有可能会使一些清理性的工作得不到完成；stop方法也会对锁定的对象进行解锁，导致数据得不到同步处理，出现数据不一致的问题。
4. 使用return停止线程：在线程中使用isInterrupted()判断线程状态是否中断，如果中断，则使用return，即可停止线程。

##### 暂停线程：

暂停线程意味着此线程还可以恢复运行。Java中可以使用suspend()方法暂停线程，使用resume()方法恢复线程的执行。

suspend()方法和resume()方法的缺点：
1. suspend()方法和resume()方法如果使用不当，极易造成公共的同步对象的独占，使得其他线程无法访问公共同步对象；
2. suspend()方法和resume()方法也容易出现因为线程的暂停而导致数据不同步的情况。

##### yiled()方法：(会释放CPU资源，但不会释放锁)

yiled()方法的作用是放弃当前的CPU资源，将它让给其他的任务去占用CPU执行时间。但放弃的时间不确定，有可能刚刚放弃，也有可能马上重新获得CPU时间片了。

##### 线程的优先级：

在操作系统中线程可以划分优先级，优先级较高的线程得到的CPU资源较多，也就是CPU优先执行优先级较高的线程对象中的任务。

设置线程优先级的方法是setPriority()，Java中线程优先级分为1~10这10个等级。

线程优先级的继承特性：在Java中，A线程启动B线程，则B线程的优先级与A线程是一样的。

优先级具有规则性：CPU尽量将执行资源让给优先级比较高的线程，但不是绝对的。

优先级具有随机性：优先级较高的线程不一定每一次都先执行完。

##### 守护线程：

Java中有两种线程，一种是用户线程，一种是守护线程。

守护线程是一种特殊的线程，它的特性有“陪伴”的含义，当进程中不存在非守护线程了，则守护线程自动销毁。

Daemon的作用是为其他线程的运行提供便利服务，守护线程最典型的应用就是GC(垃圾收集器)，它是一个称职的守护者。
