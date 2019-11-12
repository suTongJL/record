#### 原理

*   只有在运行的线程数小于核心线程数时，创建核心线程，执行任务；
*   当达到最大核心线程数，但是队列未满的情况，则往队列中添加，等待有空闲的核心线程之后，从队列中取出任务执行；
*   当队列也满了之后，并且在运行的线程未达到最大线程数，再创建普通线程（在任务完成一段时间内没有任务复用该线程，就会被销毁），执行线程；
*   在执行的线程数也达到最大值后，再继续有新任务执行，则执行拒绝策略；

#### Executors 创建四种线程池

*   newCachedThreadPool创建一个可缓存线程池，如果线程池长度超过处理需要，可灵活回收空闲线程，若无可回收，则新建线程。
*   newFixedThreadPool 创建一个定长线程池，可控制线程最大并发数，超出的线程会在队列中等待。
*   newScheduledThreadPool 创建一个定长线程池，支持定时及周期性任务执行。
*   newSingleThreadExecutor 创建一个单线程化的线程池，它只会用唯一的工作线程来执行任务，保证所有任务按照指定顺序(FIFO, LIFO, 优先级)执行。

#### ThreadPoolExecutor

```java
ThreadPoolExecutor(int corePoolSize,
                              int maximumPoolSize,
                              long keepAliveTime,
                              TimeUnit unit,
                              BlockingQueue<Runnable> workQueue,
                              ThreadFactory threadFactory,
                              RejectedExecutionHandler handler)
```
下面对参数进行说明：

1.  corePoolSize：表示核心线程池的大小。当提交一个任务时，如果当前核心线程池的线程个数没有达到 corePoolSize，则会创建新的线程来执行所提交的任务，**即使当前核心线程池有空闲的线程**。如果当前核心线程池的线程个数已经达到了 corePoolSize，则不再重新创建线程。如果调用了`prestartCoreThread()`或者 `prestartAllCoreThreads()`，线程池创建的时候所有的核心线程都会被创建并且启动。
2.  maximumPoolSize：表示线程池能创建线程的最大个数。如果当阻塞队列已满时，并且当前线程池线程个数没有超过 maximumPoolSize 的话，就会创建新的线程来执行任务。
3.  keepAliveTime：空闲线程存活时间。如果当前线程池的线程个数已经超过了 corePoolSize，并且线程空闲时间超过了 keepAliveTime 的话，就会将这些空闲线程销毁，这样可以尽可能降低系统资源消耗。
4.  unit：时间单位。为 keepAliveTime 指定时间单位。
5.  workQueue：阻塞队列。用于保存任务的阻塞队列，关于阻塞队列[可以看这篇文章](https://www.jianshu.com/p/c422ed5ea9ce)。可以使用 **ArrayBlockingQueue, LinkedBlockingQueue, SynchronousQueue, PriorityBlockingQueue**。
6.  threadFactory：创建线程的工程类。可以通过指定线程工厂为每个创建出来的线程设置更有意义的名字，如果出现并发问题，也方便查找问题原因。
7.  handler：饱和策略。当线程池的阻塞队列已满和指定的线程都已经开启，说明当前线程池已经处于饱和状态了，那么就需要采用一种策略来处理这种情况。


#### 四个队列区别

1. ArrayBlockingQueue
2. LinkedBlockingQueue
3. SynchronousQueue
4. PriorityBlockingQueu

#### 四种策略的区别

1.  AbortPolicy： 直接拒绝所提交的任务，并抛出 **RejectedExecutionException** 异常；
2.  CallerRunsPolicy：只用调用者所在的线程来执行任务；
3.  DiscardPolicy：不处理直接丢弃掉任务；
4.  DiscardOldestPolicy：丢弃掉阻塞队列中存放时间最久的任务，执行当前任务
