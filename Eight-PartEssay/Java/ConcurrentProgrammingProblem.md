# 多线程
## Java的内存模型(JMM)
JMM是专门解决多线程并发问题的一套规则。简单说，就是规定了多线程环境下，线程怎么访问共享变量才能不出错，核心是处理可见性、原子性、有序性这三个问题。

JMM的核心思路是：定义主内存(大家共享的内存)和工作内存(每个线程自己的缓存)，规定变量必须从主内存加载到工作内存才能操作，改完再写回主内存。

然后通过volatile、synchronized这些关键字，控制加载、写回的时机，以及禁止不合理的指令重排，最终保证多线程操作共享变量时能正确交互。



## Java多线程是什么？需要注意什么？
Java多线程是指在一个Java程序中同时运行多个线程，这些线程共享程序的内存空间(如全局变量、方法区等)，但有各自的栈和程序计数器，能同时执行不同的任务，比如一个线程处理用户输入，另一个线程后台下载文件，提升程序效率。

使用Java多线程需要注意一下几点：

+ 首先是线程安全问题。多个线程同时操作共享数据时，可能出现错误。比如两个线程同时给一个变量加1，原本该加2，结果可能只加了1，这是因为线程切换时没做好数据保护。需要synchronized关键字、Lock锁等方式，保证同一时间只有一个线程操作共享数据
+ 其次是线程间通信。线程需要协作时，比如一个线程产生数据，另一个线程消费数据，要通过wait()、notify()等方法控制，避免出现一方没准备好，另一方就操作的情况，否则可能导致数据错误或线程无线等待。
+ 然后是线程的创建和销毁成本。频繁创建和销毁线程会消耗系统资源，影响性能。可以用线程驰管理线程，提前创建好一定数量的线程，重复使用，减少资源消耗。



## Java里面的线程和操作系统的流程一样吗？
Java底层会调用pthread_create来创建线程，所以本质上Java程序创建的线程，就是和操作系统线程是一样的，是1对1的线程模型。



## 使用多线程需要注意哪些问题？
要保证多线程的程序是安全的，不要出现数据竞争造成的数据混乱的问题。

Java的线程安全体现在一下三个方面：

+ 原子性：互斥访问，即同一时刻只能有一个线程对数据进行操作，在Java中使用了atomic包和synchronized关键字来确保原子性；
+ 可见性：一个线程对主内存的修改可以及时地被其他线程看到，在Java中使用了synchronized和volatile这两个关键字确保可见性
+ 有序性：一个线程观察其他线程中的指令顺序，由于指令重排序，该观察结果一般杂乱无序，在Java中使用了happens-before原则来确保有序性



## 保证数据的一致性有哪些方案呢？
+ 事务管理管理：使用数据库事务来确保一组数据库操作要么全部成功提交，要么全部失败回滚。通过ACID(原子性、一致性、隔离性、持久性)属性，数据库事务可以保证数据的一致性。
+ 锁机制：使用锁来实现对共享资源的互斥访问。在Java中，可以使用synchronized关键字、ReentrantLock或其他锁机制来控制并发访问，从而避免并发操作导致数据不一致。
+ 版本控制：通过乐观锁的方式，在更新数据时记录数据的版本信息，从而避免同时对同一数据进行修改，进而保证数据的一致性。



## 线程的创建方式有哪些？
1. 继承Thread类：用户自定义类继承java.lang.Thread类，重写其run()方法，run()方法中定义了线程执行的具体任务。创建给类的实例后，通过调用start()方法启动线程。
    1. 优点：编写简单，可直接使用this获得当前线程，无需使用Thread.currenttThread()方法。
    2. 缺点：因为线程类已经继承了Thread类所以不能再继承其他的父类
2. 实现Runnable接口：类可以通过实现java.lang.Runnable接口。而实现Runnable接口需要重写run()方法，然后将此Runnable对象作为参数传递给Thread类的构造器，创建Thread对象后调用其start()方法启动线程。
    1. 优点：线程类只是实现了Runnable接口，还可以继承其他的类。在这种方式下，可以多个线程共享同一个目标对象，所以非常适合多个相同线程来处理同一份资源的情况，从而可以将CPU代码和数据分开，形成清晰的模型，较好地体现了面向对象的思想。
    2. 缺点：编程稍微复杂，如果需要访问当前线程，必须使用Thread.currentThread()方法。
3. 实现Callable接口与FutureTask：java.util.concurrent.Callable接口类似于Runnable，但Callable的call()方法可以有返回值并可以抛出异常。要执行Callable任务，需将它包装进一个FutureTask，因为Thread类的构造器只接受Runnable参数，而FutureTask实现了Runnable接口。
    1. 缺点：编程稍微复杂，如果需要访问当前线程，必须调用Thread.currentThread()方法。
    2. 优点：线程只是实现Runnable或实现Callable接口，还可以继承其他类。这种方式下，多个线程可以共享一个target对象，非常适合多线程处理同一份资源的情形。
