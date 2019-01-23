# Arthas
###上传工具：
  
```shell 
scp arthas.tar.gz xuefeng.sha@l-rebuildintlb2.h.cn2:/tmp
```

###解压：  

```shell
tar zxvf arthas.tar.gz
```

###运行：  

```shell
sudo -u tomcat -EH java -jar arthas-boot.jar
```
###关闭：

```
shutdown
```

###实战：

1 查看dashboard

```
$ dashboard
```

2 查看占用cpu最高的3个线程

```
$ thread -n 3
```

3 反编译类

```
$ jad com.qunar.hotel.inter.v2.price.processor.SelectCommissionRatioProcessor
```

4 查看函数参数返回值等

```
watch com.qunar.hotel.inter.price.base.CityReference getCity "{params,returnObj}" -x 2 -n 2 "params[0]=='i-hong_kong'"
```
-x表示遍历深度，-n表示执行次数

具体查看：
> https://alibaba.github.io/arthas/watch.html

5 查看函数执行耗时

```
trace com.qunar.hotel.inter.price.service.executor.AbstractDetailExecutor executeProcessor '#cost>10'
```

# VJTools

###vjtop

上传：

```
scp vjtop.tar.gz xuefeng.sha@l-rebuildcore1.h.cn2:/tmp
```

运行：

```
sudo -u tomcat -EH ./vjtop.sh $PID
```

###vjdump
一键收集jstack、jmap以及GC日志等相关信息，并以zip包保存，默认在目录 **/tmp/vjtools/vjdump** 下  

收集数据包括：

* thread dump数据：jstack -l $PID
* vjtop JVM概况及繁忙线程：vjtop.sh -n 1 $PID (需要将vjtop.sh 加入用户的PATH变量中)
* jmap histo 堆对象统计数据：jmap -histo $PID & jmap -histo:live $PID
* GC日志(如果JVM有设定GC日志输出)
* heap dump数据（需指定--liveheap开启）：jmap -dump:live,format=b,file=${DUMP_FILE} $PID

上传：

```
scp vjdump.sh xuefeng.sha@l-rebuildcore1.h.cn2:/tmp
```

运行：

```
sudo -u tomcat -EH ./vjdump.sh --liveheap $pid
```

下载：

```
scp xuefeng.sha@l-rebuildcore1.h.cn2:/tmp/vjtools/vjdump-7719-20190122161646.zip .
```
