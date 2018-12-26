
## 开启方式
### 1. 采集正在运行的应用的JFR

* jcmd 查看正在运行的java应用的pid
    ```
    {19:46}~ ➭ jcmd
    2720 org.jetbrains.idea.maven.server.RemoteMavenServer
    31824 sun.tools.jcmd.JCmd
    2404 com.intellij.idea.Main
    31547 org.apache.catalina.startup.Bootstrap start

    ```
* jcmd help查看是否支持JFR

    ```
    {19:50}~ ➭ jcmd 31547 help
    31547:
    The following commands are available:
    JFR.stop
    JFR.start
    JFR.dump
    JFR.check
    ...
    ```
    sudo -u tomcat jcmd 22213 VM.unlock_commercial_features
    
* **Start**
    ```
    {19:51}~ ➭ jcmd 31547 JFR.start name=MyRecording settings=default
    31547:
    Started recording 3. No limit (duration/maxsize/maxage) in use.

    Use JFR.dump name=MyRecording filename=FILEPATH to copy recording data to file.
    ```
    >**注意** settings的取值可以是一个采样template的路径, 或者是/jre/lib/jfr文件夹下的template名称
* **Check**
    ```
    {19:53}~ ➭ jcmd 31547 JFR.check                                  
    31547:
    Recording: recording=2 name="MyRecording" (running)
    ```
* **Dump**
    ```
    {20:00}~ ➭ jcmd 31547 JFR.dump name=MyRecording filename=/tmp/r.jfr
    31547:
    Dumped recording "MyRecording", 1.7 MB written to:

    /tmp/r.jfr
    ```
* **Stop**
    ```
    {20:01}~ ➭ jcmd 31547 JFR.stop name=MyRecording
    31547:
    Stopped recording "MyRecording".
    ```
### 2. 命令行模式

如果要采集应用启动时的事件数据时，命令行模式能够满足．下面是一个例子：
```
-XX:+UnlockCommercialFeatures -XX:+FlightRecorder -XX:StartFlightRecording=delay=20s,duration=60s,name=MyRecording,filename=C:\demo\myrecording.jfr,settings=profile
```
表示的含义为在JVM启动后20秒开始采集，持续60s，使用的template为profile.

下面是一个使用默认template，持续采集的例子：
```
-XX:+UnlockCommercialFeatures -XX:+FlightRecorder -XX:StartFlightRecording=name=MyRecording,settings=default
```
这里没有指定文件名称，采集的数据可以使用jcmd进行dump，或者使用dumponexit参数来使JVM退出时dump数据．
```
-XX:+UnlockCommercialFeatures -XX:+FlightRecorder -XX:FlightRecorderOptions=defaultrecording=true,dumponexit=true,dumponexitpath=c:\demo\dumponexit.jfr
```