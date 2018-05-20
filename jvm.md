# JVM相关总结

## 查看jvm默认及运行时参数值

* 查看默认参数值

    ```shell
    java -XX:+PrintFlagsFinal -version
    ```
* 查看单个运行时参数
    ```shell
    jinfo -flag <option> <pid>
    ```
    例：
    ```shell
    ~ ➭ jinfo -flag UseParallelOldGC 14473
    -XX:-UseParallelOldGC
    ```

* 查看非默认值参数
    ```shell
    jinfo -flags <pid>
    ```
    例：
    ```shell
    ~ ➭ jinfo -flags 14473
    Attaching to process ID 14473, please wait...
    Debugger attached successfully.
    Server compiler detected.
    JVM version is 25.51-b03
    Non-default VM flags: -XX:CICompilerCount=3 -XX:ConcGCThreads=1 -XX:+DisableExplicitGC -XX:G1HeapRegionSize=2097152 -XX:InitialHeapSize=4294967296 -XX:MarkStackSize=4194304 -XX:MaxGCPauseMillis=200 -XX:MaxHeapSize=4294967296 -XX:MaxNewSize=2575302656 -XX:MinHeapDeltaBytes=2097152 -XX:+PrintGC -XX:+PrintGCDateStamps -XX:+PrintGCDetails -XX:+PrintGCTimeStamps -XX:+TieredCompilation -XX:+UseCompressedClassPointers -XX:+UseCompressedOops -XX:+UseG1GC 
    Command line:  -Djava.util.logging.config.file=/...
    ```

## Thread Dump的三种方式

1. unix命令行方式

    ```shell
    kill -3 <pid>
    ```
    使用这种方式打印的栈信息是输出到运行java程序的控制台上，对于tomcat，则会输出到catalina.out文件中，不推荐使用。

2. jstack

    ```java
    jstack <pid> >> mydumps.tdump
    ```
3. jcmd

    ```java
    jcmd <pid> Thread.print
    ```
    java8开始支持

## Java Thread知识点

* JVM的线程与操作系统的线程一一对应，nid即native thread id为os的线程id的十六进制值。

* sleeping和waiting状态的线程不消耗CPU, 处于Runnable状态的线程也不一定真正地消耗CPU，只能说明该线程没有阻塞在java的wait或sleep上，同时也没等待在锁上面。如果该线程调用了本地方法，而本地方法处于等待状态（如socket），此时jvm是不知道本地代码中发生了什么，尽管当前线程实际上也是阻塞状态，但显示出来的还是runnable状态，这种情况下是不消耗CPU的。

* wait释放占有的锁，sleep不释放。wait()后面的代码需要重新获得锁才能继续执行。

* 1. 当一个线程占有一个锁时，线程堆栈中打印-locked <0x22bffb60>
  2. 当一个线程正在等待其他线程释放该锁，打印-waiting to lock <0x22bffb50>
  3. 当一个线程占有一个锁时，但又执行到该锁的wait()上，堆栈中首先打印locked，然后又会打印-waiting on <0x22c03c60>

* 死锁并不消耗CPU，不会导致100%CPU使用率。无限循环，并且循环中的代码都是CPU密集型的，才会导致CPU的100%使用率。

## JVM内存分析

1. 打印堆快照
    ```java
    jmap -histo <pid> 或
    jmap -histo:live <pid>
    ```
    带live参数在打印快照前会进行一次full gc，所以打印的是系统中存活的对象。

## 其他问题

1. 使用Java监控工具出现Can't attach to the process的解决方法：
    ```shell
    echo 0 | sudo tee /proc/sys/kernel/yama/ptrace_scope
    ```
    **原因:**

    新版的Linux系统加入了 ptrace-scope 机制. 这种机制为了防止用户访问当前正在运行的进程的内存和状态, 而一些调试软件本身就是利用 ptrace 来进行获取某进程的内存状态的(包括GDB),所以在新版本的Linux系统, 默认情况下不允许再访问了. 可以使用上述命令临时开启.    
    永久写到文件来持久化:
    ```shell
    vim /etc/sysctl.d/10-ptrace.conf
    
    添加或修改为以下这一句:(0:允许, 1:不允许)
    kernel.yama.ptrace_scope = 0
    ```
## Metaspace in Java 8