4. 使用线程池(Executor框架)：从Java5开始引入的java.util.concurrrent.ExecutorService和相关类提供了线程池的支持，这是一种更高效的线程管理方式，避免了频繁创建和销毁线程的开销。可以通过Executors类的静态方法创建不同类型的线程池。
    1. 缺点：线程池增加了程序的复杂度，特别是当涉及线程池参数调整和故障排查时。错误的配置可能导致死锁、资源耗尽等问题，这些问题的诊断和修复可能较为复杂
    2. 优点：线程池可以重用预先创建的线程，避免了线程创建和销毁的开销，显著提高了程序的性能。对于需要快速响应的并发请求，线程池可以迅速提供线程来处理任务，减少等待时间。并且，线程池能够有效控制运行的线程数量，防止因创建过多线程导致的系统资源耗尽(如内存溢出)。通过合理配置线程池大小，可以最大化CPU利用率和系统吞吐量。



## 怎么启动线程？
启动线程，通过Tread类的Start()



## 如何停止一个线程的运行
主要有这些方法：

+ 异常法停止：线程调用interrupt()方法后，在线程的run方法中判断当前对象的interrupt()状态，如果是中断状态则抛出异常，达到中断线程的效果。
+ 在沉睡中停止：现将线程sleep，然后调用interrupt标记中断状态，interrupt会将阻塞状态的线程中断。会抛出中断异常，达到停止线程的效果。
+ stop()暴力停止：线程调用stop()方法会被暴力停止，方法已弃用，该方法会有不好的后果；强制让线程停止有可能使一些请理性的工作得不到完成。
+ 使用return停止线程：调用interrupt标记为中断状态后，在run方法中判断当前线程状态，如果为中断状态则return，能达到停止线程的效果。



## 调用interrupt是如何让线程抛出异常的？
每个线程都有一个与之关联的布尔属性来表示其中断状态，中断状态的初始值为false，当一个线程被其他线程调用Thread.interrupt()方法中断时，会根据实际情况做出响应。

+ 如果该线程正在执行低级别的可中断方法(如Thread.sleep()、Thread.jion()或Object.wait())，则会解除阻塞并抛出InterruptedException异常。
+ 否则Thread.interrupt()仅设置线程的中断状态，在该被中断的线程中稍后可通过轮询中断状态来决定是否要停止当前正在执行的任务。



## Java线程的状态有哪些
| 线程状态 | 解释 |
| --- | --- |
| NEW | 尚未启动的线程状态，即线程创建，还未调用start方法 |
| RUNNABLE | 就绪状态（调用start，等待调度）+正在运行 |
| BLOCKED | 等待监视器锁时，陷入阻塞状态 |
| WAITING | 等待状态的线程正在等待另一线程执行特定的操作（如notify） |
| TIMED_WAITING | 具有指定等待时间的等待状态 |
| TERMINATED | 线程完成执行，终止状态 |




## sleep和wait的区别
对比列表

|  特性 | sleep()  | wait() |
| --- | --- | --- |
| 所属类 | Thread类(静态方法) | Object类(实例方法) |
| 锁释放 | ❌ | ✅ |
| 使用前提 | 任意位置调用 | 必须在同步块内(持有锁) |
| 唤醒机制 | 超时自动恢复 | 需要notify()/notifyAll()或超时 |
| 设计用途 | 暂时线程执行，不涉及锁协作 | 线程间协调，释放锁让其他线程工作 |


+ 所属分类的不同：sleep是Thread类的静态方法，可以在任何地方直接通过Thread.sleep()调用，无需依赖对象实例。wait是Object类的实例方法，这意味着必须通过对象实例来调用。
+ 锁释放的情况：Thread.sleep()在调用时，线程会暂停执行指定的时间，但不会释放持有的对象锁。也就是说，在sleep期间，其他线程无法获得该线程持有的锁。Object.wait()：调用该方法时，线程会释放持有的对象锁，进入等待状态，直到其他线程调用相同对象的notify()或notifyAll()方法唤醒它。
+ 使用条件：sleep可在任意位置调用，无需事先获取锁。wait必须在同步块或同步方法内调用(即线程需持有该对象的锁)，否则抛出IllegalMonitorStateException。
+ 唤醒机制：sleep休眠时间结束后，线程自动恢复到就绪状态，等待CPU调度。wait需要其他线程调用相同对象的notify()或notifyAll()方法才能被唤醒。notify()会随机唤醒一个在该对象上等待的线程，而notifyAll()会唤醒所有在该对象上等待的线程.



## sleep会释放cpu吗？
是的，调用Thread.sleep()时，线程会释放CPU，但不会释放持有的锁。

当线程调用sleep()后，会主动让出CPU时间片，进入TIMED_WAITING状态，此时操作系统会触发调度，将CPU分配给其他出于就绪状态的线程。这样其他线程(无论是需要同一锁的线程还是不相关线程)便有机会执行。

sleep()不会释放线程已持有的任何锁(如synchronized同步代码块或方法中获取的锁)。因此，如果有其他线程试图获取一把锁，它们仍会被阻塞，直到原线程退出同步代码块。



## blocked和waiting的区别
区别如下:

