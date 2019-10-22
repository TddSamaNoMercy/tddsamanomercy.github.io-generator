---
title: "Java多线程总结"
date: 2019-10-23T01:39:45+08:00
draft: true
---
# 1.线程&进程

* 其本质的区别在于每个进程拥有一套自己的变量，而多线程则可以共享数据
* 在操作系统中线程更轻量级，创建或者撤销一个线程的开销要远远小于进程

# 2.多线程的目的
* 更好的利用cpu的资源
* Java的执⾏模型是同步/阻塞(block)的

## 并发和并行
* 并行：多个cpu实例或者多台机器同时执行一段处理逻辑，是真正的同时
* 并发：通过cpu调度算法，让用户看上去同时执行，实际上从cpu操作层面不是真正的同时。并发往往在场景中有公用的资源，那么针对这个公用的资源往往产生瓶颈，我们会用TPS或者QPS来反应这个系统的处理能力。（Java中的网络IO或者读写文件）
* 同步：Java中的同步指的是通过人为的控制和调度，保证共享资源的多线程访问成为线程安全，来保证结果的准确。

# 3.线程的生命周期
* 线程状态

![](/images/JavaConcurrent/1.png)

* 线程状态转换

![](/images/JavaConcurrent/2.png)

* 关于中断：它并不像stop方法那样会中断一个正在运行的线程。线程会不时地检测中断标识位，以判断线程是否应该被中断（中断标识值是否为true）。中断只会影响到wait状态、sleep状态和join状态。被打断的线程会抛出InterruptedException。
    * Thread.interrupted()检查当前线程是否发生中断，返回boolean
    * synchronized在获锁的过程中是不能被中断的。
    * 中断是一个状态！interrupt()方法只是将这个状态置为true而已。所以说正常运行的程序不去检测状态，就不会终止，而wait等阻塞方法会去检查并抛出异常。

# 4.开启一个线程
* 继承Thread类
    * Java中只有这么⼀种东⻄代表线程
    * start⽅法才能并发执行
    * 每多开⼀个线程，就多⼀个执⾏流
    * ⽅法栈(局部变量)是线程私有的
    * 静态变量/类变量是被所有线程共享的
* 实现Runnable接口
* 开启一个新的线程是昂贵的
    * 第⼀，CPU切换上下⽂很慢
    * 第⼆，线程需要占⽤内存等系统资源 
* 线程常用方法
    * sleep() 方法和 wait() 方法区别和共同点：
        1. 每个对象都有一个锁来控制同步访问，Synchronized关键字可以和对象的锁交互，来实现同步方法或同步块。sleep()方法正在执行的线程主动让出CPU（然后CPU就可以去执行其他任务），在sleep指定时间后CPU再回到该线程继续往下执行(注意：sleep方法只让出了CPU，而并不会释放同步资源锁！！！)；wait()方法则是指当前线程让自己暂时退让出同步资源锁，以便其他正在等待该资源的线程得到该资源进而运行，只有调用了notify()方法，之前调用wait()的线程才会解除wait状态，可以去参与竞争同步资源锁，进而得到执行。（注意：notify的作用相当于叫醒睡着的人，而并不会给他分配任务，就是说notify只是让之前调用wait的线程有权利重新参与线程的调度）；

        2. sleep()方法可以在任何地方使用；wait()方法则只能在同步方法或同步块中使用；

        3. sleep()是线程线程类（Thread）的方法，调用会暂停此线程指定的时间，但监控依然保持，不会释放对象锁，到时间自动恢复；wait()是Object的方法，调用会放弃对象锁，进入等待队列，待调用notify()/notifyAll()唤醒指定的线程或者所有线程，才会进入锁池，不再次获得对象锁才会进入运行状态；
    * Thead类中start()方法和run()方法的区别:
        * 调用start方法方可启动线程，而run方法只是thread的一个普通方法调用，还是在主线程里执行。

# 5.线程安全
* 线程安全：经常用来描绘一段代码。指在并发的情况之下，该代码经过多线程使用，线程的调度顺序不影响任何结果

## 享受多线程带来的便利的同时多线程有很多安全隐患
* 由于很多操作不是原子性的，并共享了同一变量，使得在多线程环境下容易出现问题，数据错误
* 死锁:因为一个线程无法满足条件释放调锁，导致所有线程都进入阻塞状态；例如调用notify()方法时恰好唤醒的线程并不能满足条件，进如wait状态，而原先线程执行完毕后因为条件不满足也会进入wait状态，导致死锁
* 预防死锁产⽣的原则:所有的线程都按照相同的顺序获得资源的锁 

