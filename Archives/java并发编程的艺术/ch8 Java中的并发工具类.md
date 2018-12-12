# Java中的并发工具类

## 等待多线程完成的CountDownLatch

CountDownLatch允许一个或多个线程等待其他线程完成操作

CountDownLatch的构造函数结构一个int类型的参数作为计数器,如果想等待N个点完成,这里传入N.

当调用CountDownLatch的`countDown`方法时,N就会减一,CountDownLatch的await方法会阻塞当前线程,直到N变为零. 由于`countDown`方法可以在任何地方使用,所以这里说的N个点可以是N个线程,也可以是1个线程里的N个执行步骤.用在多个线程时,只需要把CountDownLatch的引用传递到线程里即可

> 计数器必须大于0,只有等于0的时候,计数器就是零,调用await方法时不会阻塞当前线程.CountDownLatch不可能重新初始化或修改CountDownLatch对象内部计数器的值.一个线程调用`countDown`happen-before另一个线程调用await方法

## 同步屏障 CyclicBarrier

CyclicBarrier字面意思可以是可循环使用的屏障.主要做的事情是,让一组线程达到一个屏障(也可以叫同步点)时被阻塞,直到最后一个线程到达屏障,屏障才会开门,所有被屏障拦截的线程才会继续运行

### CyclicBarrier简介

### CyclicBarrier的应用场景

CyclicBarrier可用于多线程计算数据,最后合并计算结果的场景.

## 控制并发线程数的Semaphore

Semaphore(信号量)时用来控制同时访问特定资源的线程数量,他通过协调各个线程,以保证合理的使用公共资源

1. 应用场景

Semaphore可以用于流量控制,特别是公共资源有限的应用场景,比如数据库连接.

## 线程间交换数据的Exchanger

Exchanger(交换者)是一个用于线程间协作的工具类.Exchanger用于进行线程间的数据交换.它是一个同步点,在这个同步点,两个线程间可以彼此交换数据.这两个线程通过exchange方法交换数据,如果第一个线程先执行`exchange()`方法,它会一直等待第二个线程也执行exchange方法,当两个线程都达到同步点,这两个线程就可以交换数据了,将本线程生产出来的数据传递给对方