+ 触发条件：线程进入BLOCKED状态通常是因为试图获取一个对象的锁(monitor lock)，但该锁已经被另外一个线程持有。这通常发生在尝试进入synchronized块或方法时，如果锁已被占用，则线程将被阻塞直到锁可用。线程进入WAITING状态是因为它正在等待另一个线程执行某些操作，例如调用Object.wait()方法、Thread0jion()方法或者LockSupport.park()方法。在这种状态下，线程将不会消耗CPU资源，并且不会参与锁的竞争。
+ 唤醒机制：当一个线程被阻塞等待锁时，一旦锁被释放，线程将有机会重新尝试获取锁。如果锁此时未被其他线程获取，那么线程可以从BLOCKED状态变为RUNNABLE状态。线程在WAITING状态中需要被显示唤醒。例如，如果线程调用了Object.wait()，那么它必须等待另一个线程调用同一对象上的Object.notify()或Object.notifyAll()方法才能被唤醒。

故它们最大的区别有两个：

+ BLOCKED是锁竞争失败后被动触发的状态，WAITING是人为的主动触发的状态
+ BLOCKED的唤醒时自动触发的，而WAITTING状态是必须要通过特定的方法来主动唤醒



## wait状态下的线程如何进行恢复到running状态？
线程从等待(WAIT)状态恢复到运行(RUNNING)状态的核心机制是通过外部事件触发或资源可用性变化，比如等待的线程被其他线程对象唤醒，notify()和notifyAll()。



## notify和notifyAll的区别？
同样是唤醒等待的线程，同样最多只有一个线程能获得锁，同样不能控制那个线程获得锁。

区别在于：

+ 唤醒一个线程，其他线程依然出于wait的等待状态，如果被唤醒的线程结束时没调用notify，其他线程就永远没人去唤醒，只能等待超时，或者被中断。
+ notifyAll：所有线程退出wait的状态，开始竞争锁，但只有一个线程能抢到，这个线程执行完后，其他线程又会有一个幸运儿脱颖而出得到锁。



## notify选择那个线程
notify在源码的注释中说到notify选择唤醒的线程是任意的，但是依赖于具体实现的jvm。

JVM有很多实现，比较流行的就是hotspot，hotspot对notofy()的实现并不是我们以为的随机唤醒，而是"先进先出"的顺序唤醒。



## 不同的线程之间如何通信？
1. **<font style="color:rgb(15, 17, 21);">共享内存</font>**<font style="color:rgb(15, 17, 21);">：线程直接读写共享变量（需配合同步机制）。</font>
2. **<font style="color:rgb(15, 17, 21);">锁/互斥量</font>**<font style="color:rgb(15, 17, 21);">：防止数据竞争，确保线程互斥访问共享资源。</font>
3. **<font style="color:rgb(15, 17, 21);">条件变量</font>**<font style="color:rgb(15, 17, 21);">：让线程等待特定条件成立，常用于生产者-消费者模型。</font>
4. **<font style="color:rgb(15, 17, 21);">信号量</font>**<font style="color:rgb(15, 17, 21);">：控制同时访问资源的线程数量。</font>
5. **<font style="color:rgb(15, 17, 21);">消息队列/管道</font>**<font style="color:rgb(15, 17, 21);">：在线程间传递数据，解耦发送方和接收方。</font>
6. **<font style="color:rgb(15, 17, 21);">原子操作</font>**<font style="color:rgb(15, 17, 21);">：直接执行不可中断的读写，避免简单数据竞争。</font>

<font style="color:rgb(15, 17, 21);">关键点：</font>**<font style="color:rgb(15, 17, 21);">必须使用同步机制确保线程安全</font>**<font style="color:rgb(15, 17, 21);">，避免数据竞争和死锁。</font>



## 线程间通信方式有哪些？
1. Object类的wait()、notify()和notifyAll()方法
2. Lock和Condition接口。代码实现：

```plain
import java.util.concurrent.locks.Condition;
import java.util.concurrent.locks.Lock;
import java.util.concurrent.locks.ReentrantLock;

class SharedResource {
    private final Lock lock = new ReentrantLock();
    private final Condition condition = lock.newCondition();

    public void consumer() throws InterruptedException {
        lock
.lock();
        try {
            while (/* 条件不满足 */) {
                condition
.await();
            }
            // 执行相应操作
        } finally {
            lock
.unlock();
        }
    }

    public void producer() {
        lock
.lock();
        try {
            // 执行相应操作
            condition
.signal(); // 或者 signalAll()
        } finally {
            lock
.unlock();
        }
    }
}
```

3. volatile关键字。
4. CountDownLatch。CountDownLatch是一个同步辅助类，它运行一个或多个线程等待其他线程完成操作。
5. CyclicBarrier。CyclicBarrier是一个同步辅助类，它允许一组线程相互等待，直到所有线程都到达某个公共屏障点
6. Semaphore。Semaphore是一个计数信号量，它可以控制同时访问特定资源的线程数量。



## 如何停止一个线程？
在Java中，停止线程的正确方式是通过协作式的逻辑控制线程终止，而非强制暴力终止（如已废弃的Thread.stop方法）。以下是实现安全停止线程的多种方法：

