## 基础概述

### 并发和并行的区别？

并发和并行最关键的差异在于，是否同时发生：

- **并发**：是指具备处理多个任务的能力，但不一定要同时。

- **并行**：是指具备同时处理多个任务的能力。

[并发与并行的区别是什么？——知乎的高票答案 (opens new window)](https://www.zhihu.com/question/33515481/answer/58849148)：

- 你吃饭吃到一半，电话来了，你一直到吃完了以后才去接，这就说明你不支持并发也不支持并行。
- 你吃饭吃到一半，电话来了，你停了下来接了电话，接完后继续吃饭，这说明你支持并发。
- 你吃饭吃到一半，电话来了，你一边打电话一边吃饭，这说明你支持并行。

### 同步异步和阻塞非阻塞的区别？

同步和异步关注的是 `消息通信机制` (synchronous communication/asynchronous communication)。

- **同步**：所谓同步，就是在发出一个调用时，在没有得到结果之前，该调用就不返回。但是一旦调用返回，就得到返回值了。换句话说，就是由调用者主动等待这个调用的结果。

- **异步**：相反，调用在发出之后，这个调用就直接返回了，所以没有返回结果。换句话说，当一个异步过程调用发出后，调用者不会立刻得到结果。而是在调用发出后，被调用者通过状态、通知来通知调用者，或通过回调函数处理这个调用。

一个简单的例子：同步与异步就好比去银行柜台办理业务，最早的时候都要在窗口后面排队成一列，排到你了就上前办理业务，这就是同步。而现在就可以通过拿号的方式，你只要拿到排队号然后等业务员叫号通知你了就可以上前去办理，这期间就不用在窗口后面排队，可以去做别的事，这就是异步。

阻塞和非阻塞关注的是程序在 `等待调用结果`（消息，返回值）时的状态。

- **阻塞**：是指调用结果返回之前，当前线程会被挂起。调用线程只有在得到结果之后才会返回。

- **非阻塞**：是指在不能立刻得到结果之前，该调用不会阻塞当前线程。

### 什么是线程和进程？

- **进程**

进程是程序的一次执行过程，是系统运行程序的基本单位，因此进程是动态的。系统运行一个程序即是一个进程从创建，运行到消亡的过程。

在 `Java` 中，当启动 `main` 函数时其实就是启动了一个 `JVM` 的进程，而 `main` 函数所在的线程就是这个进程中的一个线程，也称主线程。

- **线程**

线程与进程相似，但线程是一个比进程更小的执行单位，线程是操作系统进行调度的基本单位。一个进程在其执行的过程中可以产生多个线程。与进程不同的是同类的多个线程共享进程的 `堆` 和 `方法区` 资源，但每个线程有自己的 `程序计数器`、`虚拟机栈` 和 `本地方法栈` ,所以系统产生一个线程，或是在各个线程之间切换工作时，负担要比进程小得多，也正是因为如此线程也被称为轻量级进程。

- **Java中的多线程**

`Java` 程序天生就是多线程程序，一个 `Java` 程序实际上是一个 `JVM` 进程，`JVM` 进程用一个主线程来执行 `main()` 方法，在 `main()` 方法内部，又可以启动多个线程。

通过 `JMX` 来看一下一个普通的 `Java` 程序有哪些线程。

```java
public class MultiThread {
	public static void main(String[] args) {
		// 获取 Java 线程管理 MXBean
	ThreadMXBean threadMXBean = ManagementFactory.getThreadMXBean();
		// 不需要获取同步的 monitor 和 synchronizer 信息，仅获取线程和线程堆栈信息
		ThreadInfo[] threadInfos = threadMXBean.dumpAllThreads(false, false);
		// 遍历线程信息，仅打印线程 ID 和线程名称信息
		for (ThreadInfo threadInfo : threadInfos) {
			System.out.println("[" + threadInfo.getThreadId() + "] " + threadInfo.getThreadName());
		}
	}
}
```
上述程序输出如下，输出内容可能不同，不用太纠结下面每个线程的作用，只用知道 `main` 线程执行 `main` 方法即可：一个 `Java` 程序的运行是 `main` 线程和多个其他线程同时运行
```java
[5] Attach Listener //添加事件
[4] Signal Dispatcher // 分发处理给 JVM 信号的线程
[3] Finalizer //调用对象 finalize 方法的线程
[2] Reference Handler //清除 reference 线程
[1] main //main 线程,程序入口
```

### 从 JVM 角度分析进程和线程的关系？

![](https://resource.lzyan.fun/PigGo/20220211131229.png)

上图Java内存区域中可以看出：一个进程中可以有多个线程，多个线程共享进程的 `堆`和 `方法区 (JDK1.8 之后的元空间)` 资源，但是每个线程有自己的 `程序计数器` 、`虚拟机栈` 和 `本地方法栈`。

**总结**： 线程是进程划分成的更小的运行单位。线程和进程最大的不同在于基本上各进程是独立的，而各线程则不一定，因为同一进程中的线程极有可能会相互影响。线程执行开销小，但不利于资源的管理和保护；而进程正相反。

- **程序计数器为什么私有**

程序计数器主要有下面两个作用：

1. 字节码解释器通过改变程序计数器来依次读取指令，从而实现代码的流程控制，如：顺序执行、选择、循环、异常处理。 

2. 在多线程的情况下，程序计数器用于记录当前线程执行的位置，从而当线程被切换回来的时候能够知道该线程上次运行到哪儿了。

需要注意的是，如果执行的是 `native` 方法，那么程序计数器记录的是 `undefined` 地址，只有执行的是 `Java` 代码时程序计数器记录的才是下一条指令的地址。所以，**程序计数器私有主要是为了线程切换后能恢复到正确的执行位置**。

- **虚拟机栈和本地方法栈为什么私有**

1. 虚拟机栈： 每个 `Java` 方法在执行的同时会创建一个栈帧用于存储局部变量表、操作数栈、常量池引用等信息。从方法调用直至执行完成的过程，就对应着一个栈帧在 `Java` 虚拟机栈中入栈和出栈的过程。

2. 本地方法栈： 和虚拟机栈所发挥的作用非常相似，区别是： 虚拟机栈为虚拟机执行 `Java` 方法 （也就是字节码）服务，而本地方法栈则为虚拟机使用到的 `Native` 方法服务。 在 `HotSpot` 虚拟机中和 Java 虚拟机栈合二为一。

所以，**为了保证线程中的局部变量不被别的线程访问到，虚拟机栈和本地方法栈是线程私有的。**

- **堆和方法区是什么**

堆和方法区是所有线程共享的资源，其中堆是进程中最大的一块内存，主要用于存放 `新创建的对象` (几乎所有对象都在这里分配内存)，方法区主要用于存放 `已被加载` 的 `类信息` 、 `常量` 、 `静态变量` 、`即时编译器编译后的代码`等数据。

### 为什么要使用多线程？

总体上来说：

- **从计算机底层**： 线程可以比作是轻量级的进程，是程序执行的最小单位,线程间的切换和调度的成本远远小于进程。另外，多核 CPU 时代意味着多个线程可以同时运行，这减少了线程上下文切换的开销。 

- **从当代互联网发展趋势**： 现在的系统动不动就要求百万级甚至千万级的并发量，而多线程并发编程正是开发高并发系统的基础，利用好多线程机制可以大大提高系统整体的并发能力以及性能。

深入到计算机硬件底层来探讨：

- **单核时代**： 在单核时代多线程主要是为了提高单进程利用 `CPU` 和 `IO` 系统的 `效率` 。 假设只运行了一个 `Java` 进程的情况，当我们请求 `IO` 的时候，如果 `Java` 进程中只有一个线程，此线程被 `IO` 阻塞则整个进程被阻塞。`CPU` 和 `IO` 设备只有一个在运行，那么可以简单地说系统整体效率只有 `50%`。当使用多线程的时候，一个线程被 `IO` 阻塞，其他线程还可以继续使用 `CPU`。从而提高了 Java 进程利用系统资源的整体效率。 

- **多核时代**： 多核时代多线程主要是为了提高进程利用 `多核 CPU` 的能力。举个例子：假如我们要计算一个复杂的任务，我们只用一个线程的话，不论系统有几个 `CPU` 核心，都只会有一个 `CPU` 核心被利用到。而创建多个线程，这些线程可以被映射到底层多个 `CPU` 上执行，在任务中的多个线程没有资源竞争的情况下，任务执行的效率会有显著性的提高，约等于 `单核时执行时间/CPU 核心数`

从平时工作中探讨：

系统 `Web` 服务器用的是 `Tomcat`，`Tomcat` 处理每一个请求都会从线程连接池里边用一个线程去处理。连接数据库会用对应的连接池 `Druid/C3P0/DBCP…` 等等这些都用了多线程。这些框架已经帮开发者屏蔽掉手写多线程的问题


### Java 中线程的生命周期和状态？

Java 线程在运行的生命周期中的指定时刻只可能处于下面 6 种不同状态的其中一个状态。

![](https://resource.lzyan.fun/PigGo/20220211162601.png)

线程在生命周期中并不是固定处于某一个状态而是随着代码的执行在不同状态之间切换。

![](https://resource.lzyan.fun/PigGo/20220211162811.png)

由上图可以看出：线程创建之后它将处于 `NEW（新建）` 状态，调用 `start()` 方法后开始运行，线程这时候处于 `READY（可运行）` 状态。可运行状态的线程获得了 `CPU 时间片（timeslice）` 后就处于 `RUNNING（运行）` 状态。

> 在操作系统层面线程有 READY 和 RUNNING 状态，而在 JVM 层面只能看到 RUNNABLE 状态，所以 Java 系统一般将这两个状态统称为 RUNNABLE（运行中） 状态 。

当线程执行 `wait()` 方法之后，线程进入 `WAITING（等待）` 状态。进入等待状态的线程需要依靠其他线程的通知才能够返回到运行状态，而 `TIMED_WAITING(超时等待)` 状态相当于在等待状态的基础上增加了超时限制，比如通过 `sleep（long millis）` 方法或 `wait（long millis）` 方法可以将 `Java` 线程置于 `TIMED_WAITING` 状态。当超时时间到达后 `Java` 线程将会返回到 `RUNNABLE` 状态。当线程调用同步方法时，在没有获取到锁的情况下，线程将会进入到 `BLOCKED（阻塞）` 状态。线程在执行 `Runnable` 的 `run()` 方法之后将会进入到 `TERMINATED（终止）` 状态。

### 什么是多线程的上下文切换？

线程在执行过程中会有自己的运行条件和状态（也称上下文），比如程序计数器，栈信息等。当出现如下情况的时候，线程会从占用 `CPU` 状态中退出。

- 主动让出 CPU，比如调用了 sleep(), wait() 等。 

- 时间片用完，因为操作系统要防止一个线程或者进程长时间占用CPU导致其他线程或者进程饿死。

- 调用了阻塞类型的系统中断，比如请求 IO，线程被阻塞。 

- 被终止或结束运行          

这其中前三种都会发生线程切换，线程切换意味着需要保存当前线程的上下文，留待线程下次占用 `CPU` 的时候恢复现场。并加载下一个将要占用 `CPU` 的线程上下文。这就是所谓的 `上下文切换`。

上下文切换是现代操作系统的基本功能，因其每次需要保存信息恢复信息，这将会占用 CPU，内存等系统资源进行处理，也就意味着效率会有一定损耗，如果频繁切换就会造成整体效率低下。

**减少上下文切换的方法**:

- 无锁并发编程 - 多线程竞争锁时，会引起上下文切换，所以多线程处理数据时，可以用一些办法来避免使用锁，如将数据的 ID 按照 Hash 算法取模分段，不同的线程处理不同段的数据。

- CAS 算法 - Java 的 Atomic 包使用 CAS 算法来更新数据，而不需要加锁。

- 使用最少线程 - 避免创建不需要的线程，比如任务很少，但是创建了很多线程来处理，这样会造成大量线程都处于等待状态。

- 使用协程 - 在单线程里实现多任务的调度，并在单线程里维持多个任务间的切换。

### 什么是线程死锁?如何避免死锁？

线程死锁描述的是这样一种情况：多个线程同时被阻塞，它们中的一个或者全部都在等待某个资源被释放。由于线程被无限期地阻塞，因此程序不可能正常终止。死锁发生后，没有任何机制能解除死锁，只能 `强制结束JVM进程` 。因此，在编写多线程应用时，要特别注意防止死锁。因为死锁一旦形成，就只能强制结束进程。

通过一个例子说明线程死锁

```java
public class DeadLockDemo {
    private static Object resource1 = new Object();//资源 1
    private static Object resource2 = new Object();//资源 2

    public static void main(String[] args) {
        new Thread(() -> {
            synchronized (resource1) {
                System.out.println(Thread.currentThread() + "get resource1");
                try {
                    Thread.sleep(1000);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
                System.out.println(Thread.currentThread() + "waiting get resource2");
                synchronized (resource2) {
                    System.out.println(Thread.currentThread() + "get resource2");
                }
            }
        }, "线程 1").start();

        new Thread(() -> {
            synchronized (resource2) {
                System.out.println(Thread.currentThread() + "get resource2");
                try {
                    Thread.sleep(1000);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
                System.out.println(Thread.currentThread() + "waiting get resource1");
                synchronized (resource1) {
                    System.out.println(Thread.currentThread() + "get resource1");
                }
            }
        }, "线程 2").start();
    }
}


//Output
Thread[线程 1,5,main]get resource1
Thread[线程 2,5,main]get resource2
Thread[线程 1,5,main]waiting get resource2
Thread[线程 2,5,main]waiting get resource1
```
`线程 A` 通过 `synchronized (resource1)` 获得 `resource1` 的监视器锁，然后通过 `Thread.sleep(1000)` ;让 `线程 A` 休眠 `1s` 为的是让 `线程 B` 得到执行然后获取到 `resource2` 的监视器锁。`线程 A` 和 `线程 B` 休眠结束了都开始企图请求获取对方的资源，然后这两个线程就会陷入互相等待的状态，这也就产生了 `死锁`。

**上面的例子符合产生死锁的四个必要条件:**

1. 互斥条件：该资源任意一个时刻只由一个线程占用。 

2. 请求与保持条件：一个进程因请求资源而阻塞时，对已获得的资源保持不放。 

3. 不剥夺条件: 线程已获得的资源在未使用完之前不能被其他线程强行剥夺，只有自己使用完毕后才释放资源。 

 4. 循环等待条件: 若干进程之间形成一种头尾相接的循环等待资源关系。

**要做到预防死锁，破坏死锁的产生的必要条件即可:**

1. 破坏请求与保持条件 ：一次性申请所有的资源。 

2. 破坏不剥夺条件 ：占用部分资源的线程进一步申请其他资源时，如果申请不到，可以主动释放它占有的资源。 

3. 破坏循环等待条件 ：靠按序申请资源来预防。按某一顺序申请资源，释放资源则反序释放。破坏循环等待条件。

**避免死锁的方式一般有以下方案：**

1. 固定加锁的顺序，比如可以使用Hash值的大小来确定加锁的先后。

2. 尽可能缩减加锁的范围，等到操作共享变量的时候才加锁。

3. 使用可释放的定时锁（一段时间申请不到锁的权限了，直接释放掉）

修改如上代码的线程获取锁的顺序一致即 `线程 1` 首先获得到 `resource1` 的监视器锁,这时候 `线程 2` 就获取不到了。然后 `线程 1` 再去获取 `resource2` 的监视器锁，可以获取到。然后 `线程 1` 释放了对 `resource1` 、`resource2` 的监视器锁的占用，`线程 2`  获取到就可以执行了。这样就破坏了破坏循环等待条件，因此避免了死锁。

```java
new Thread(() -> {
    synchronized (resource1) {
        System.out.println(Thread.currentThread() + "get resource1");
        try {
            Thread.sleep(1000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        System.out.println(Thread.currentThread() + "waiting get resource2");
        synchronized (resource2) {
            System.out.println(Thread.currentThread() + "get resource2");
        }
    }
}, "线程 2").start();

//Output
Thread[线程 1,5,main]get resource1
Thread[线程 1,5,main]waiting get resource2
Thread[线程 1,5,main]get resource2
Thread[线程 2,5,main]get resource1
Thread[线程 2,5,main]waiting get resource2
Thread[线程 2,5,main]get resource2

Process finished with exit code 0
```

### 什么是线程安全？

《Java并发编程实战》作者Brian Goetz给出的定义：

当多个线程同时访问一个对象时，如果不用考虑这些线程在运行时环境下的调度和交替执行，也不需要进行额外的同步，或者在调用方进行任何其他的协调操作，调用这个对象的行为都可以获得正确的结果，那就称这个对象是线程安全的。

### 创建线程的方式？

创建线程有三种方式：

1. 继承 `Thread` 类创建线程类

    - 定义 `Thread` 类的子类，并覆写该类的 `run` 方法。`run` 方法的方法体就代表了线程要完成的任务，因此把 `run` 方法称为执行体。
    - 创建 `Thread` 子类的实例，即创建了线程对象。
    - 调用线程对象的 `start` 方法来启动该线程。
    ```java
    public class ThreadDemo {

    public static void main(String[] args) {
        // 实例化对象
        MyThread tA = new MyThread("Thread 线程-A");
        MyThread tB = new MyThread("Thread 线程-B");
        // 调用线程主体
        tA.start();
        tB.start();
    }

    static class MyThread extends Thread {

        private int ticket = 5;

        MyThread(String name) {
            super(name);
        }

        @Override
        public void run() {
            while (ticket > 0) {
                System.out.println(Thread.currentThread().getName() + " 卖出了第 " + ticket + " 张票");
                ticket--;
            }
        }

    }
    }
    ```

2. 实现 `Runnable` 接口创建线程类

    实现 `Runnable` 接口优于继承 `Thread` 类，`Java` 不支持多重继承，所有的类都只允许继承一个父类，但可以实现多个接口。如果继承了 `Thread` 类就无法继承其它类，这不利于扩展。并且类可能只要求可执行就行，继承整个 `Thread` 类开销过大。

    - 定义 `Runnable` 接口的实现类，并覆写该接口的 `run` 方法。该 `run` 方法的方法体同样是该线程的线程执行体。
    - 创建 `Runnable` 实现类的实例，并以此实例作为 `Thread` 的 `target` 来创建 `Thread` 对象，该 `Thread` 对象才是真正的线程对象。
    - 调用线程对象的 `start` 方法来启动该线程。

    ```java
    public class RunnableDemo {

        public static void main(String[] args) {
            // 实例化对象
            Thread tA = new Thread(new MyThread(), "Runnable 线程-A");
            Thread tB = new Thread(new MyThread(), "Runnable 线程-B");
            // 调用线程主体
            tA.start();
            tB.start();
        }

        static class MyThread implements Runnable {

            private int ticket = 5;

            @Override
            public void run() {
                while (ticket > 0) {
                    System.out.println(Thread.currentThread().getName() + " 卖出了第 " + ticket + " 张票");
                    ticket--;
                }
            }

        }

    }
    ```

3. 通过 `Callable` 和 `Future` 接口 (把 `Callable` 变成 `Runnable`)

    继承 `Thread` 类和实现 `Runnable` 接口这两种创建线程的方式都没有返回值。所以，线程执行完后，无法得到执行结果。为了解决这个问题，`Java 1.5` 后，提供了 `Callable` 接口和 `Future` 接口，通过它们，可以在线程执行结束后，返回执行结果。

    `Callable` 接口只声明了一个方法 `call()`，接收一个泛型参数。

    ```java
    public interface Callable<V> {
        /**
        * Computes a result, or throws an exception if unable to do so.
        *
        * @return computed result
        * @throws Exception if unable to compute a result
        */
        V call() throws Exception;
    }
    ```
    `Future` 接口就是对于具体的 `Callable` 任务的执行结果进行取消、查询是否完成、获取结果。必要时可以通过 `get` 方法获取执行结果，该方法会阻塞直到任务返回结果。

    ```java
    public interface Future<V> {
    boolean cancel(boolean mayInterruptIfRunning);
    boolean isCancelled();
    boolean isDone();
    V get() throws InterruptedException, ExecutionException;
    V get(long timeout, TimeUnit unit)
        throws InterruptedException, ExecutionException, TimeoutException;
    }
    ```
    `FutureTask` 是 `Future` 接口的唯一实现类，其实质就是用实现了 `Runnable` 接口的 `FutureTask` 作为转换器(适配器)，在 `run()` 方法中调用 `Callable` 的 `call()` 方法，本质上仍然是实现了 `Runnable` 接口。

    - 创建 `Callable` 接口的实现类，并实现 `call` 方法。该 `call` 方法将作为线程执行体，并且有返回值。
    - 创建 `Callable` 实现类的实例，使用 `FutureTask` 类来包装 `Callable` 对象，该 `FutureTask` 对象封装了该 `Callable` 对象的 `call` 方法的返回值。
    - 使用 `FutureTask` 对象作为 `Thread` 对象的 `target` 创建并启动新线程。
    - 调用 `FutureTask` 对象的 `get` 方法来获得线程执行结束后的返回值。

    ```java
    public class CallableDemo {

        public static void main(String[] args) {
            Callable<Long> callable = new MyThread();
            FutureTask<Long> future = new FutureTask<>(callable);
            new Thread(future, "Callable 线程").start();
            try {
                System.out.println("任务耗时：" + (future.get() / 1000000) + "毫秒");
            } catch (InterruptedException | ExecutionException e) {
                e.printStackTrace();
            }
        }

        static class MyThread implements Callable<Long> {
            private int ticket = 10000;
            @Override
            public Long call() {
                long begin = System.nanoTime();
                while (ticket > 0) {
                    System.out.println(Thread.currentThread().getName() + " 卖出了第 " + ticket + " 张票");
                    ticket--;
                }
                long end = System.nanoTime();
                return (end - begin);
            }
        }

    }
    ```

### sleep() 方法和 wait() 方法的区别？

- 两者最主要的区别在于：`sleep()` 方法没有释放锁，而 `wait()` 方法释放了锁 。

- 两者都可以暂停线程的执行。

- `wait()` 是 `Object` 类的方法，通常被用于线程交互/通信，`sleep()` 通常被用户暂停执行。

- `wait()` 方法被调用后，线程不会自动苏醒，需要别的线程调用同一个对象上的 `notify()` 或者`notifyAll()` 方法。`sleep()` 方法执行完成后，线程会自动苏醒。或者可以使用 `wait(long timeout)` 超时后线程会自动苏醒。

### 为什么调用 start() 方法时会执行 run() 方法， 而不能直接调用 run() 方法？

这是另一个非常经典的 Java 多线程面试问题，而且在面试中会经常被问到。

`new` 一个 `Thread`，线程进入了新建状态。调用 `start()` 方法，会启动一个线程并使线程进入了 `就绪状态`，当分配到时间片后就可以开始运行了。 `start()` 会执行线程的相应准备工作，然后自动执行 `run()` 方法的内容，这是真正的多线程工作。 但是，直接执行 `run()` 方法，会把 `run()` 方法当成一个 `main` 线程下的 `普通方法` 去执行，并不会在某个线程中执行它，所以这并不是多线程工作。

**总结**： 调用 `start()` 方法方可启动线程并使线程进入就绪状态，直接执行 `run()` 方法的话不会以多线程的方式执行。

### 为什么 wait()、notify()、notifyAll() 不定义在 Thread 中？

`wait()`、`notify()`、`notifyAll()` 都是 `Object` 类中的方法。

- `wait()` - 自动释放当前线程占有的对象锁，并请求操作系统挂起当前线程，让线程从 `Running` 状态转入 `Waiting` 状态，等待 `notify / notifyAll` 来唤醒。如果没有释放锁，那么其它线程就无法进入对象的同步方法或者同步控制块中，那么就无法执行 `notify` 或者 `notifyAll` 来唤醒挂起的线程，造成死锁。

- `notify` - 唤醒一个正在 `Waiting` 状态的线程，并让它拿到对象锁，具体唤醒哪一个线程由 `JVM` 控制 。

- `notifyAll` - 唤醒所有正在 `Waiting` 状态的线程，接下来它们需要竞争对象锁。

每一个 `Java` 对象都有一个与之对应的 `监视器（monitor）`，每一个监视器里面都有一个 `对象锁` 、一个 `等待队列`、一个 `同步队列`。由于每个对象都拥有 `对象锁`，让当前线程等待某个 `对象锁`，自然应该基于这个 `对象（Object）` 来操作，而非使用 `当前线程（Thread）` 来操作。因为 `当前线程`可能会等待多个线程的锁，如果基于 `线程（Thread）` 来操作，就非常复杂了。

### 为什么 wait()、notify()、notifyAll() 要配合 synchronized 使用？

如果调用某个对象的 `wait()` 方法，当前线程必须 **拥有这个对象的对象锁**，因此调用 `wait()` 方法必须在 `synchronized` 方法和 `synchronized` 代码块中。

### sleep() 和 yield() 有什么区别？

`Thread` 类的 `sleep()` 和 `yield()` 方法将处理 `Running` 状态的线程。所以在其他处于 `非Running` 状态的线程上执行这两个方法是没有意义的。这也是为什么这些方法是静态的。

- `sleep()` 方法给其他线程运行机会时不考虑线程的优先级，因此会给低优先级的线程以运行的机会；`yield()` 方法只会给相同优先级或更高优先级的线程以运行的机会；

- 线程执行 `sleep()` 方法后转入 `阻塞（blocked）` 状态，而执行 `yield()` 方法后转入 `就绪（ready）` 状态；

- `sleep()` 方法声明抛出 `InterruptedException` ，而 `yield()` 方法没有声明任何异常；

- `sleep()` 方法比 `yield()` 方法（跟操作系统CPU调度相关）具有更好的可移植性。

### 了解 JUC 吗？

`Java` 的 `java.util.concurrent` 包（简称 JUC）中提供了大量并发工具类，是 `Java` 并发能力的主要体现（注意，不是全部，有部分并发能力的支持在其他包中）。从功能上，大致可以分为：

- 原子类 - 如：`AtomicInteger`、`AtomicIntegerArray`、`AtomicReference`、`AtomicStampedReference` 等。

- 锁 - 如：`ReentrantLock`、`ReentrantReadWriteLock` 等。

- 并发容器 - 如：`ConcurrentHashMap`、`CopyOnWriteArrayList`、`CopyOnWriteArraySet` 等。

- 阻塞队列 - 如：`ArrayBlockingQueue`、`LinkedBlockingQueue` 等。

- 非阻塞队列 - 如： `ConcurrentLinkedQueue` 、`LinkedTransferQueue` 等。

- Executor 框架（线程池）- 如：`ThreadPoolExecutor`、`Executors` 等。

`Java` 并发框架可以分为以下层次

![](https://resource.lzyan.fun/PigGo/20220219133909.png)

可以看出，`JUC` 包中的工具类是基于 `synchronized`、`volatile`、`CAS`、`ThreadLocal` 这样的并发核心机制打造的。所以，要想深入理解 `JUC` 工具类的特性、为什么具有这样那样的特性，就必须先理解这些核心机制。

## synchronized

###  说一说对 synchronized 关键字的了解？

**`synchronized` 关键字解决的是多个线程之间访问资源的同步性，`synchronized` 关键字可以保证被它修饰的方法或者代码块在任意时刻只能有一个线程执行。**

在 Java 早期版本中，`synchronized` 属于 `重量级锁`，效率低下。为什么？

因为 `监视器锁（monitor）` 是依赖于底层的操作系统的 `Mutex Lock` 来实现的，**Java 的线程是映射到操作系统的原生线程之上的**。如果要挂起或者唤醒一个线程，都需要操作系统帮忙完成，而操作系统实现线程之间的切换时需要从用户态转换到内核态，这个状态之间的转换需要相对比较长的时间，时间成本相对较高。

在 `Java 6` 之后 `Java` 官方对从 `JVM` 层面对 `synchronized` 较大优化，所以现在的 `synchronized` 锁效率也优化得很不错了。`JDK1.6` 对锁的实现引入了大量的优化，如`自旋锁`、`适应性自旋锁`、`锁消除`、`锁粗化`、`偏向锁`、`轻量级锁` 等技术来减少锁操作的开销。

所以不论是各种 `开源框架` 还是 `JDK源码` 都大量使用了 `synchronized` 关键字。

### synchronized 的用法有哪些？

1. **修饰实例方法**: 作用于当前对象实例加锁，进入同步代码前要获得 `当前对象实例的锁`

2. **修饰静态方法**: 也就是给当前类加锁，会作用于 `类的所有对象实例` ，进入同步代码前要获得 `当前 class` 的锁。因为静态成员不属于任何一个实例对象，是类成员（ `static` 表明这是该类的一个静态资源，不管 `new` 了多少个对象，只有一份）。所以，如果一个线程 `A` 调用一个实例对象的非静态 `synchronized` 方法，而线程 `B` 需要调用这个实例对象所属类的静态 `synchronized` 方法是允许的，不会发生互斥现象，因为访问静态 `synchronized` 方法占用的锁是 `当前类的锁` ，而访问非静态 `synchronized` 方法占用的锁是 `当前实例对象锁` 。

3. **修饰代码块**：指定加锁对象，对给定对象/类加锁。`synchronized(this|object)` 表示进入同步代码库前要获得给定对象的锁。`synchronized(类.class)` 表示进入同步代码前要获得 `当前 class` 的锁

**总结**：

- `synchronized` 关键字加到 `static` 静态方法和 `synchronized(class)` 代码块上都是是给 `Class` 类上锁。 

- `synchronized` 关键字加到实例方法上是给对象实例上锁。 

- 尽量不要使用 `synchronized(String a)` 因为 JVM 中，字符串常量池具有缓存功能！


### synchronized 的底层原理？

`synchronized` 关键字底层原理属于 `JVM` 层面。通过字节码看


```java
  public synchronized void test1(){
  }

  public void test2(){
    synchronized(new Test()){
    }
  }

  //上面代码所编译的字节码
  public synchronized void test1();
    descriptor: ()V
    flags: ACC_PUBLIC, ACC_SYNCHRONIZED    // here

  public void test2();
    descriptor: ()V
    flags: ACC_PUBLIC
    Code:
      stack=2, locals=3, args_size=1
         0: new           #2                  // class com/easy/helloworld/Test
         3: dup
         4: invokespecial #3                  // Method "<init>":()V
         7: dup
         8: astore_1
         9: monitorenter                   // here
        10: aload_1
        11: monitorexit                    // here
        12: goto          20
        15: astore_2
        16: aload_1
        17: monitorexit                    // here
        18: aload_2
        19: athrow
        20: return

```


 `synchronized` 同步语句块的实现使用的是 `monitorenter` 和 `monitorexit` 这两个 `JVM` 指令，主要是基于 `Mark Word` 和 `Object monitor` 来实现的。其中 `monitorenter` 指令指向同步代码块的开始位置，`monitorexit` 指令则指明同步代码块的结束位置。当执行 `monitorenter` 指令时，线程试图获取锁也就是获取 对象监视器 `monitor` 的持有权。

>在 Java 虚拟机(HotSpot)中，Monitor 是基于 C++实现的，由ObjectMonitor  (opens new window)实现的。每个对象中都内置了一个 ObjectMonitor对象。当多个线程同时访问一段同步代码时，多个线程会先被存放在 EntryList 集合中，处于 block 状态的线程，都会被加入到该列表。接下来当线程获取到对象的 Monitor 时，Monitor 是依靠底层操作系统的 Mutex Lock 来实现互斥的，线程申请 Mutex 成功，则持有该 Mutex，其它线程将无法获取到该 Mutex。

>在内存中，对象一般由三部分组成，分别是对象头、对象实际数据和对齐填充。重点在于对象头Mark Word，对象头又由几部分组成，但重点关注对象头Mark Word的信息就好了。Mark Word会记录对象关于锁的信息

`synchronized` 修饰的方法并没有 `monitorenter` 指令和 `monitorexit` 指令，取得代之的是会设置一个 `ACC_SYNCHRONIZED` 标志。当方法调用时，调用指令将会检查该方法是否被设置 `ACC_SYNCHRONIZED` 访问标志。如果设置了该标志，如果设置了，执行线程将先获取 `monitor`，然后再执行方法。在该方法运行期间，其它线程将无法获取到该 `Mointor` 对象，当方法执行完成后，再释放该 `Monitor` 对象。

**对于 `synchronized` 同步语句块和 `synchronized` 修饰的方法。两者的本质都是对对象监视器 `monitor` 的获取。** 获取过程如下：

1. 如果 `monitor` 的进入数为 `0`，则该线程进入 `monitor`，然后将进入数设置为 `1`，该线程即为 `monitor` 的所有者。
2. 如果线程已经占有该 `monitor` ，只是重新进入，则进入 `monitor` 的进入数加 `1`。
3. 如果其他线程已经占用了 `monitor` ，则该线程进入阻塞状态，直到 `monitor` 的进入数为 `0`，再重新尝试获取 `monitor` 的所有权。

这里涉及到一个Java的线程锁：`可重入锁`

什么是可重入的锁？例子：

```java
public class Counter {
    private int count = 0;

    public synchronized void add(int n) {
        if (n < 0) {
            dec(-n);
        } else {
            count += n;
        }
    }

    public synchronized void dec(int n) {
        count += n;
    }
}
```
观察 `synchronized` 修饰的 `add()` 方法，一旦线程执行到 `add()` 方法内部，说明它已经获取了当前实例的 `this` 锁。如果传入的 `n < 0` ，将在 `add()` 方法内部调用 `dec()` 方法。由于 `dec()` 方法也需要获取 `this` 锁，现在问题来了：对同一个线程，能否在获取到锁以后继续获取同一个锁？

答案是肯定的。`JVM` 允许同一个线程重复获取同一个锁，这种能被同一个线程反复获取的锁，就叫做 `可重入锁` 。由于 `Java` 的线程锁是 `可重入锁` ，所以获取锁的时候，不但要判断是否是第一次获取，还要记录这是第几次获取。每获取一次锁，记录 `+1` ，每退出 `synchronized` 块，记录 `-1` ，减到 `0` 的时候，才会真正释放锁。

### JDK1.6 之后的 synchronized 关键字底层做了哪些优化？

在 `JDK 1.6` 之前,`synchronized` 只有传统的锁机制，直接关联到 `monitor` 对象，存在性能上的瓶颈。在 `JDK 1.6` 后，为了提高锁的获取与释放效率，`JVM` 引入了两种锁机制：`偏向锁`和 `轻量级锁`。`JVM` 层面实现加锁的逻辑，不依赖底层操作系统，就没有切换的消耗。这几种锁的实现和转换正是依靠对象头中的 `Mark Word`。`Mark Word` 对锁的状态记录一共有4种：`无锁` 、 `偏向锁` 、`轻量级锁` 和 `重量级锁`。

引入了偏向锁和轻量级锁，就是为了在不同的使用场景使用不同的锁，进而提高效率。**锁只有升级，没有降级。**
![](https://resource.lzyan.fun/PigGo/1644751000(1).jpg)

### synchronized 和 ReentrantLock 的区别？

`ReentrantLock` 是 `java.util.concurrent（J.U.C）`包中的锁。
```java
public class LockExample {

    private Lock lock = new ReentrantLock();

    public void func() {
        lock.lock();
        try {
            for (int i = 0; i < 10; i++) {
                System.out.print(i + " ");
            }
        } finally {
            lock.unlock(); // 确保释放锁，从而避免发生死锁。
        }
    }
}

public static void main(String[] args) {
    LockExample lockExample = new LockExample();
    ExecutorService executorService = Executors.newCachedThreadPool();
    executorService.execute(() -> lockExample.func());
    executorService.execute(() -> lockExample.func());
}

//Output
0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9
```
**比较**

- 锁的实现：`synchronized` 是 `JVM` 实现的，而 `ReentrantLock` 是 `JDK` 实现的一个普通类，基于 `AQS(AbstractQueuedSynchronizer)`。

- 性能：新版本 `Java` 对 `synchronized` 进行了很多优化，例如自旋锁等，`synchronized` 与 `ReentrantLock` 大致相同。

- 等待可中断：当持有锁的线程长期不释放锁的时候，正在等待的线程可以选择放弃等待，改为处理其他事情。`ReentrantLock` 可中断，而 `synchronized` 不行。

- 公平锁：公平锁是指多个线程在等待同一个锁时，必须按照申请锁的时间顺序来依次获得锁。`synchronized` 中的锁是非公平的，`ReentrantLock` 默认情况下也是非公平的，但是也可以是公平的。

- 锁绑定多个条件：一个 `ReentrantLock` 可以同时绑定多个 `Condition` 对象。

- 可重入：两者都是 `可重入锁`。

**使用选择**：除非需要使用 `ReentrantLock` 的高级功能，否则优先使用 `synchronized`。这是因为 `synchronized` 是 `JVM` 实现的一种锁机制，`JVM` 原生地支持它，而 `ReentrantLock` 不是所有的 `JDK` 版本都支持。并且使用 `synchronized` 不用担心没有释放锁而导致死锁问题，因为 `JVM` 会确保锁的释放。

## volatile

### 说一说对 volatile 关键字的了解？

`volatile` 的出现和 `CPU 缓存模型` 和 `JMM（Java内存模型）` 有关，`volatile`通常被比喻成 `轻量级的synchronized` ，是Java虚拟机提供的最轻量级的同步机制，是Java并发编程中比较重要的一个关键字。

与 `synchronized` 不同， `volatile` 是一个变量修饰符，只需要在声明一个可能被多线程同时访问的变量时，使用 `volatile` 修饰就可以了，无法修饰方法及代码块等。

### volatile 的作用和 synchronized 的区别？

说到 `volatile` 的作用首先得了解并发编程中的三大特性：

1. **原子性**：一个的操作或者多次操作，要么所有的操作全部都得到执行并且不会受到任何因素的干扰而中断，要么所有的操作都执行，要么都不执行。

2. **可见性** ：多个线程共同访问共享变量时，某个线程修改了此变量，其他线程能立即看到修改后的值。

3. **有序性** ：代码在执行的过程中的先后顺序，`Java` 在编译器以及运行期间的优化，代码的执行顺序未必就是编写代码时候的顺序。(由于JMM模型中允许编译器和处理器为了效率，进行指令重排序的优化。指令重排序在单线程内表现为串行语义，在多线程中会表现为无序。那么多线程并发编程中，就要考虑如何在多线程环境下可以允许部分指令重排，又要保证有序性)

`synchronized` 关键字同时保证上述三种特性。`volatile` 关键字作用的是保证可见性和有序性，并不保证原子性。`volatile` 关键字主要用于解决变量在多个线程之间的可见性，而 `synchronized` 关键字解决的是多个线程之间访问资源的同步性。

### 简单描述一下 CPU 缓存模型 和 JMM（Java内存模型）？

- **CPU 缓存模型**

`CPU` 缓存是为了解决 `CPU` 处理速度和内存处理速度不对等的问题。因为 `CPU` 运算速度要比内存读写速度快得多。这种访问速度的显著差异，导致 `CPU` 可能会花费很长时间等待数据到来或把数据写入内存。

基于此，现在 `CPU` 大多数情况下读写都不会直接访问内存（`CPU` 都没有连接到内存的管脚），取而代之的是 `CPU` 缓存，`CPU` 缓存是位于 `CPU` 与内存之间的 `临时存储器` ，它的容量比内存小得多但是交换速度却比内存快得多。而缓存中的数据是内存中的一小部分数据，但这一小部分是短时间内 `CPU` 即将访问的，当 `CPU` 调用大量数据时，就可先从缓存中读取，从而加快读取速度。

一个简单的 `CPU Cache` 示意图表示主内存 `Main Menory` 数据读取之间的关系：

![](https://resource.lzyan.fun/PigGo/20220216153807.png)

`CPU Cache` 的工作方式：程序以及数据被加载到 Main Memory`，指令和数据被加载到 `CPU Cache` 中，当 `CPU` 需要用到的时候就可以直接从 `CPU Cache` 中读取数据，当运算完成后，再将运算得到的数据写回 `Main Memory` 中。如果服务器是单核 `CPU`，那么这不会有问题，但是如果服务器是多核 `CPU`，这样就会存在**内存缓存不一致性的问题** 。

比如执行一个 `i++` 操作的话，如果两个线程同时执行的话，假设两个线程从 `CPU Cache` 中读取的 `i=1` ，两个线程做了 `1++` 运算完之后再写回 `Main Memory` 之后 `i=2`，而正确结果应该是 `i=3`。

CPU 为了解决内存缓存不一致性问题可以通过制定缓存一致协议或者其他手段来解决。例如 `CPU` 制造商制定了一个规则：当一个CPU修改缓存中的字节时，服务器中其他CPU会被通知，它们的缓存将视为无效。

- **JMM（Java内存模型）**

`JMM（Java内存模型）` 是一个抽象出来的概念抽象了线程和主内存之间的关系，是一种规范，试图屏蔽各种硬件和操作系统的内存访问差异，以实现让 `Java` 程序在各种平台下都能达到一致的内存访问效果。`Java` 内存模型规范了 `Java` 虚拟机与计算机内存是如何协同工作的：规定了一个线程如何和何时可以看到由其他线程修改过后的共享变量的值，以及在必须时如何同步的访问共享变量。

与 `Java内存结构` 的区别：`java内存结构` 是解决 `java` 中的数据如何存放的问题; `java内存模型` 是解决 `java` 中多个线程共享数据的问题。

`java内存模型` 大概就是长这样：

![](https://resource.lzyan.fun/PigGo/20220216151821.png)

在 `JDK1.2` 之前，`Java 内存模型` 实现总是从主内存读取变量，是不需要进行特别的注意的。而在当前的 `Java 内存模型` 下，线程可以把变量保存 `本地内存（比如机器的寄存器）` 中，而不是直接在主存中进行读写。这就可能造成一个线程在主存中修改了一个变量的值，而另外一个线程还继续使用它在寄存器中的变量值的拷贝，造成数据的不一致。

> 主内存:所有线程创建的实例对象都存放在主内存中，不管该实例对象是成员变量还是方法中的本地变量(也称局部变量)
> 
> 本地内存 ：每个线程都有一个私有的本地内存来存储共享变量的副本，并且，每个线程只能访问自己的本地内存，无法访问其他线程的本地内存。本地内存是 JMM 抽象出来的一个概念，存储了主内存中的共享变量副本。

要解决这个问题，就需要把变量声明为 `volatile` ，指示 `JVM` 这个变量是共享且不稳定的，每次使用它都到主存中进行读取。这就是 `volatile` 保证变量的可见性。

![](https://resource.lzyan.fun/PigGo/20220216153315.png)

### 双重检验锁方式实现懒汉式单例模式的原理？

实现代码

```java
public class Singleton {
    //volatile 防止指令重排 和可见性
    private volatile static Singleton uniqueInstance;

    private Singleton() {
    }

    public Singleton getUniqueInstance() {
        //先判断对象是否已经实例化过，没有实例化才进入加锁代码
        if (uniqueInstance == null) {
            //类对象加锁
            synchronized (Singleton.class) {
                //避免 singleTon== null时，第一个线程实例化后，进入阻塞状态的线程被唤醒后仍会进行实例化。
                if (uniqueInstance == null){
                    uniqueInstance = new Singleton();
                }
            }
        }
        return uniqueInstance;
    }
}
```
第一个 `if (uniqueInstance == null)` 是为了判断对象是否已经实例化，只有非实例化的情况下才能进入 `if` 块进行加锁。第二个 `if(uniqueInstance == null)` 是假设存在 `线程1` 和 `线程2`，当第一次实例化时，他们都能进入到第一个 `if` 块中，当 `线程1` 获取到 `Singleton` `的锁，由于此时uniqueInstance` 还未加载可以进入第二个 `if` 块，当执行完 `uniqueInstance = new Singleton();` 便返回 `uniqueInstance`，此时 `uniqueInstance` 已经实例化，但是此时线程2获得锁，如果没有第二个 `if` 块则会再次 `new` 一个新的实例出来，就违反了单例的规则。


对于采用 `volatile` 关键字修饰是很有必要的，对于 `uniqueInstance = new Singleton();` 这段代码分为三步执行：

1. 为 `uniqueInstance` 分配内存空间。

2. 初始化 `uniqueInstance`。

3. 将 `uniqueInstance` 指向分配的内存地址。

但是由于 `JVM` 具有指令重排的特性，执行顺序有可能变成 `1->3->2`。指令重排在单线程环境下不会出现问题，但是在多线程环境下会导致一个线程获得还没有初始化的实例。例如，线程 `T1` 执行了 `1` 和 `3`，此时 `T2` 调用 `getUniqueInstance()` 后发现 `uniqueInstance` 不为空，因此返回 `uniqueInstance`，但此时 `uniqueInstance` 还未被初始化。使用 `volatile` 可以禁止 JVM 的指令重排，保证在多线程环境下也能正常运行。

## CAS

### CAS 了解吗？

`CAS` 全称 `Compare and swap`，即 `比较并交换`，它是一条 `CPU` 同步原语。是一种硬件对并发的支持，针对多处理器操作而设计的一种特殊指令，用于管理对共享数据的并发访问。通过处理器的指令来保证操作的原子性，它主要包含三个操作数：`变量内存地址 M`、`旧的预期值 A`、`准备设置的新值 B`。当且仅当 `M` 的值等于 `A` 时，`CAS` 通过原子方式用新值 `B` 来更新 `M` 的 值，否则不会执行任何操作。将旧的预期值与内存值进行对比，判断是否有被修改过，这就是 `CAS` 的核心。

`CAS` 并发原语体现在` Java` 语言中的 `sum.misc.Unsafe` 类中的各个方法。`Unsafe` 的 `CAS` 依赖的是 `JVM` 针对不同的操作系统实现的硬件指令 `Atomic::cmpxchg`。这是一种完全依赖于硬件的功能，`Atomic::cmpxchg` 的实现使用了汇编的 `CAS` 操作，并使用 `CPU` 提供的 `lock` 信号保证其原子性。

由于 `CAS` 是一种系统原语，原语属于操作系统用于范畴，是由若干条指令组成的，用于完成某个功能的一个过程，并且原语的执行必须是连续的，在执行过程中不允许被中断，`CAS` 是一条 `CPU` 的原子指令，不会造成数据不一致问题。

`CAS` 只适用于线程冲突较少的情况。`CAS` 的典型应用场景是：原子类、自旋锁

### CAS 带来的问题？

一般情况下，`CAS` 比锁性能更高。因为 `CAS` 是一种非阻塞算法，所以其避免了线程阻塞和唤醒的等待时间。

但是，事物总会有利有弊，CAS 也存在三大问题：

- **ABA 问题**

如果一个变量初次读取的时候是 `A` 值，它的值被改成了 `B`，后来又被改回为 `A`，那 `CAS` 操作就会误认为它从来没有被改变过。

`JUC` 包提供了一个带有标记的原子引用类 `AtomicStampedReference` 来解决这个问题，它可以通过控制变量值的版本来保证 `CAS` 的正确性。大部分情况下 `ABA` 问题不会影响程序并发的正确性，如果需要解决 `ABA` 问题，改用传统的互斥同步可能会比原子类更高效。

- **循环时间长开销大**

在并发量比较高的情况下，如果许多线程反复尝试更新某一个变量，却又一直更新不成功，又因为自旋的时候会一直占用 `CPU`，如果 `CAS` 一直更新不成功就会一直占用，造成 `CPU` 的浪费。

如果 `JVM` 能支持处理器提供的 `pause` `指令那么效率会有一定的提升，pause` 指令有两个作用：1.可以延迟流水线执行指令（de-pipeline）,使 `CPU` 不会消耗过多的执行资源，延迟的时间取决于具体实现的版本，在一些处理器上延迟时间是零。2.它可以避免在退出循环的时候因内存顺序冲突（memory order violation）而引起 `CPU` 流水线被清空（CPU pipeline flush），从而提高 `CPU` 的执行效率。

- **只能保证一个共享变量的原子性**

当对一个共享变量执行操作时，我们可以使用循环 `CAS` 的方式来保证原子操作，但是对多个共享变量操作时，循环 `CAS` 就无法保证操作的原子性，这个时候就可以用锁。

从` Java 1.5` 开始 `JDK` 提供了 `AtomicReference` 类来保证引用对象之间的原子性，可以把多个变量放在一个对象里来进行 `CAS` 操作。

## ThreadLocal

### 描述 ThreadLocal 的作用以及应用场景？

通常情况下，创建的全局变量是可以被任何一个线程访问并修改的，所以这些变量不是线程安全的，但是可以使用同步技术。如果不想使用同步技术，想实现每一个线程都有自己的专属本地变量， `JDK` 中提供的 `ThreadLocal` 类正是为了解决这样的问题。`ThreadLocal` 类主要解决的就是让每个线程绑定自己的值，可以将 `ThreadLocal` 类形象的比喻成存放数据的盒子，盒子中可以存储每个线程的私有数据。

创建了一个 `ThreadLocal` 变量，那么访问这个变量的每个线程都会有这个变量的本地副本，这也是 `ThreadLocal` 变量名的由来。他们可以使用 `get（）` 和 `set（）` 方法来获取默认值或将其值更改为当前线程所存的副本的值，从而避免了线程安全问题。

```java
public class ThreadLocal<T> {
    // 用于获取 ThreadLocal 在当前线程中保存的变量副本。
    public T get() {}
    // 用于设置当前线程中变量的副本。
    public void set(T value) {}
    // 用于删除当前线程中变量的副本。如果此线程局部变量随后被当前线程读取，则其值将通过调用其 initialValue 方法重新初始化，除非其值由中间线程中的当前线程设置。 这可能会导致当前线程中多次调用 initialValue 方法。
    public void remove() {}
    // 为 ThreadLocal 设置默认的 get 初始值，需要重写 initialValue 方法 。
    public static <S> ThreadLocal<S> withInitial(Supplier<? extends S> supplier) {}
}
```

应用场景：`JDBC 连接`、`Session 管理`、`Spring 事务管理`、`调用链，参数传递`、`AOP`

`JDBC` 连接时：由于 `JDBC` 连接 对象不是线程安全的，因此当多线程应用程序在没有协同的情况下使用全局变量时，就不是线程安全的，此时便可以通过把 `JDBC` 的连接对象保存到 `ThreadLocal` 中，每个线程都拥有属于自己的连接对象副本。

### 描述 ThreadLocal 的原理？

- `Thread` 类中维护着一个 `ThreadLocal.ThreadLocalMap` 类型的实例变量 `threadLocals`。即每个线程都有一个属于自己的 `ThreadLocalMap`，这个成员就是用来存储当前线程独占的变量副本。

- `ThreadLocalMap` 是 `ThreadLocal` 的内部类，它维护着一个 `Entry` 数组，`Entry` 继承了 `WeakReference` ，所以是弱引用。 每个 `Entry` 代表一个完整的对象，`key` 是 `ThreadLocal` 本身，`value` 是传递进来的对象（变量副本）。

- `ThreadLocalMap` 虽然是类似 `Map` 结构的数据结构，但它并没有实现 `Map` 接口。它不支持 `Map` 接口中的 `next` 方法，这意味着 `ThreadLocalMap` 中解决 `Hash` 冲突的方式并非 `拉链表` 方式。实际上，`ThreadLocalMap` 采用 `线性探测` 的方式来解决 `Hash` 冲突

- `ThreadLocal` 可以理解为只是 `ThreadLocalMap` 的封装，传递了变量值。 `ThrealLocal` 类中可以通过 `Thread.currentThread()` 获取到当前线程对象后，直接通过 `getMap(Thread t)` 可以访问到该线程的 `ThreadLocalMap` 对象。

```java
public class Thread implements Runnable {
    // ...
    ThreadLocal.ThreadLocalMap threadLocals = null;
    // ...
}

static class ThreadLocalMap {
    // ...
    static class Entry extends WeakReference<ThreadLocal<?>> {
        /** The value associated with this ThreadLocal. */
        Object value;

        Entry(ThreadLocal<?> k, Object v) {
            super(k);
            value = v;
        }
    }
    // ...
}
```
`ThreadLocal` 内存结构图：

![](https://resource.lzyan.fun/PigGo/20220217104443.png)

### 描述 ThreadLocal 的内存泄露问题？

`ThreadLocalMap` 的 `Entry` 继承了 `WeakReference`，所以它的 `key （ThreadLocal 对象）` 是弱引用，而 `value （变量副本）` 是强引用。

- 如果 `ThreadLocal` 对象没有外部强引用来引用它，那么 `ThreadLocal` 对象会在下次 `GC` 时被回收。

- 此时，`Entry` 中的 `key` 已经被回收，但是 `value` 由于是强引用不会被垃圾收集器回收。如果创建 `ThreadLocal` 的线程一直持续运行，那么 `value` 永远无法被 `GC` 回收，产生内存泄露。

> Java的4种引用:
> 
> 强引用：强引用是最常见的，只要把一个对象赋给一个引用变量，这个引用变量就是一个强引用。只要对象没有被置null，在GC时就不会被回收。
> 
> 软引用：需要继承 SoftReference实现，如果内存充足，只有软引用指向的对象不会被回收。如果内存不足了，只有软引用指向的对象就会被回收。
> 
> 弱引用：需要继承 WeakReference 实现，只要发生 GC，只有弱引用指向的对象就会被回收。
> 
> 虚引用：需要继承 PhantomReference 实现，跟踪对象垃圾回收的状态，当回收时通过引用队列做些通知类的工作。

`ThreadLocalMap` 实现中已经考虑了内存泄漏这种情况，在调用 `set()`、`get()`、`remove()` 方法的时候，会清理掉 `key` 为 `null` 的记录。使用完 `ThreadLocal` 方法后最好手动调用 `remove()` 方法

```java
ThreadLocal<String> threadLocal = new ThreadLocal();
try {
    threadLocal.set("xxx");
    // ...
} finally {
    threadLocal.remove();
}
```

## 并发容器

### 同步容器与并发容器分别有哪些？

- **同步容器**

Java中的同步容器主要包括两类:

1. `Vector`、`Stack`、`Hashtable`
      - `Vector` - `Vector` 实现了 `List` `接口。Vector` 实际上就是一个数组，和 `ArrayList` 类似。但是 `Vector` 中的方法都是 `synchronized` 方法，即进行了同步措施。
      - `Stack` - `Stack` 也是一个同步容器，它的方法也用 `synchronized` 进行了同步，它实际上是继承于 `Vector` 类。
      - `Hashtable`- `Hashtable` 实现了 `Map` 接口，它和 `HashMap` 很相似，但是 `Hashtable` 进行了同步处理，而 `HashMap` 没有。

2. `Collections` 类中提供的静态工厂方法创建的类（由 `Collections.synchronizedXXX` 等方法）

同步容器的同步原理就是在其 `get`、`set`、`size` 等主要方法上用 `synchronized` 修饰。由于被 `synchronized` 修饰的方法，每次只允许一个线程执行，其他试图访问这个方法的线程只能等待。 `synchronized` 的互斥同步会产生阻塞和唤醒线程的开销，这种方式比没有使用 `synchronized` 的容器性能要差。并且同步容器未必真的安全，在做复合操作时，仍然需要加锁来保护。

- **并发容器**

同步容器将所有对容器状态的访问都串行化，以保证线程安全性，这种策略会严重降低并发性。`Java 1.5 `后提供了多种并发容器，使用并发容器来替代同步容器，可以极大地提高伸缩性并降低风险。

`JDK` 提供的这些容器大部分在 `java.util.concurrent` 包中:

|       并发容器        | 对应普通容器 |                             描述                             |
| :-------------------: | :----------: | :----------------------------------------------------------: |
|   ConcurrentHashMap   |   HashMap    | Java 1.8 之前采用分段锁机制细化锁粒度，降低阻塞，从而提高并发性；Java 1.8 之后基于 CAS 实现 |
| ConcurrentSkipListMap |  SortedMap   |                         基于跳表实现                         |
| CopyOnWriteArrayList  |  ArrayList   | 写操作在一个复制的数组上进行，读操作还是在原始数组中进行，读写分离，互不影响 |
|  CopyOnWriteArraySet  |     Set      |                 基于CopyOnWriteArrayList实现                 |
| ConcurrentSkipListSet |  SortedSet   |                基于ConcurrentSkipListMap实现                 |
| ConcurrentLinkedQueue |    Queue     |          线程安全的无界队列，底层采用单链，支持FIFO          |
| ConcurrentLinkedDeque |    Deque     |   线程安全的无界双端队列，底层采用双向链表，支持FIFO和FILO   |
|  ArrayBlockingQueue   |    Queue     |                      数组实现的阻塞队列                      |
|  LinkedBlockingQueue  |    Queue     |                      链表实现的阻塞队列                      |
|  LinkedBlockingDeque  |    Deque     |                  双向链表实现的双端阻塞队列                  |

以容器的命名分类：

1. `Concurrent` - 这类型的锁竞争相对于 `CopyOnWrite` 要高一些，但写操作代价要小一些。此外，`Concurrent` 往往提供了较低的遍历一致性，即：当利用迭代器遍历时，如果容器发生修改，迭代器仍然可以继续进行遍历。代价就是，在获取容器大小 `size()` ，容器是否为空等方法，不一定完全精确，但这是为了获取并发吞吐量的设计取舍，可以理解。与之相比，如果是使用同步容器，就会出现 `fail-fast` 问题，即：检测到容器在遍历过程中发生了修改，则抛出 `ConcurrentModificationException`，不再继续遍历。

2. `CopyOnWrite` - 一个线程写，多个线程读。读操作时不加锁，写操作时通过在副本上加锁保证并发安全，空间开销较大

3. `Blocking` - 内部实现一般是基于锁，提供阻塞队列的能力。

使用这些并发容器与使用非线程安全的集合类完全相同，因为所有的同步和加锁的逻辑都在集合内部实现，对外部调用者来说，只需要正常按接口引用，其他代码和原来的非线程安全代码完全一样。即当我们需要多线程访问时，把 `Map<String, String> map = new HashMap<>();`,改成 `Map<String, String> map = new ConcurrentHashMap<>();` 就可以了。

### 并发场景下如何使用 Map、List？

- **Map** - 如果对数据有强一致要求，则需使用 `Hashtable`；在大部分场景通常都是弱一致性的情况下，使用 `ConcurrentHashMap` 即可；如果数据量在千万级别，且存在大量增删改操作，则可以考虑使用 `ConcurrentSkipListMap`。

- **List** - 读多写少用 `CopyOnWriteArrayList`。写多读少用 `ConcurrentLinkedQueue` ，但由于是无界的，要有容量限制，避免无限膨胀，导致内存溢出。

### CopyOnWriteArrayList 是如何实现？

- **读写分离（源码简单分析）**

`CopyOnWriteArrayList` 写操作需要加锁，防止并发写入时导致写入数据丢失。当 `List` 需要被修改的时候，并不修改原有内容，而是对原有数据进行一次复制，将修改的内容写入副本。写完之后，就将指向原来内存指针指向新的内存，原来的内存就可以被回收掉了，读写分离，互不影响。

```java
public boolean add(E e) {
    final ReentrantLock lock = this.lock;
    lock.lock();//加锁
    try {
        Object[] elements = getArray();
        int len = elements.length;
        Object[] newElements = Arrays.copyOf(elements, len + 1);//拷贝新数组
        newElements[len] = e;
        setArray(newElements);
        return true;
    } finally {
        lock.unlock();//释放锁
    }
}
final void setArray(Object[] a) {
    array = a;
}
```

`CopyOnWriteArrayList` 读取操作没有任何同步控制和锁操作，理由就是内部数组 `array` 不会发生修改，只会被另外一个 `array` 替换，因此可以保证数据安全。并且多个迭代器可以同时遍历而不会相互阻塞。

```java
private transient volatile Object[] array;
public E get(int index) {
    return get(getArray(), index);
}
@SuppressWarnings("unchecked")
private E get(Object[] a, int index) {
    return (E) a[index];
}
final Object[] getArray() {
    return array;
}
```

- **适用场景**

`CopyOnWriteArrayList` 在写操作的同时允许读操作，大大提高了读操作的性能，因此很适合读多写少的应用场景。

但是 `CopyOnWriteArrayList` 有其缺陷：
  1. 内存占用：在写操作时需要复制一个新的数组，使得内存占用为原来的两倍左右；
  
  2. 数据不一致：读操作不能读取实时性的数据，因为部分写操作的数据还未同步到读数组中。

所以 `CopyOnWriteArrayList` 不适合内存敏感以及对实时性要求很高的场景。

### ConcurrentHashMap 是如何实现（待完善）？

`ConcurrentHashMap` 在 `JDK 1.7` 和 `JDK 1.8` 的实现方式有所不同。

- **JDK 1.7**

1. 数据结构：数组＋单链表
2. 并发机制：采用分段锁机制细化锁粒度，降低阻塞，从而提高并发性。

`ConcurrentHashMap` 是由 `Segment` 数组结构和 `HashEntry` 数组结构组成，即 `ConcurrentHashMap` 把哈希桶切分成` 小数组（Segment）`，每个小数组有 `n` 个 `HashEntry` 组成。其中，`Segment` 继承了 `ReentrantLock`，所以 `Segment` 是一种可重入锁，扮演锁的角色；`HashEntry` 用于存储键值对数据。首先将数据分为一段一段的存储，然后给每一段数据配一把锁，当一个线程占用锁访问其中一个段数据时，其他段的数据也能被其他线程访问，能够实现真正的并发访问。

![](https://resource.lzyan.fun/PigGo/20220219145745.png)

- **JDK 1.8**

1. 数据结构：数组＋单链表＋红黑树
2. 并发机制：取消分段锁，之后基于 CAS + synchronized 实现。

在锁的实现上，抛弃了原有的 `Segment` 分段锁，采用 `CAS + synchronized` 实现更加低粒度的锁。将锁的级别控制在了更细粒度的哈希桶元素级别，也就是说只需要锁住这个链表头结点（红黑树的根节点），就不会影响其他的哈希桶元素的读写，大大提高了并发度。现代 `JDK` 中，`synchronized` 已经被不断优化，可以不再过分担心性能差异，另外，相比于 `ReentrantLock`，它可以减少内存消耗，这是个非常大的优势。

![](https://resource.lzyan.fun/PigGo/20220219150232.png)

### ConcurrentHashMap 和 ConcurrentSkipListMap 的使用区别？

`ConcurrentHashMap` 和 `ConcurrentSkipListMap`，它们从应用的角度来看，主要区别在于 `ConcurrentHashMap` 的 `key` 是无序的，而 `ConcurrentSkipListMap` 的 `key` 是有序的。所以如果你需要保证 `key` 的顺序，就只能使用 `ConcurrentSkipListMap`。

使用 `ConcurrentHashMap` 和 `ConcurrentSkipListMap` 需要注意的地方是，它们的 `key` 和 `value` 都不能为空，否则会抛出 `NullPointerException` 这个运行时异常。

`ConcurrentHashMap` 用于多线程的 ，如果 `map.get(key)` 得到了 `null` ，无法判断映射的 `value` 是 `null` ，还是没有找到对应的 `key` 而为 `null` ，这就有了二义性。而用于单线程状态的 `HashMap` 可以直接用 `containsKey(key)` 去判断到底是否包含了这个 `null` ，因为它不涉及到当线程 `A` 调用 `ConcurrentHashMap.get(key)` 方法之后，`containsKey(key)` 之前，线程 `B` 执行了 `ConcurrentHashMap.put(key, null)` 的操作。

## 线程池

### 为什么使用线程池？

线程池提供了一种限制和管理资源（包括执行一个任务）。每个线程池还维护一些基本统计信息，例如已完成任务的数量。如果并发请求数量很多，但每个线程执行的时间很短，就会出现频繁的创建和销毁线程。如此一来，会大大降低系统的效率，可能频繁创建和销毁线程的时间、资源开销要大于实际工作的所需。

正是由于这个问题，所以有必要引入线程池。使用 `线程池的好处` 有以下几点：

- **降低资源消耗** - 通过重复利用已创建的线程降低线程创建和销毁造成的消耗。

- **提高响应速度** - 当任务到达时，任务可以不需要等到线程创建就能立即执行。

- **提高线程的可管理性** - 线程是稀缺资源，如果无限制的创建，不仅会消耗系统资源，还会降低系统的稳定性，使用线程池可以进行统一的分配，调优和监控。但是要做到合理的利用线程池，必须对其原理了如指掌。

### Executor 框架结构的组成？

> Executor 框架是 Java5 之后引进的，在 Java 5 之后，通过 Executor 来启动线程比使用 Thread 的 start 方法更好，除了更易管理，效率更好（用线程池实现，节约开销）外，还有关键的一点：有助于避免 this 逃逸问题。Executor框架实现的就是线程池的功能。


1. **任务** - 也就是工作的单元，执行任务需要实现的 `Runnable` 接口 或 `Callable` 接口。`Runnable` 接口或 `Callable` 接口 实现类都可以被 `ThreadPoolExecutor` 或 `ScheduledThreadPoolExecutor` 执行。

2. **任务的执行** - 也就是把任务分派给多个线程的执行机制，包括 `Executor` 接口及继承自 `Executor` 接口的 `ExecutorService` 接口。`ThreadPoolExecutor` 和 `ScheduledThreadPoolExecutor` 这两个关键类实现了 `ExecutorService` 接口，实际上 `ScheduledThreadPoolExecutor` 也是继承了 `ThreadPoolExecutor（重点类）`。

3. **异步计算的结果** - `Future` 接口以及它的实现类 `FutureTask` 类都可以代表异步计算的结果。

![](https://resource.lzyan.fun/PigGo/33233.png)

以 `Executor` 框架的使用示意图，解释三个组成部分之间的关系：

- 主线程首先要创建实现 `Runnable` 或者 `Callable` 接口的任务对象。

- 把创建完成的实现 `Runnable/Callable` 接口的对象直接交给 `ExecutorService` 执行：
`ExecutorService.execute（Runnable command）`、`ExecutorService.submit（Runnable task）`、`ExecutorService.submit（Callable <T> task）`。

- 如果执行 `ExecutorService.submit（）`，`ExecutorService` 将返回一个实现 `Future` 接口的对象。由于 `FutureTask` 实现了 `Runnable`，我们也可以创建 `FutureTask`，然后直接交给 `ExecutorService` 执行。

- 最后，主线程可以执行 `FutureTask.get()` 方法来等待任务执行完成。主线程也可以执行 `FutureTask.cancel（boolean mayInterruptIfRunning）`来取消此任务的执行。

![](https://resource.lzyan.fun/PigGo/20220218153002.png)

### 如何创建线程池？

- **方式一：通过 `Executor` 框架的工具类 `Executors` 来实现(不推荐)**

《阿里巴巴 Java 开发手册》中有一条规定

![](https://resource.lzyan.fun/PigGo/20220218162945.png)

虽说不推荐，但是可以了解一下：创建三种类型的 `ThreadPoolExecutor`

   1. FixedThreadPool ： 该方法返回一个固定线程数量的线程池。该线程池中的线程数量始终不变。当有一个新的任务提交时，线程池中若有空闲线程，则立即执行。若没有，则新的任务会被暂存在一个任务队列中，待有线程空闲时，便处理在任务队列中的任务。

   2. SingleThreadExecutor： 方法返回一个只有一个线程的线程池。若多余一个任务被提交到该线程池，任务会被保存在一个任务队列中，待线程空闲，按先入先出的顺序执行队列中的任务。

   3. CachedThreadPool： 该方法返回一个可根据实际情况调整线程数量的线程池。线程池的线程数量不确定，但若有空闲线程可以复用，则会优先使用可复用的线程。若所有线程均在工作，又有新的任务提交，则会创建新的线程处理任务。所有线程在当前任务执行完毕后，将返回线程池进行复用。

- **方式二：通过 `ThreadPoolExecutor` 类构造方法实现**

`ThreadPoolExecutor` 类提供了4种构造方法，可根据需求来自定义一个线程池。

![](https://resource.lzyan.fun/PigGo/20220218164802.png)


### ThreadPoolExecutor 类构造方法创建线程池的核心参数？

`ThreadPoolExecutor` 类中提供的四个构造方法。关键是最长的那个，其余三个都是在这个构造方法的基础上产生

```java
    public ThreadPoolExecutor(int corePoolSize,//线程池的核心线程数量
                              int maximumPoolSize,//线程池的最大线程数
                              long keepAliveTime,//当线程数大于核心线程数时，多余的空闲线程存活的最长时间
                              TimeUnit unit,//时间单位
                              BlockingQueue<Runnable> workQueue,//任务队列，用来储存等待执行任务的队列
                              ThreadFactory threadFactory,//线程工厂，用来创建线程，一般默认即可
                              RejectedExecutionHandler handler//拒绝策略，当提交的任务过多而不能及时处理时，我们可以定制策略来处理任务
                               ) {
        if (corePoolSize < 0 ||
            maximumPoolSize <= 0 ||
            maximumPoolSize < corePoolSize ||
            keepAliveTime < 0)
            throw new IllegalArgumentException();
        if (workQueue == null || threadFactory == null || handler == null)
            throw new NullPointerException();
        this.corePoolSize = corePoolSize;
        this.maximumPoolSize = maximumPoolSize;
        this.workQueue = workQueue;
        this.keepAliveTime = unit.toNanos(keepAliveTime);
        this.threadFactory = threadFactory;
        this.handler = handler;
    }
```
一下参数中前面三个参数是比较核心的：

- `corePoolSize` : 核心线程数线程数定义了最小可以同时运行的线程数量。

- `maximumPoolSize` : 当队列中存放的任务达到队列容量的时候，当前可以同时运行的线程数量变为最大线程数。 

- `workQueue`: 当新任务来的时候会先判断当前运行的线程数量是否达到核心线程数，如果达到的话，新任务就会被存放在队列中

- `keepAliveTime`：当线程池中的线程数量大于 `corePoolSize` 的时候，如果这时没有新的任务提交，核心线程外的线程不会立即销毁，而是会等待，直到等待的时间超过了 `keepAliveTime`才会被回收销毁；

- `unit`： `keepAliveTime` 参数的时间单位。

- `threadFactory`：`executor` 创建新线程的时候会用到。

- `handler`：饱和策略。

### 执行 execute() 和 submit() 的区别？

默认情况下，创建线程池之后，线程池中是没有线程的，需要提交任务之后才会创建线程。`execute()` 向线程池提交一个任务，交由线程池去执行，工作流程如下：

1. 如果 `workerCount < corePoolSize`，则创建并启动一个线程来执行新提交的任务；

2. 如果 `workerCount >= corePoolSize`，且线程池内的阻塞队列未满，则将任务添加到该阻塞队列中；

3. 如果 `workerCount >= corePoolSize` && `workerCount < maximumPoolSize`，且线程池内的阻塞队列已满，则创建并启动一个线程来执行新提交的任务；

4. 如果 `workerCount >= maximumPoolSize`，并且线程池内的阻塞队列已满，则根据拒绝策略来处理该任务, 默认的处理方式是直接抛异常。

![](https://resource.lzyan.fun/PigGo/20220218162331.png)

- `execute()` 方法用于提交不需要返回值的任务，`execute()` 只能接受 `Runnable` 类型的任务，所以无法判断任务是否被线程池执行成功与否。

- `submit()` 类似于 `execute()`，但是它用于提交需要返回值的任务，针对有返回值的线程。线程池会返回一个 `Future` 类型的对象，通过这个 `Future` 对象可以判断任务是否执行成功，并且可以通过 `Future` 的 `get()` 方法来获取返回值，`get()` 方法会阻塞当前线程直到任务完成，而使用 `get(long timeout，TimeUnit unit)` 方法则会阻塞当前线程一段时间后立即返回，这时候有可能任务没有执行完。当传入的是 `Runnable`，使用 `get()` 获得的是 `null`。

### 如何合理配置线程池参数？

自定义线程池就需要自己配置最大线程数 `maximumPoolSize` ，为了高效的并发运行，这时需要看我们的业务是IO密集型还是CPU密集型。

- **CPU 密集型任务(N+1)：** 这种任务消耗的主要是 `CPU` 资源，可以将线程数设置为 `N（CPU 核心数）+1`，比 `CPU` 核心数多出来的一个线程是为了防止线程偶发的缺页中断，或者其它原因导致的任务暂停而带来的影响。一旦任务暂停，`CPU` 就会处于空闲状态，而在这种情况下多出来的一个线程就可以充分利用 `CPU` 的空闲时间。 

- **I/O 密集型任务(2N)：** 这种任务应用起来，系统会用大部分的时间来处理 `I/O` 交互，而线程在处理 `I/O` 的时间段内不会占用 `CPU` 来处理，这时就可以将 `CPU` 交出给其它线程使用。因此在 `I/O` 密集型任务的应用中，我们可以多配置一些线程，具体的计算方法是 `2N`。

[当以上都不适用时，选用动态化线程池，看美团技术团队的实践](https://tech.meituan.com/2020/04/02/java-pooling-pratice-in-meituan.html)

如何判断是 `CPU` 密集任务还是 `IO` 密集任务？

`CPU` 密集型简单理解就是利用 `CPU` 计算能力的任务比如你在内存中对大量数据进行排序。但凡涉及到网络读取，文件读取这类都是 `IO` 密集型，这类任务的特点是 `CPU` 计算耗费时间相比于等待 `IO` 操作完成的时间来说很少，大部分时间都花在了等待 `IO` 操作完成上。

## AQS
## Atomic 原子类

