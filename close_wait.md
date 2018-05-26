# 记一次CLOSE_WAIT状态故障的排查过程
## 问题描述
5月19起线上应用的机器CLOSE_WAIT状态在不断增长，且在增长一段时间后又会降到很低．监控如图：
![close_wait](pictures/close_wait/选区_001.jpg)

登上机器，查看引起CLOSE_WAIT的连接的IP，发现基本都是10.88.159.10这个IP，host这个IP，发现是ng的IP地址

![netstat](pictures/close_wait/选区_002.jpg)

## CLOSE_WAIT是个啥