1. 通过共享标志位主动终止：定义一个可见的状态变量，有主线程控制其值，工作线程循环检测该变量以决定是否退出
2. 使用线程中断机制。通过使用Thread.Interrupt()触发线程中断状态，结合中断检测逻辑实现安全停止。
3. 通过Future取消任务。使用线程池提交任务，并通过Future.cancel()停止线程，依赖中断机制
4. 处理不可中断的阻塞操作。某些I/O或同步操作(如Socket.accept()、Lock.lock())无法通过中断直接响应。此时需结合资源关闭操作。比如，释放Socket释放阻塞。



## GO的协程和Java的线程有啥区别？
1. 首先最核心的区别是他们的调度模型不同：
    1. Java的线程是操作系统级别的线程，也就是内核线程，一个Java线程对应一个操作系统线程，它的创建、销毁、调度都是由操作系统内核来管理的。
    2. GO的协程，也就是goroutine，它是用户态的轻量级线程，是由Go运行时自己调度的，不需要经过操作系统内核。这就导致了很多后续的差异。
2. 从资源消耗上来说，差别非常明显：
    1. Java创建一个线程的开销是比较大的，默认情况下一个线程的栈空间大概是1MB左右，而且线程的创建和销毁都需要系统调用，开销不小。所以在Java里，不能无限制地创建线程，创建几千个线程系统可能就扛不住了，这也就是为什么Java里要用线程池来复用线程。
    2. Go的协程就轻量多了，一个goroutine初始化栈空间只有2KB，而且栈空间是可以动态伸缩的。Go里创建几万甚至几十万个协程都是很正常的事情。
3. 调度方式上也不太一样。
    1. Java线程的调度是抢占式的，由操作系统的调度器决定什么时候切换线程，线程切换需要保存和回复上下文，涉及到用户态和内核态的切换，这个开销比较大。
    2. Go的协程调度是在用户态完成的，Go运行时有个GMP模型，G就是goroutine，M是操作系统线程，P是逻辑处理器。Go会把多个协程映射到少量的操作系统线程上执行，协程的切换完全在用户态，不需要陷入内核，所以切换成本非常低。而Go的调度器会在协程发生阻塞的时候，比如IO操作或者channel操作时，自动把这个协程挂起，让其他协程继续执行，这种协作式调度非常高效。
4. 从使用方式上来讲，Go的协程使用起来十分简单。
    1. 在Go里创建一个协程只需要在函数调用前加个go关键字就行了，比如go doSomething()。
    2. 在Java里创建线程，要么需要new一个Thread对象，要么实现Runnable接口，或者用线程池，总之代码量要多一些。
5. 通信机制也有很大差异。
    1. Java线程之间通信主要靠共享内存，需要用synchronized、Lock这些同步机制来保证线程安全，还要小心处理死锁、竞态条件这些问题，写起来比较容易出错
    2. Go虽然也支持共享内存和锁，但它更提倡用channel来通信，有句话叫"不要通过共享内存来通信，而要通过通信来共享内存"。用channel的话，代码更清晰，也不容易出现并发问题。
6. 性能上来说，因为Go协程的轻量级特性和高效的调度器，在高并发场景下，Go的表现通常会更好。比如说要处理几万个并发请求，Java可能需要用复杂的异步框架或者线程池来优化，而Go直接为每个请求开一个协程就行了，代码简单性能还好。Java在企业级应用、生态完善性这些方面还是有很大优势的，但在并发模型的设计上，Go确实更现代化一些。



# 并发安全
## juc包下常用的类
线程池相关：

+ ThreadPoolExecutor：最核心的线程池类，用于创建和管理线程池。通过它可以灵活地配置线程池参数，如核心线程数、最大线程数、任务队列等，以满足不同的并发处理需求。
+ Executors：线程池工厂类，提供了一系列静态方法来创建不同类型的线程池，如newFixedThreadPool(创建固定线程数的线程池)、newCachedThreadPool(创建可缓存线程池)、newSingleThreadExecutor(创建单线程线程池)等，方便开发者快速创建线程池。

并发集合类：

+ ConCurrentHashMap：线性安全的哈希映射表，用于在多线程环境下高效地存储和访问键值对。采用了分段锁等技术。
+ CopyOnWriteArrayList：线程安全的列表，在对列表进行修改操作时，会创建一个新的底层数组，将修改数组操作应用到新数组上，而读操作任然可以在旧数组上进行，从而实现了读写分离，提高了并发读的性能，适用于读多写少的场景。

同步工具类：

+ CountDownLatch：允许一个或多个线程等待其他一组线程完成操作后再继续执行。通过计数器来实现。常用于多个线程完成各自任务后，再进行汇总或下一步操作的场景
+ CyclicBarrier：让一组线程相互等待，直到所有线程都到达某个屏障点后，在一起继续执行。与CountDownLatch不同的是，CyclicBarrier可以重复使用，当所有线程都通过屏障后，计数器会重置，可以再次用于下一轮的等待。适用于多个线程需要协同工作，在某个阶段完成后再一起进入下一个阶段的场景。
+ Semaphore：信号量，用于控制同时访问某个资源的线程数量。它维护了一个许可计数器，线程在访问资源前需要获取许可。

原子类：

