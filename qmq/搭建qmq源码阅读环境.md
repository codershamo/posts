# 搭建NewQmq源码阅读环境

## 配置

1. 在**qmq-metaserver**和**qmq-server**模块的resources文件夹下新建**qunar-env.properties**文件,内容：

    ```shell
    name=beta
    ```
2. 在qmq-metaserver的resources下新建qconfig_test文件夹，再在里面新建tc_newqmq_meta_server文件夹，在里面新建brokers.properties文件，内容：

   ```shell
   sha/20881=testsha,master
   ```

   sha/20881分别是qmq-server的hostname和端口号，testsha是broker名称，可以随便指定，master是broker角色．

3. 在qmq-server的resources下新建qconfig_test文件夹，在下面再新建tc_newqmq_server文件夹，在里面新建config.properties文件，内容：

   ```shell
   store.root=/home/q/newqmq/master
   meta.server.endpoint=http://localhost:8080/meta/address
   wait.slave.wrote=false
   broker.port=20881
   sync.port=20882
   receiver.flusher.enable=false
   sync.batch.size=1000000
   message.sync.timeout.ms=2
   messagelog.retention.hours=24
   consumerlog.retention.hours=24
   pulllog.retention.hours=24
   ```

    注意meta.server.endpoint是metaserver服务地址

4. 为了方便debug, 修改qunar.tc.qmq.register.BrokerRegisterService中的超时时间，将

   ```java
   private static final long TIMEOUT_MS = TimeUnit.SECONDS.toMillis(5);
   ```

   改为

   ```java
   private static final long TIMEOUT_MS = TimeUnit.HOURS.toMillis(5);
   ```
