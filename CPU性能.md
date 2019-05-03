## CPU性能
### 基本概念
1. *平均负载(average load)*：单位时间内，系统处于**可运行状态**和**不可中断状态**的平均进程数  
	a. 可运行状态：正在使用或等待CPU的进程，ps命令处于R状态的进程。  
	b. 不可中断状态：正处于内核态关键流程中的进程，如常见的等待硬件设备的I/O响应，也就是ps命令中的D状态进程。 
	
2. *平均负载与CPU使用率*：平均负载不仅包括正在使用cpu进程，还包括等待CPU和等待I/O进程。CPU密集型进程，两都一致；I/O密集开进程，等待I/O也会导致平均负载升高，但CPU使用率不一定高；大量等待CPU进程调度也会导致平均负载升高，此时CPU使用率也较高。

3. 常用CPU指标：
	* user(us): 用户态CPU时间，不包括nice时间，包括guest时间
	* nice(ni): 低优先级用户态CPU时间，也就是进程nice值被调整为1-19之间时的CPU时间。注意，nice可取值范围是-20到19，数值越大，优先级反而越低。
	* system(sys): 内核态CPU时间
	* idle(id): 空闲时间，不包括等待I/O时间
	* iowait(wa): 等待I/O的CPU时间
	* irq(hi): 处理硬中断的CPU时间
	* softirq(si): 处理软中断的CPU时间
	* steal(st): 当系统运行在虚拟机中的时候，被其他虚拟机占用的CPU时间。
	* guest(guest): 通过虚拟化运行其他操作系统的时间，也就是运行虚拟机的CPU时间
	* guest_nice(gnice): 以低优先级运行虚拟机的时间。

4. 进程状态：
	* *R* 是Running或Runnable的缩写，表示进程在CPU的就绪队列中，正在运行或正在等待运行
	* *D* 是Disk Sleep的缩写，也就是不可中断状态睡眠(Uninterruptible Sleep)，一般表示进程正在跟硬件交互，并且交互过程不允许被其他进程或中断打断。
	* *Z* 是Zombie的缩写，表示僵尸进程，也就是进程实际上已经结束了，但父进程还没有回收它的资源（比如进程的描述符、PID等）。
	* *S* 是Interruptible Sleep的缩写，也就是可中断状态睡眠，表示进程因为等待某个事件而系统挂起。当等待的事件发生时，它会被唤醒并进入R状态。
	* *I* 是Idle的缩写，也就是空闲状态，用在不可中断睡眠的内核线程上。硬件交互导致的不可中断进程用D表示，但对某些内核线程来说，它们有可能实际上并没有任何负载，用Idle正是为了区分这种情况。D状态的进程会导致平均负载升高，I状态不会。
	* *T* 进程处于暂停或跟踪状态。
	* *X* 是Dead的缩写，表示进程已经消亡，不会在top或ps命令中出现。

5. 中断：中断是系统用来响应硬件设备请求的一种机制，它会打断进程的正常调度和执行，然后调用内核中的中断处理程序来响应设备的请求。
	* 硬中断：中断的上半部，直接处理硬件请求，特点是快速执行
	* 软中断：由内核触发，特点是延迟执行。

### 命令
1. 查看cpu个数: lscpu,nproc
2. 查看各个CPU使用率: mpstat

	```shell
	# -P ALL 表示监控所有 CPU，后面数字 5 表示间隔 5 秒后输出一组数据
	$ mpstat -P ALL 5
	Linux 4.15.0 (ubuntu) 09/22/18 _x86_64_ (2 CPU)
	13:30:06     CPU    %usr   %nice    %sys %iowait    %irq   %soft  %steal  %guest  %gnice   %idle
	13:30:11     all   50.05    0.00    0.00    0.00    0.00    0.00    0.00    0.00    0.00   49.95
	13:30:11       0    0.00    0.00    0.00    0.00    0.00    0.00    0.00    0.00    0.00  100.00
	13:30:11       1  100.00    0.00    0.00    0.00    0.00    0.00    0.00    0.00    0.00    0.00

	```  

3. 查看占用CPU进程：pidstat
	
	```shell
	# 间隔 5 秒后输出一组数据, -u 表示 CPU 指标
	$ pidstat -u 5 1
	13:37:07      UID       PID    %usr %system  %guest   %wait    %CPU   CPU  Command
	13:37:12        0      2962  100.00    0.00    0.00    0.00  100.00     1  stress

	```
	
	```shell
	# -d 展示 I/O 统计数据，-p 指定进程号，间隔 1 秒输出 3 组数据
	$ pidstat -d -p 4344 1 3
	06:38:50      UID       PID   kB_rd/s   kB_wr/s kB_ccwr/s iodelay  Command
	06:38:51        0      4344      0.00      0.00      0.00       0  app
	06:38:52        0      4344      0.00      0.00      0.00       0  app
	06:38:53        0      4344      0.00      0.00      0.00       0  app

	```

