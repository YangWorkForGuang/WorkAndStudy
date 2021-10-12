# 参考资料

[云深不知处](https://blog.csdn.net/mu_wind/article/list/1)可以参考这个博主的博客，讲的细致全面。

# 多线程入门

[多线程三分钟就可以入个门了！](https://link.zhihu.com/?target=https%3A//segmentfault.com/a/1190000014428190)

## 进程

进程已经是可以进行资源分配和调度了，**为什么还要线程呢**？

为使程序能并发执行，系统必须进行以下的一系列操作：

- **创建进程**，系统在创建一个进程时，必须为它分配其所必需的、除处理机以外的所有资源，如内存空间、I/O设备，以及建立相应的PCB；
- **撤消进程**，系统在撤消进程时，又必须先对其所占有的资源执行回收操作，然后再撤消PCB；
- **进程切换**，对进程进行上下文切换时，需要保留当前进程的CPU环境，设置新选中进程的CPU环境，因而须花费不少的处理机时间。

引入线程主要是**为了提高系统的执行效率，减少处理机的空转时间和调度切换的时间，以及便于系统管理。**使OS具有更好的并发性。

## 进程与线程

- 进程作为资源**分配**的基本单位
- 线程作为CPU**调度**的基本单位

## Java实现多线程

### 继承Thread，重写run方法

```java
public class MyThread extends Thread {

    @Override
    public void run() {
        for (int x = 0; x < 200; x++) {
            System.out.println(x);
        }
    }

}

public class MyThreadDemo {
    public static void main(String[] args) {
        // 创建两个线程对象
        MyThread my1 = new MyThread();
        MyThread my2 = new MyThread();

        my1.start();
        my2.start();
    }
}
```

### 实现Runnable接口，重写run方法

```java
public class MyRunnable implements Runnable {

    @Override
    public void run() {
        for (int x = 0; x < 100; x++) {
            System.out.println(x);
        }
    }
}

public class MyRunnableDemo {
    public static void main(String[] args) {
        // 创建MyRunnable类的对象
        MyRunnable my = new MyRunnable();

        Thread t1 = new Thread(my);
        Thread t2 = new Thread(my);

        t1.start();
        t2.start();
    }
}
```

### Java实现多线程的细节

- run( )仅仅是封装被线程执行的代码
- start( )首先启动了线程，然后再去由JVM去调用该线程的run( )方法

### JVM虚拟机的启动是多线程的

不仅仅是启动main线程，还至少会启动垃圾回收线程。

# 线程生命周期

## 线程状态

[**Java线程的6种状态及切换(透彻讲解)**](https://blog.csdn.net/pange1991/article/details/53860651)

- 初始状态（NEW）

  - 实现Runnable接口和继承Thread可以得到一个线程类，new一个实例出来，线程就进入了初始状态。

- 就绪状态（READY）

  - 调用线程的start( )方法，线程进入就绪状态。
  - 就绪状态仅为有资格运行，只要调度程序没有选中，就永远是就绪状态。
  - 当前线程sleep()方法执行结束；其他线程join()结束；等待用户输入完毕；某个线程拿到对象锁，这些线程也会进入就绪状态。
  - 当前线程的时间片用完了，调用当前线程的yield()方法，当前线程进入就绪状态。
  - 所持里的线程拿到对象锁后，进入就绪状态。

- 运行中状态（RUNNING）

  线程调度程序从可运行池中选择一个线程**XXXX**，这也是线程进入运行状态的唯一方式。

- 阻塞状态（BLOCKED）

  - 线程阻塞于锁
  - 阻塞状态时阻塞在进入synchronized关键字修饰的方法或者代码块（**获取锁？？**）时的状态。

- 等待（WAITING）

  处于这种状态的线程不会被分配CPU执行时间，它们要等待被显式地唤醒，否则会处于无限期等待的状态

- 超时等待（TIMED_WAITING）

  处于这种状态的线程不会被分配CPU执行时间，不过无须无限期等待被其他线程显示地唤醒，在达到一定时间后它们会自动唤醒。

- 终止状态（TERMINATED）

  - 当线程的run()方法完成时，或者主线程的main()方法完成时，我们就认为它终止了
  - 这个线程对象也许是活的，但是它已经不是一个单独执行的线程
  - 线程一旦终止了，就不能复生。

## 线程方法比较

[**Java线程的6种状态及切换(透彻讲解)**](https://blog.csdn.net/pange1991/article/details/53860651)

- Thread：线程类方法
- thread：线程实例方法
- obj：对象方法

### Thread.sleep(long millis)

- 必须是当前线程调用此方法，当前线程进入TIMED_WAITING状态，但不释放对象锁，millis时间后线程自动苏醒进入就绪状态。
- 作用：给其它线程执行机会的最佳方式。

### Thread.yield()

- 必须是当前线程调用此方法，当前线程放弃获取的CPU时间片，但不释放锁资源，由运行状态变为就绪状态，让OS再次选择线程。
- 作用：让相同优先级的线程轮流执行，但并不保证一定会轮流执行。
- 实际中无法保证yield()达到让步目的，因为让步的线程还有可能被线程调度程序再次选中。
- Thread.yield()不会导致阻塞。该方法与sleep()类似，只是不能由用户指定暂停多长时间。

### thread.join()/thread.join(long millis)

- 当前线程里调用其它线程t的join方法，当前线程进入WAITING/TIMED_WAITING状态，**当前线程不会释放已经持有的对象锁**。
- 线程t执行完毕或者millis时间到，当前线程一般情况下进入RUNNABLE状态，也有可能进入BLOCKED状态（因为join是基于wait实现的）

### obj.wait()

- 当前线程调用对象的wait()方法，当前线程**释放对象锁**，进入等待队列。依靠notify()/notifyAll()唤醒或者wait(long timeout) timeout时间到自动唤醒。

### obj.notify()

- 唤醒在此对象监视器上等待的单个线程，选择是任意性的。notifyAll()唤醒在此对象监视器上等待的所有线程。

### LockSupport.park()/LockSupport.parkNanos(long nanos),LockSupport.parkUntil(long deadlines)

当前线程进入WAITING/TIMED_WAITING状态。对比wait方法,不需要获得锁就可以让线程进入WAITING/TIMED_WAITING状态，需要通过LockSupport.unpark(Thread thread)唤醒。

## 等待队列与同步队列

[**Java线程的6种状态及切换(透彻讲解)**](https://blog.csdn.net/pange1991/article/details/53860651)

![](https://img-blog.csdn.net/20180701221233161?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3BhbmdlMTk5MQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

- 线程1获取对象A的锁，正在使用对象A。
- 线程1调用对象A的wait()方法。
- 线程1释放对象A的锁，并马上进入等待队列。
- 锁池（同步队列）里面的对象争抢对象A的锁。
- 线程5获得对象A的锁，进入synchronized块，使用对象A。
- 线程5调用对象A的notifyAll()方法，唤醒所有线程，所有线程进入同步队列。
- 若线程5调用对象A的notify()方法，则唤醒一个线程，不知道会唤醒谁，被唤醒的那个线程进入同步队列。
- notifyAll()方法所在synchronized结束，线程5释放对象A的锁。
- 同步队列的线程争抢对象锁，但线程1什么时候能抢到就不知道了。 

**PS**

- 线程等待时间到了或被notify/notifyAll唤醒后，会进入同步队列竞争锁，如果获得锁，进入RUNNABLE状态，否则进入BLOCKED状态等待获取锁。
- 同步队列是在同步的环境下才有的概念，一个对象对应一个同步队列。

## Condition

参考：[java condition使用及分析](https://blog.csdn.net/bohu83/article/details/51098106?utm_medium=distribute.pc_relevant.none-task-blog-2%7Edefault%7EBlogCommendFromMachineLearnPai2%7Edefault-2.control&dist_request_id=1328679.10635.16161473830194159&depth_1-utm_source=distribute.pc_relevant.none-task-blog-2%7Edefault%7EBlogCommendFromMachineLearnPai2%7Edefault-2.control)

Condition是在java 1.5中才出现的，它用来替代传统的Object的wait()、notify()实现线程间的协作，相比使用Object的wait()、notify()，**使用Condition的await()、signal()这种方式实现线程间协作更加安全和高效**。

- Condition是个接口，基本的方法就是await()和signal()方法；
  - Conditon中的await()对应Object的wait()；
  - Condition中的signal()对应Object的notify()；
- Condition依赖于Lock接口，生成一个Condition的基本代码是lock.newCondition() 
- 调用Condition的await()和signal()方法，都必须在lock保护之内，就是说必须在lock.lock()和lock.unlock之间才可以使用。

condition可以通俗的理解为条件队列。当一个线程在调用了await方法以后，直到线程等待的某个条件为真的时候才会被唤醒。这种方式为线程提供了更加简单的等待/通知模式。Condition必须要配合锁一起使用，因为对共享状态变量的访问发生在多线程环境下。一个Condition的实例必须与一个Lock绑定，因此Condition一般都是作为Lock的内部实现。

### Condition实现简单的阻塞队列

```java
package condition;

import java.util.concurrent.locks.Condition;
import java.util.concurrent.locks.Lock;
import java.util.concurrent.locks.ReentrantLock;

/**
 * @author Yg
 * @date 2021/10/11
 */
public class ConTest {

    final Lock lock = new ReentrantLock();
    final Condition condition = lock.newCondition();

    public static void main(String[] args) {
        // TODO Auto-generated method stub
        ConTest test = new ConTest();
        Producer producer = test.new Producer();
        Consumer consumer = test.new Consumer();

        consumer.start();
        producer.start();
    }

    class Consumer extends Thread{

        @Override
        public void run() {
            consume();
        }

        private void consume() {
            try {
                lock.lock();
                System.out.println("我在等一个新信号"+this.currentThread().getName());
                condition.await();

            } catch (InterruptedException e) {
                // TODO Auto-generated catch block
                e.printStackTrace();
            } finally{
                System.out.println("拿到一个信号"+this.currentThread().getName());
                lock.unlock();
            }
        }
    }

    class Producer extends Thread{

        @Override
        public void run() {
            produce();
        }

        private void produce() {
            try {
                lock.lock();
                System.out.println("我拿到锁"+this.currentThread().getName());
                condition.signalAll();
                System.out.println("我发出了一个信号："+this.currentThread().getName());
            } finally{
                lock.unlock();
            }
        }
    }
}
```

### Condition实现生产者、消费者模式

```java
package condition;

import java.util.PriorityQueue;
import java.util.concurrent.locks.Condition;
import java.util.concurrent.locks.Lock;
import java.util.concurrent.locks.ReentrantLock;

/**
 *
 * @params 杨光
 * @return 2021-10-11
 */
public class ConTest2 {
    private int queueSize = 10;
    private PriorityQueue<Integer> queue = new PriorityQueue<Integer>(queueSize);
    private Lock lock = new ReentrantLock();
    private Condition notFull = lock.newCondition();
    private Condition notEmpty = lock.newCondition();

    public static void main(String[] args) throws InterruptedException  {
        ConTest2 test = new ConTest2();
        Producer producer = test.new Producer();
        Consumer consumer = test.new Consumer();
        producer.start();
        consumer.start();
        Thread.sleep(0);
        producer.interrupt();
        consumer.interrupt();
    }

    class Consumer extends Thread{
        @Override
        public void run() {
            consume();
        }
        volatile boolean flag=true;
        private void consume() {
            while(flag){
                lock.lock();
                try {
                    while(queue.isEmpty()){
                        try {
                            System.out.println("队列空，等待数据");
                            notEmpty.await();
                        } catch (InterruptedException e) {
                            flag =false;
                        }
                    }
                    queue.poll();                //每次移走队首元素
                    notFull.signal();
                    System.out.println("从队列取走一个元素，队列剩余"+queue.size()+"个元素");
                } finally{
                    lock.unlock();
                }
            }
        }
    }

    class Producer extends Thread{
        @Override
        public void run() {
            produce();
        }
        volatile boolean flag=true;
        private void produce() {
            while(flag){
                lock.lock();
                try {
                    while(queue.size() == queueSize){
                        try {
                            System.out.println("队列满，等待有空余空间");
                            notFull.await();
                        } catch (InterruptedException e) {
                            flag =false;
                        }
                    }
                    queue.offer(1);        //每次插入一个元素
                    notEmpty.signal();
                    System.out.println("向队列取中插入一个元素，队列剩余空间："+(queueSize-queue.size()));
                } finally{
                    lock.unlock();
                }
            }
        }
    }
}

```



# JAVA锁机制

## Synchronized关键字

### synchronized锁是什么？

- synchronized是Java的一个**关键字**，它能够将**代码块(方法)锁起来**

- 它使用起来是非常简单的，只要在代码块(方法)添加关键字synchronized，即可以**实现同步**的功能~

- synchronized是一种互斥锁，一次只允许一个线程进入被锁住的代码块。

- synchronized是一种内置锁/监视器锁。Java中每个对象都要一个内置锁（监视器，也可以理解为锁标记），而synchronized就是使用对象的内置锁（监视器）来将代码块（方法）锁定的。

  

  ```java
  public synchronized void test() {
      // 关注公众号Java3y
      // doSomething
  }
  ```

### synchronized用处是什么？

- 保证了线程的原子性。被保护的代码是一次被执行的，没有其他线程会同时访问。
- 保证了可见性。当执行完synchronized之后，修改后的变量对其他线程是可见的。

Java中的synchronized，通过使用内置锁，来实现对变量的同步操作，进而实现了**对变量操作的原子性和其他线程对变量的可见性**，从而确保了并发情况下的线程安全。

### synchronized如何使用

- 修饰普通方法（对象锁）
- 修饰代码块（对象锁）
- 修饰静态方法（类锁）

其中获取了类锁的线程与获取了对象锁的线程是不冲突的。

### [可重入性](https://blog.csdn.net/u011068702/article/details/103813368)

java中synchronized是基于原子性的内部锁机制，是可重入的，因此在一个线程调用synchronized方法的同时在其方法体内部调用该对象另一个synchronized方法.

- 也就是说一个线程得到一个对象锁后再次请求该对象锁，是允许的。
- 还有就是当子类继承父类时，子类也是可以通过可重入锁调用父类的同步方法，这就是synchronized的可重入性。

```java
public class Widget {

    // 锁住了
    public synchronized void doSomething() {
        ...
    }
}

public class LoggingWidget extends Widget {

    // 锁住了
    public synchronized void doSomething() {
        System.out.println(toString() + ": calling doSomething");
        super.doSomething();
    }
}
```

### 释放锁的时机

- 方法（代码块）执行结束自动释放锁
- 当一个线程执行的代码出现异常时，其持有的锁会自动释放。

## Lock

- 允许多个读线程同时访问贡献资源
- Lock显式锁可以给我们的带来很好的灵活性，但同时我们**必须手动释放锁**。

以下未理解

- Lock方式来获取锁**支持中断、超时不获取、是非阻塞的**
- **提高了语义化**，哪里加锁，哪里解锁都得写出来
- 支持Condition条件对象

### lock()、trylock()、lockInterruptibly()三种方法比较

- lock：调用后一直阻塞到获得锁

- tryLock：尝试是否能获得锁 会立即返回获取结果

- lockInterruptibly：调用后一直阻塞到获得锁 但是接受中断信号（例如Thread sleep）

# 线程池

参考资料：[深入Java线程池：从设计思想到源码解读](https://blog.csdn.net/mu_wind/article/details/113806680)

> 可以反复的去看，将的非常的全面、细致。
>
> 目前解读线程池部分的几个方法没有去看。

## 线程池

线程的创建和销毁都需要映射到操作系统，因此其代价是比较高昂的。出于避免频繁创建、销毁线程以及方便线程管理的需要，线程池应运而生。

**线程池优势**

- 降低资源消耗
- 提高响应速度
- 提高线程的可管理性



