## 查看jvm默认及运行时参数值
* 查看默认参数值
    ```
    java -XX:+PrintFlagsFinal -version
    ```
* 查看单个运行时参数
    ```
    jinfo -flag <option> <pid>
    ```
    例：
    ```
    ~ ➭ jinfo -flag UseParallelOldGC 14473
    -XX:-UseParallelOldGC
    ```

* 查看非默认值参数
    ```
    jinfo -flags <pid>
    ```
    例：
    ```
    ~ ➭ jinfo -flags 14473
    Attaching to process ID 14473, please wait...
    Debugger attached successfully.
    Server compiler detected.
    JVM version is 25.51-b03
    Non-default VM flags: -XX:CICompilerCount=3 -XX:ConcGCThreads=1 -XX:+DisableExplicitGC -XX:G1HeapRegionSize=2097152 -XX:InitialHeapSize=4294967296 -XX:MarkStackSize=4194304 -XX:MaxGCPauseMillis=200 -XX:MaxHeapSize=4294967296 -XX:MaxNewSize=2575302656 -XX:MinHeapDeltaBytes=2097152 -XX:+PrintGC -XX:+PrintGCDateStamps -XX:+PrintGCDetails -XX:+PrintGCTimeStamps -XX:+TieredCompilation -XX:+UseCompressedClassPointers -XX:+UseCompressedOops -XX:+UseG1GC 
    Command line:  -Djava.util.logging.config.file=/...
    ```

## Metaspace in Java 8