+ AtomicInteger：原子整数类，提供了对整数类型的原子操作，如自增、自减、比较并交换等。通过硬件级别的原子指令来保证操作的原子性和线程安全性，避免了使用锁带来的性能开销，在多线程环境下对整数进行计数、状态标记等操作非常方便。
+ AtomicReference：原子引用类型，用于对对象引用进行原子操作。可以保证在多线程环境下，对对象的更新操作是原子性的，即要么全部成功，要么全部失败不会出现数据不一致的情况。常用于实现无锁数据结构或需要 对对象进行原子更新的场景。



## 怎么保证线程安全
+ synchronized关键字：可以使用synchronized关键字来同步代码块或方法，确保同一时刻只有一个线程可以访问这些代码。对象锁是通过synchronized关键字锁定对象的监视器(monitor)来实现的。
+ volatile关键字：用于变量，确定所有线程看到的是该变量的最新值，而不是可能存储在本地寄存器中的副本。
+ Lock接口和ReentrantLock类：java.util.concurrent.locks.lock接口提供了比synchronized更强大的锁定机制，ReentrantLock是一个实现该接口的例子，提供了更灵活的锁管理和更高的性能。
+ 原子类：java并发库(java.util.concurrrent.atomic)提供了原子类，如AtomicInteger、AtomicLong等，这些类提供了原子操作，可以用于更新基本类型的变量而无需额外的同步。
+ 线程局部变量：ThreadLocal类可以为每个线程提供独立的变量副本，这样每个线程都拥有自己的变量，消除了竞争条件。
+ 并发集合：使用java.util.concurrrent包中的线程安全集合，如ConcurrrentHashMap、ConcurrrentLinkedQueue等，这些集合内部已经实现了线程安全的逻辑。
+ JUC工具类：使用java.util.concurrent包中的一些工具类可以用于控制线程间的同步和协作。例如：Semaphore和CyclicBarrier等



## Java中有哪些常用的锁，在什么场景下使用？
Java中的锁是用于管理多线程并发访问共享资源的关键机制。锁可以确保在任意给定时间内只有一个线程可以访问特定的资源，从而避免数据竞争和不一致性。Java提供了多种锁机制，可以分为一下几类：

+ 内置锁(synchronized)：Java中的synchronized关键字是内置锁机制的基础，可以用于方法或代码块。synchronized加锁时有无锁、偏向锁、轻量级锁和重量级锁几个级别。偏向锁用于当一个线程进入同步块时，如果没有其他线程竞争，就会使用偏向锁，以减少锁的开销。轻量级锁使用线程栈上的数据结构，避免了操作系统级别的锁。重量级锁则涉及操作系统级的互斥锁
+ ReentrantLock：java.util.concurrent.locks.ReentrantLock是一个显式的锁类，提供了比synchronized更高级的功能，如可中断的锁等待、定时锁等待、公平锁选项等。ReentrantLock使用lock()和unlock()方法来获取和释放锁。其中，公平锁按照线程请求锁的顺序来分配锁，保证了锁分配的公平性，但可能增加锁的等待时间，非公平锁不保证锁分配的公平性，但可能增加锁的等待时间。非公平锁不保证锁分配的顺序，可以减少锁的竞争，提高性能但可能造成某些线程的饥饿。
+ 读写锁：java.util.concurrent.locks.ReadWriteLock接口定义了一种锁，允许多个读者同时访问共享资源，但只允许一个写入者。读写锁通常用于读取远多于写入的情况，以提高并发性
+ 乐观锁和悲观锁：悲观锁(Pessimistic Locking)通常指在访问数据前就锁定资源，假设最坏的情况，即数据很可能被其他线程修改。synchronized和ReentrantLock都是悲观锁的例子。乐观锁常使用版本号或时间戳来实现。
+ 自旋锁：自旋锁是一种锁机制，线程等待锁时会持续循环检查锁是否可用，而不是放弃CPU并阻塞。通常可以使用CAS来实现。这在锁等待时间很短的情况下可以提高性能，但过度自旋会很浪费CPU资源。



## 怎么在实践中用锁的？
Java提供了多种锁的实现，包括synchronized关键字、java.util.concurrent.locks包下的Lock接口及其具体实现如ReentrantLock、ReadWriteLock等。以下就是这些锁的使用方式：

1. synchronized：可以用于方法或代码块，它是Java中最早的锁实现。
2. 使用Lock接口：Lock接口提供了比synchronized更灵活的锁操作，包括尝试锁、可中断锁、定时锁等。ReentrantLock是Lock接口的一个实现。

```plain
import java.util.concurrent.locks.Lock;
import java.util.concurrent.locks.ReentrantLock;

public class Counter{
  private Lock lock = new ReentrantLock();
  private int count = 0;

  public void increment(){
    lock.lock();
    try{
      count++;
    } finally{
      lock.unlock();
    }
  }
}
```

3. 使用ReadWriteLock：它提供了一种读写锁的实现，允许多个多操作同时进行，但写操作是独占的。

