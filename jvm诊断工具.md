# Arthas
####上传工具：
  
```shell 
scp arthas.tar.gz xuefeng.sha@l-rebuildintlb2.h.cn2:/tmp
```

####解压：  

```shell
tar zxvf arthas.tar.gz
```

####运行：  

```shell
sudo -u tomcat -EH java -jar arthas-boot.jar
```
####关闭：

```
shutdown
```

####实战：

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
# Greys