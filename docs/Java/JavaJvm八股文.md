![](https://resource.lzyan.fun/PigGo/lsawleRSzP5PAc5UBvY3sAQKuCur.png)

## JVM 内存管理

### Java 运行时数据区域

`JVM` 在执行 `Java` 程序的过程中会把它所管理的内存划分为若干个不同的数据区域。这些区域都有各自的用途，以及创建和销毁的时间，有的区域随着虚拟机进程的启动而存在，有些区域则依赖用户线程的启动和结束而建立和销毁。

`JDK8` 之后的 `JVM` 运行数据区域：

![](https://resource.lzyan.fun/PigGo/20220223201513.png)

## JVM 垃圾回收
## JVM 类加载过程