```plain
import java.util.concurrent.locks.Lock;
import java.util.concurrent.locks.ReadWriteLock;
import java.util.concurrent.locks.ReentrantReadWriteLock;

public class Cache{
  private ReadWriteLock lock = new ReentrantReadWriteLock();
  private Lock readLock = lock.readLock();
  private Lock writeLock = lock.writeLock();
  private Object date;

  public Object readData(){
    readLock.lock();
    try{
      return data;
    } finally {
      readLock.unlock();
    }
  }

  public void writeData(Object newData){
    writeLock.lock();
    try{
      data=newData;
    } finally{
      writeLock.unlock();
    }
  }
}
```



## Java并发工具有哪些？
Java中一些常用的并发工具，它们位于java.util.concurrent包中，常见的有：

+ CountDownLatch：CountDownLatch是一个同步辅助类，它允许一个或多个线程等待其他线程完成操作。它使用一个计数器进行初始化，调用countDown()方法会使计数器减一，当计数器的值减为0时，等待的线程会被唤醒。可以把他想象成一个计时器，当倒计时结束(计数器为0)时，等待的事件就会发生。
+ CyclicBarrier：CyclicBarrier允许一组线程相互等待，直到到达一个公共的屏障点。当所有线程都达到这个屏障点后，它们可以继续执行后续操作，并且这个屏障可以被重置循环使用。与CountDownLatch不同，CyclicBarrier侧重于线程间的相互等待，而不是等待某些操作完成。
+ Semaphore：Semaphore是一个计数信号量，用于控制同时访问某个共享资源的线程数量。通过acquire()方法获取许可，使用release()方法释放许可。如果没有许可可用，线程将会被阻塞，直到有许可被释放。可以用来限制对某些资源(如数据库连接池、文件操作等)的并发访问量。
+ Future和Callable：Callable是一个类似于Runnable的接口，但它可以返回结果，并且可以抛出异常。Future用于表示一个异步计算的结果，可以通过它来获取Callable任务的执行结果 或取消任务。
+ ConcurrentHashMap：ConcurrentHashMap是一个线程安全的哈希表，它允许多个线程同时进行读操作，在一定程度上支持并发的修改操作，避免了HashMap在多线程环境下需要使用synchronized或Collections.synchronizedMap()进行同步的性能问题。



## CountDownLatch是做什么的
CountDownLatch是Java并发包(java.util.concurrent)中的一个同步工具类，用于让一个或多个线程等待其他线程完成操作后再继续执行。

其核心是通过一个计数器(Counter)实现线程间的协调，常用于多线程任务的分阶段控制或主线程等待多个子线程就绪的场景，核心原理：

+ 初始化计数器：创建CountDownLatch时指定一个初始计数值
+ 等待线程阻塞：调用await()的线程会被阻塞，直到计数器变为0.
+ 任务完成通知：其他线程完成任务后调用countDown()，使计数器减1.
+ 唤醒等待线程：当计数器减到0时，所有等待的线程会被唤醒



## synchronized和ReentrantLock及其应用场景
### synchronized工作原理：
synchronized是Java提供的原子性内置锁，这种内置的并且使用者看不到的锁也被称为监视锁。

使用synchronized之后，会在编译之后在同步的代码块前后加上monitorenter和monitorexit字节码指令，它依赖操作系统底层互斥锁实现。它的作用主要就是实现原子性操作和解决共享变量的内存可见性问题。

执行monitorenter指令时会尝试获取对象锁，如果对象没有被锁定或者已经获得了锁，锁的计数器加1.此时其他竞争锁的线程则会进入等待队列中。执行monitorexit指令时会把计数器-1，当计数器值为0时，则锁释放，处于等待队列中的线程在继续竞争锁。

synchronized是排它锁，当一个线程获得锁之后，其他线程必须等待该线程释放锁后才能获得锁，而且由于Java中的线程和在系统原生线程是一一对应的，线程被阻塞或者唤醒时会从用户态切换到内核态，这种转换非常消耗性能。

从内存语义来说，加锁的过程会清除工作内存中的共享变量，再从主内存读取，而释放锁的过程则是将工作内存中的共享变量写回主内存。



### ReentrantLock工作原理
ReentrantLock的底层实现主要依赖于AbstractQueuedSynchronized(AQS)这个抽象类。AQS是一个提供了基本同步机制的框架，其中包括了队列、状态值等。

ReentrantLock在AQS的基础上通过内部类Sync来实现具体的锁操作。不同的Sync子类实现了公平锁和非公平锁的不同逻辑：

+ 可中断性：ReentrantLock实现了可中断性，这意味着线程在等待锁的过程中，可以被其他线程中断而提前结束等待。在底层，ReentrantLock使用了与LockSupport.park()和LockSupport.unpark()相关的机制来实现可中断性。
+ 设置超时时间：ReentrantLock支持在尝试获取 锁时设置超时时间，即等待一定时间后如果还未获得锁，则放弃锁的获取。这是通过内部的tryAcquireNanos方法来实现的。
+ 公平锁和非公平锁：在直接创建ReentrantLock对像时，默认情况下是非公平锁。公平锁是按照线程等待的顺序来获取锁，而非公平锁则允许多个线程在同一时刻来竞争锁，不考虑他们申请锁的顺序。公平锁可以通过在创建ReentrantLock时传入true来设置。
+ 多个条件变量：ReentrantLock支持多个条件变量，每个条件变量可以与一个ReentrantLock关联。这使得线程可以更灵活地进行等待和唤醒操作，而不仅仅是基于对象监视器的wait()和notify()。多个条件变量的实现依赖于Condition接口。
+ 可重入性：ReentrantLock支持可重入性，即同一个线程可以多次获得同一把锁，而不会造成死锁。这是通过内部的holdCount计数来实现的。当一个线程多次获取锁时，holdCount递增，释放锁时递减，只有当holdCount为零时，其他线程才有机会获取锁。

