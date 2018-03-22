## cpu使用率统计中nice的理解
The nice value for the processor is

>the time the CPU has spent running users' processes that have been "niced".

(from man top) A "niced" process is one with a positive nice value. So if the processor's nice value is high, that means it is working with some low priority processes. So this indicator is useful when you see high CPU utilization and you are afraid that this high load will have bad effect on your system:

* High CPU utilization with high nice value: Nothing to worry, not so important tasks doing their job, important processes will easily get CPU time if they need. This situation is not a real bottleneck.
* High CPU utilization with low nice value: Something to worry because the CPU is stressed with important processes so these or new processes will have to wait. This situation is a real bottleneck.

nice值是cpu任务调度优先级的修正值，范围是(-19,20)．
任务的优先级的值越小优先级越高．
_____
对nice值一个形象比喻，假设在一个CPU轮转中，有2个runnable的进程A和B，如果他们的nice值都为0，假设内核会给他们每人分配1k个cpu时间片。但是假设进程A的为0，但是B的值为-10，那么此时CPU可能分别给A和B分配1k和1.5k的时间片。故可以形象的理解为，nice的值影响了内核分配给进程的cpu时间片的多少，时间片越多的进程，其优先级越高，其优先级值（PRI）越低