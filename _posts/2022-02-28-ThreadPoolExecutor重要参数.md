# ThreadPoolExecutor重要参数

ThreadPoolExecutor有几个重要的成员变量：`keepAliveTime`、`allowCoreThreadTimeOut`、`poolSize`、`corePoolSize`、`maximumPoolSize`。下面分别介绍一下：

- `corePoolSize`：[线程池](https://so.csdn.net/so/search?q=线程池&spm=1001.2101.3001.7020)的基本大小。下面会解释什么是基本大小。

- `maximumPoolSize`：线程池中允许的最大线程数。

  注意还有一个`largestPoolSize`，记录了曾经出现的最大线程个数。因为`setMaximumPoolSize()`可以改变最大线程数。

- `poolSize`：线程池中当前线程的数量。

- allowCoreThreadTimeOut：是否允许核心线程超时退出。

  如果该值为false，且`poolSize<=corePoolSize`，线程池都会保证这些核心线程处于存活状态，不会超时退出。

  如果为true，则不论poolSize的大小，都允许超时退出。

  如果`poolSize>corePoolSize`，则该参数不论true还是false，都允许超时退出。

# poolSize、corePoolSize、maximumPoolSize

那么`poolSize`、`corePoolSize`、`maximumPoolSize`三者的关系是如何的呢？

当新提交一个任务时：

1. 如果`poolSize<corePoolSize`，新增加一个线程处理新的任务。
2. 如果`poolSize=corePoolSize`，新任务会被放入阻塞队列等待。
3. 如果阻塞队列的容量达到上限，且这时`poolSize<maximumPoolSize`，新增线程来处理任务。
4. 如果阻塞队列满了，且`poolSize=maximumPoolSize`，那么线程池已经达到极限，会根据饱和策略RejectedExecutionHandler拒绝新的任务。

所以通过上面的描述可知`corePoolSize<=maximumPoolSize`，`poolSize<=maximumPoolSize`；而`poolSize`和`corePoolSize`无法比较，`poolSize`是有可能比`corePoolSize大`的。

# 四种线程池

Executors提供四种线程池：

- newCachedThreadPool ：缓存线程池，如果线程池长度超过处理需要，可回收空闲线程，若无可回收，则新建线程。
- newFixedThreadPool ： 定长线程池，可控制线程最大并发数，超出的线程会在队列中等待。
- newScheduledThreadPool ： 计划线程池，支持定时及周期性任务执行。
- newSingleThreadExecutor ：单线程线程池，用唯一的线程来执行任务，保证所有任务按照指定顺序(FIFO, LIFO, 优先级)执行。