### 应用场景的区别
synchronized：

+ 简单同步需求：需要对代码块或方法进行简单的同步控制时，synchronized是一个很好的选择。它使用起来简单，不需要额外的资源管理，因为锁会在方法退出或代码块执行完毕后自动释放。
+ 代码块同步：对特定代码段进行同步，而不是整个方法，可以使用synchronized代码块。这可以更精细地控制同步的范围，从而减少锁的持有时间，提高并发性能。
+ 内置锁的使用：synchronized关键字使用对象的内置锁(也称为监视器锁)，这需要使用对象作为锁对象的情况下很有用，尤其是在对象作为锁对象的情况下很有用，尤其是在对象状态与锁保护的代码紧密相关时。

ReentrantLock：

+ 高级锁功能需求：ReentrantLock提供了synchronized所不具备的高级功能，如公平锁、响应中断、定时锁尝试、以及多个条件变量。当你需要这些功能时，ReentrantLock是更好的选择。
+ 性能优化：在高度竞争的环境中，ReentrantLock可以提供比Synchronized更好的性能，因为它提供了更细粒度的控制，如尝试锁定和定时锁定，可以减少线程阻塞的可能性。
+ 复杂同步结构：需要更复杂的同步结构，如需要多个条件变量来协调线程之间的通信时，ReentrantLock及其配套的Condition对象可以提供更灵活的解决方案。

综上，Synchronized适用于简单同步需求和不需要额外锁功能的场景，而ReentrantLock适用于需要更高级锁功能、性能优化或复杂同步逻辑的情况。



## 除了用Synchronized，还有什么方法可以实现线程同步？
+ 使用ReentrantLock类：ReentrantLock是一个可重入的互斥锁，相比Synchronized提供了更灵活的锁定和解锁操作。它还支持公平锁和非公平锁，以及可以响应中断的锁获取操作。
+ 使用volatile关键字：虽然volatile不是一种锁机制，但它可以确保变量的可见性。当一个变量被声明为volatile后，线程将直接从主内存中读取该变量的值，这样就能保证线程间变量的可见性。但它不具备原子性。
+ 使用Atomic类：Java提供了一系列的原子类，例如AtomicInteger、AtomicLong、AtomicReference等，用于实现对单个变量的原子操作，这些类在实现细节上利用了CAS（Compare-And-Swap）算法，可以用来实现无锁的线程安全。



## Synchronized锁静态方法和普通方法区别？
锁多想不同：

+ 普通方法：锁的是当前对象实例(this)。同一对像实例的Synchronized普通方法，同一时间只能被一个线程访问；不同对象实例间互不影响，可被不同线程同时访问各自的同步普通方法。
+ 静态方法：锁的是当前类的Class对。由于类的Class对象全局唯一，无论多少个对象实例，该静态同步方法同一时间只能被一个线程访问。

作用范围不同：

+ 普通方法：仅对同一对象实例的同步方法调用互斥，不同对象实例的同步普通方法可并行执行。
+ 静态方法：对整个类的所有实例的该静态方法调用都互斥，一个线程进入静态同步方法，其他线程无法进入同一类任何实例的该方法。

多实例场景影响不同：

+ 普通方法：多线程访问不同对象实例的同步方法时，可同时执行。
+ 静态方法：不管有多少对象实例，同一时间仅一个线程能执行该静态同步方法。



## Synchronized和ReentrantLock区别
Synchronized和ReentrantLock是Java中提供的可重入锁：

+ 用法不同：Synchronized可用来修饰普通方法、静态方法和代码块，而ReentrantLock只能用在代码块上
+ 获取锁和释放锁方式不同：Synchronized会自动加锁和释放锁，当进入Synchronized修饰的代码块之后会自动加锁，当离开Synchronized的代码段之后会自动释放锁。而ReentrantLock需要手动加锁和释放锁。
+ 锁类型不同：Synchronized属于非公平锁，而ReentrantLock既可以是公平锁也可以是非公平锁。
+ 响应中断不同：ReentrantLock可以响应中断，解决死锁问题，而Synchronized不能响应中断。
+ 底层实现不同：Synchronized是JVM层面通过监视器实现的，而ReentrantLock是基于AQS实现的。



## 怎么理解可重入锁？
可重入锁是指同一个线程在获取了锁之后，可以再次重复获取该锁而不会造成死锁或其他问题。当一个线程持有锁，如果再次尝试获取该锁，就会成功获取而不会被阻塞。

ReentrantLock实现可重入锁的机制是基于线程持有锁的计数器。

+ 当一个线程第一次获取锁时，计数器会+1，表示该线程持有了锁。在此后，每次线程成功获取锁时，都会将计数器加1。
+ 当线程释放锁时，计数器会相应地减一。只有当计数器减到0时，锁才会完全释放，其他线程才有机会获取锁