4. 查看中断和上下文切换: vmstat、pidstat  
	vmstat查看系统总体情况：

	```shell
	# 每隔 5 秒输出 1 组数据
	$ vmstat 5
	procs -----------memory---------- ---swap-- -----io---- -system-- ------cpu-----
 	r  b   swpd   free   buff  cache   si   so    bi    bo   in   cs us sy id wa st
 	0  0      0 7005360  91564 818900    0    0     0     0   25   33  0  0 100  0  0
 
	```

	* cs(context switch)：每秒上下文切换次数
	* in(interrupt)：每秒中断次数
	* r(Running or Runnable)：就绪队列长度，也就是正在运行和等待CPU的进程数
	* b(Blocked)：处于不可中断睡眠状态的进程数。
	
	pidstat查看具体进程:  
	
	```shell
	# 每隔 1 秒输出一组数据（需要 Ctrl+C 才结束）
	# -wt 参数表示输出线程的上下文切换指标
	$ pidstat -wt 1
	08:14:05      UID      TGID       TID   cswch/s nvcswch/s  Command
	...
	08:14:05        0     10551         -      6.00      0.00  sysbench
	08:14:05        0         -     10551      6.00      0.00  |__sysbench
	08:14:05        0         -     10552  18911.00 103740.00  |__sysbench
	08:14:05        0         -     10553  18915.00 100955.00  |__sysbench
	08:14:05        0         -     10554  18827.00 103954.00  |__sysbench
	...

	```
	
	* cswch(voluntary context switches): 自愿上下文切换
	* nvcswch(non voluntary context switches): 非自愿上下文切换
	
5. 查看中断：

	系统中断可以从 /proc/interrupts 这个只读文件中读取。/proc实际上是Linux的一个虚拟文件系统，用于内核空间与用户空间之间的通信。

	```shell
	# -d 参数表示高亮显示变化的区域
	$ watch -d cat /proc/interrupts
           CPU0       CPU1
	...
	RES:    2450431    5279697   Rescheduling interrupts
	...

	```
	
	* 自愿上下文切换变多了，说明进程在等待资源，有可能发生了I/O等其他问题
	* 非自愿上下文切换变多了，说明进程都在被强制调度，也就是都在争抢CPU，说明CPU的确成了瓶颈
	* 中断次数变多了，说明CPU被中断处理程序占用，还需要通过查看/proc/interrupts文件来分析具体的中断类型。

6. 查看使用CPU线程：perf top

	```shell
	# -g 开启调用关系分析，-p 指定 php-fpm 的进程号 21515
	$ perf top -g -p 21515

	```
	
	```
	$ perf record -g
	$ perf report

	```

7. 查看进程间关系: pstree

	```shell
	# -a 表示输出命令行选项
	# p 表 PID
	# s 表示指定进程的父进程
	$ pstree -aps 3084
	systemd,1
	  └─dockerd,15006 -H fd://
	      └─docker-containe,15024 --config /var/run/docker/containerd/containerd.toml
	          └─docker-containe,3991 -namespace moby -workdir...
	              └─app,4009
	                  └─(app,3084)

	```
	
8. CPU使用率高原因不明，要查看是不是短时应用造成。使用工具perf record和execsnoop //todo
9. 当 iowait 升高时，进程很可能因为得不到硬件的响应，而长时间处于不可中断状态
10. 跟踪进程系统调用工具: strace
	
	```shell
	strace -p 6082
	strace: attach: ptrace(PTRACE_SEIZE, 6082): Operation not permitted

	```
	> root用户查看进程信息无权限，用ps命令查看进程状态，很有可能是进程为僵尸进程
11. 查看中断：  
	
	```shell
	#软中断
	cat /proc/softirqs
                    CPU0       CPU1
          HI:          0          0
       TIMER:     811613    1972736
      NET_TX:         49          7
      NET_RX:    1136736    1506885
       BLOCK:          0          0
    IRQ_POLL:          0          0
     TASKLET:     304787       3691
       SCHED:     689718    1897539
     HRTIMER:          0          0
         RCU:    1330771    1354737

	```
	
	>软中断以内核的方式运行，每个CPU都对应一个软中断内核线程，这个软中断内核线程就叫做 ksoftirqd/CPU 编号。
	
	```shell
	$ ps aux | grep softirq
	root         7  0.0  0.0      0     0 ?        S    Oct10   0:01 [ksoftirqd/0]
	root        16  0.0  0.0      0     0 ?        S    Oct10   0:01 [ksoftirqd/1]

	```
	
###总结
![根据性能指标查工具](pictures/cpu/tools.png)
![根据工具查指标](pictures/cpu/tools2.png)
![工具关联](pictures/cpu/tools3.png)