# 6.实现线程安全的方法
## volatile
* 针对多线程使用的变量如果不是volatile或者final修饰的，很有可能产生不可预知的结果（另一个线程修改了这个值，但是之后在某线程看到的是修改之前的值）。其实道理上讲同一实例的同一属性本身只有一个副本。但是多线程是会缓存值的，本质上，volatile就是不去缓存，直接取值。在线程安全的情况下加volatile会牺牲性能。
## Object类⾥的线程⽅法
* Java从⼀开始就把线程作为语⾔特性，供语⾔级的⽀持

![](/images/JavaConcurrent/3.png)

* monitor

    * 他们是应用于同步问题的人工线程调度工具。讲其本质，首先就要明确monitor的概念，Java中的每个对象都有一个监视器，来监测并发代码的重入。在非多线程编码时该监视器不发挥作用，反之如果在synchronized 范围内，监视器发挥作用。

    * wait/notify必须存在于synchronized块中。并且，这三个关键字针对的是同一个监视器（某对象的监视器）。这意味着wait之后，其他线程可以进入同步块执行。

    * 当某代码并不持有监视器的使用权时（如图中的状态，即脱离同步块）去wait或notify，会抛出java.lang.IllegalMonitorStateException。也包括在synchronized块中去调用另一个对象的wait/notify，因为不同对象的监视器不同，同样会抛出此异常。 
* Lock/Condition 
## synchronized
* synchronized同步块
* 同步块同步了什么东⻄？
    * synchronized(⼀个对象) 把这个对象当成锁
    * Static synchronized⽅法 把Class对象当成锁
    * 实例的synchronnized⽅法把该实例当成锁
* Collections.synchronized
## java.util.concurrent

* ThreadLocal类
    * 用处：保存线程的独立变量。对一个线程类（继承自Thread)当ThreadLocal维护变量时，ThreadLocal为每个使用该变量的线程提供独立的变量副本，所以每一个线程都可以独立地改变自己的副本，而不会影响其它线程所对应的副本。常用于用户登录控制，如记录session信息。

    * 实现：每个Thread都持有一个TreadLocalMap类型的变量（该类是一个轻量级的Map，功能与map一样，区别是桶里放的是entry而不是entry的链表。功能还是一个map。）以本身为key，以目标为value。
    * 主要方法是get()和set(T a)，set之后在map里维护一个threadLocal -> a，get时将a返回。ThreadLocal是一个特殊的容器。
* 原子类
    * 如果使用atomic wrapper class如atomicInteger，或者使用自己保证原子的操作，则等同于synchronized
* Lock类
    * lock: 在java.util.concurrent包内。
    ```
    ReentrantLock//可重入的意义在于持有锁的线程可以继续持有，并且要释放对等的次数后才真正释放该锁。
    ReentrantReadWriteLock.ReadLock
    ReentrantReadWriteLock.WriteLock
    ```
    主要目的是和synchronized一样， 两者都是为了解决同步问题，处理资源争端而产生的技术。功能类似但有一些区别。
    * lock更灵活，可以自由定义多把锁的枷锁解锁顺序（synchronized要按照先加的后解顺序）
    提供多种加锁方案，lock 阻塞式, trylock 无阻塞式, lockInterruptily 可打断式， 还有trylock的带超时时间版本。
    本质上和监视器锁（即synchronized是一样的）能力越大，责任越大，必须控制好加锁和解锁，否则会导致灾难。
    * 和Condition类的结合。
* 容器类
    * BlockingQueue：除了传统的queue功能（表格左边的两列）之外，还提供了阻塞接口put和take，带超时功能的阻塞接口offer和poll。put会在队列满的时候阻塞，直到有空间时被唤醒；take在队　列空的时候阻塞，直到有东西拿的时候才被唤醒。
    * ConcurrentHashMap
# 7.线程池
* 如果在程序中创建了大量生命周期很短的线程，应该使用线程池
* Executor类中提供了几个静态方法用于构建线程池
```
ExecutorService e = Executors.newCachedThreadPool();
ExecutorService e = Executors.newSingleThreadExecutor();
ExecutorService e = Executors.newFixedThreadPool(3);
...
```
* 该类内部是通过ThreadPoolExecutor实现的，掌握该类有助于理解线程池的管理，本质上，他们都是ThreadPoolExecutor类的各种实现版本