这种计数器的设计使得同一个线程可以多次获取同一个锁，而不会造成死锁或其他问题。每次获取锁时，计数器jia1；每次释放锁时，计数器-1。只有当计数器减到0时，锁才会完全释放。

ReentrantLock通过这种计数器的方式，实现了可重入锁的机制。他允许同一个线程多次获取同一个锁，并且能够正确地处理锁的获取和释放，避免了死锁和其他并发问题。



## Synchronized支持重入吗？如何实现
Synchronized是基于原子性的内部锁机制，是可重入的，因此在一个线程调用Synchronized方法的同时在其方法体内部调用该对象另一个Synchronized方法，也就是说一个线程得到一个对象锁后再次请求该对象锁，是允许的，这就是Synchronized的可重入性。

Synchronized底层是利用计算机系统mutex Lock实现的。每一个可重入锁都会关联一个线程ID和一个锁状态status。

当一个线程请求方法时，会去检查锁状态。

1. 如果锁状态是0，代表该锁没有被占用，使用CAS操作获取锁，将线程ID替换成自己的线程ID。
2. 如果锁状态不是0，代表有线程在访问该方法。此时，如果线程ID时自己的线程ID，如果是可重入锁，会将status自增1，然后获取到该锁，进而执行相应的方法；如果是非重入锁，就会进入阻塞队列等待。

在释放锁时，

1. 如果是可重入锁，没一次退出方法，就会将status减1，直至status的值为0，最后释放该锁。
2. 如果非可重入锁，线程退出方法，直接就会释放该锁。



## Synchronized锁升级的过程
具体的锁升级的过程是：无锁->偏向锁->轻量级锁->重量级锁。

+   无锁：这是没有开启偏向锁的时候的状态，在JDK1.6之后偏向锁的默认开启，但是有一个偏向延迟，需要在JVM启动之后的多少秒之后才买开启，这个可以通过JVM参数进行设置，同时是否开启偏向锁也可以通过JVM参数设置。
+ 偏向锁：这个是在偏向锁开启之后的锁的状态，如果还没有一个线程拿到这个锁的话，这个状态叫做匿名偏向，当一个线程拿到偏向锁的时候，下次想要竞争锁只需要拿线程ID跟MarkWord当中存储的线程ID进行比较，如果线程ID相同则直接获取锁(相当于锁偏向于这个线程)，不需要进行CAS操作和将线程挂起的操作。
+ 轻量级锁：在这个状态下线程主要是通过CAS操作实现的。将对象的MarkWorld存储到线程的虚拟机栈上，然后通过CAS将对象的MarkWord的内容设置为指向Displaced Mark Word的指针，如果设置成功则获取锁。在线程出临界区的时候，也需要使用CAS，如果使用CAS替换成功则同步成功，如果失败表示有其它线程在获取锁，那么就需要在释放锁之后将被挂起的线程唤醒。
+ 重量级锁：当有两个以上的线程获取锁的时候轻量级锁就会升级为重量级锁，因为CAS如果没有成功的话始终都在自旋，进行while循环操作，这是非常消耗CPU的，但是在升级为重量级锁之后线程会被操作系统调度然后挂起，这可以节约CPU资源。



## JVM对Synchronized的优化？
Synchronized对核心优化方案主要包含一下4个：

+ 锁膨胀：Synchronized从无锁升级到偏向锁，再到轻量级锁，最后到重量级锁的过程，它叫做锁膨胀也叫做锁升级。JDK1.6之前，Synchronized是重量级锁，也就是说Synchronized在释放和获取锁时都会从用户态转换成内核态，而转换的效率是比较低的。但有了锁膨胀机制之后，Synchronized的状态就多了无锁、偏向锁以及轻量级锁了，这时候在进行并发操作时，大部分的场景都不需要用户态到内核态的转换了，这样就大幅的提升了Synchronized的性能。
+ 锁消除：指的是在某些情况下，JVM虚拟机如果检测不到某段代码被共享和竞争的可能性，就会将这段代码所属的同步锁消除掉，从而到底提高程序性能的目的。
+ 锁粗化：将多个连续的加锁、解锁操作连接在一起，扩展成一个范围更大的锁。
+ 自适应自旋锁：指通过自身循环，尝试获取锁的一种方式，优点在于它避免一些线程的挂起和恢复操作，因为挂起操作和恢复线程都需要从用户态转入内核态，这个过程是比较慢的，所以通过自旋的方式可以一定程度上避免挂起和恢复所造成的性能开销。



## 介绍一下AQS
AQS全称为AbstractQueueSynchronizer，是Java中的一个抽象类。AQS是一个用于构建锁、同步器、协作工具类的工具类(框架)。

AQS核心思想是，如果被请求的共享资源空闲，那么就将当前请求资源的线程设置为有效的工作线程，将共享资源设置为锁定状态；如果共享资源被占用，就需要一定的阻塞等待唤醒机制来保证锁分配。这个机制主要用的是CLH队列的变体实现的，将暂时获取不到锁的线程加入到队列中。







## 



















