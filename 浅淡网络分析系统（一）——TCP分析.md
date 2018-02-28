# 浅淡网络分析系列（一）—— TCP协议分析
## TCP协议介绍
TCP(Transmission Control Protocol)具体以下几方面特性（大概看下，后面会结合具体例子进行详细解释）
* Connection-oriented setup and tear-down of TCP sessions （面向连接）
* The service sends and receives a stream of bytes, not messages, and guarantees that
all bytes received will be identical with bytes sent and in the correct order （可靠地字节传输）
* Reliable, in-order delivery, uses **sequence number** to recover from data that is
damaged, lost, duplicated, or delivered out of order by the Internet communication
system（使用sequence number保证传输数据顺序的正确性）
* **Flow control** prevents the receiver’s buffer space from overflowing（流量控制）
* Congestion control (as defined in RFC 5681) algorithms are: **slow start, congestion
avoidance, fast retransmit, and fast recovery**（冲突控制）
* Multiplexing; every TCP conversation has two logical pipes; an outgoing and
incoming pipe（全双工）

### TCP头定义
