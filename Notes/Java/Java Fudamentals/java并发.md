# Java并发基础

## 同步

* 两个或两个以上的线程需要共享对同一数据的存取，可能会产生竞争条件

### 锁对象

* java 提供一个可重入锁 ReentrantLock, 其保护代码块的基本结构如下：

  ```java
  myLock.lock();
  try{
      
  }
  finally{
      myLock.unlock();
  }
  ```

  解锁操作放到finally字句内，确保即使临界区代码抛出异常，锁也必须被释放

  finally只能有在try/catch语句中，作为异常处理的一部分。

  finally不被执行的几种情况：没有执行try语句；System.exit(0) 退出了jvm； 线程被中断(interrupt)或终止（kill）

* 锁是可重入的，锁保持一个**持有计数**来跟踪对lock方法的嵌套调用（加锁持有计数器+1，解锁持有计数器-1）。被一个锁保护的代码可以调用另一个使用相同的锁的方法

### 条件对象

* 条件对象用来管理已经获得锁但是却不能做有效工作的线程，这些线程需要等待某一条件满足之后才能执行

* 常见的用法如下：

  ```java
  private Condition condition=myLock.newCondition();     //条件对象绑定相关的锁，一个锁对象可以有一个或者多个条件对象
  
  myLock.lock();
  try{
      while(!(ok to proceed))   //循环检验，直到满足执行条件
          condition.await();  //调用await() 当前的线程被阻塞，并且释放对象锁
      // ....something to do ...
      condition.signalAll();  //执行一系列操作以后，条件发生改变
  }
  finally{
      myLock.unlock();
  }
  ```

* signalAll 激活因为条件对象而等待的所有线程（解除阻塞），将他们从等待集中移出，再次成为可运行状态，由调度器决定某一个线程获得该锁

  signal 只解除等待集中某一个线程的阻塞状态，这样做更加有效但是存在风险，系统有可能产生死锁

### Synchronized 关键字

* Java中每个对象都有一个内部锁（intrinsicLock），如果一个方法用**synchronized**关键字声明，那么要调用该方法必须获得内部的对象锁
* 内部锁只有一个相关条件，wait()方法添加一个线程到等待集中，notifyAll/notify方法解除等待线程的阻塞状态（notifyAll（）等价于 intrinsicContidition.signalAll( )）
* Synchronized VS Lock/Condition    Synchronized关键字减少编写代码的数量，但是也有局限：每个内部锁只有一个单一的条件，可能不够；
* wait notifyAll notify方法是Object类的final方法，wait只能在同步方法中调用， notifyAll notify只能在同步方法或同步块中调用

### 同步阻塞

* 每个Java对象都有一个锁，线程可以通过调用同步方法获得锁，也可以通过进入一个同步阻塞获得锁。

  使用方法如下：

  ```java
  synchronized(object){  //获得object的内部锁，这是一个同步代码块
      //......
  }
  ```

### volatile 域

* Volatile的实现原理跟java的内存模型有关

* volatile关键字为实例域的同步访问提供了一种免锁机制

* 一旦一个共享变量（类的成员变量、类的静态成员变量）被volatile修饰之后，那么就具备了两层语义： 

  1）保证了不同线程对这个变量进行操作时的可见性，即一个线程修改了某个变量的值，这新值对其他线程来说是立即可见的。也就是 它会强制将对缓存的修改操作立即写入主存； 并且如果是写操作，它会导致其他CPU中对应的缓存无效。 

  2）禁止进行指令重排序。禁止指令重排序有两层意思：a 当程序执行到volatile变量的读操作或者写操作时，在其前面的操作的更改肯定全部已经进行，且结果已经对后面的操作可见；在其后面的操作肯定还没有进行； 

  b.在进行指令优化时，不能将在对volatile变量访问的语句放在其后面执行，也不能把volatile变量后面的语句放到其前面执行。 

* volatile 不具备原子性，具有可见性、有序性。volatile 不同于synchronized的阻塞式同步，不会让线程阻塞，响应速度比synchronized高，这是它的优点。 

* 为什么volatile不能保证原子性

  Java中只有对基本类型变量的赋值和读取是原子操作，如i = 1的赋值操作，但是像j = i或者i++这样的操作都不是原子操作，因为他们都进行了多次原子操作，比如先读取i的值，再将i的值赋值给j，两个原子操作加起来就不是原子操作了。 所以，如果一个变量被volatile修饰了，那么肯定可以保证每次读取这个变量值的时候得到的值是最新的，但是一旦需要对变量进行自增这样的非原子操作，就不会保证这个变量的原子性了。 

* 参考资料  [[Java并发编程：volatile关键字解析](https://www.cnblogs.com/dolphin0520/p/3920373.html)]()

### 线程局部变量

* ThreadLocal为变量在每个线程中都创建了一个副本 

  每个线程都有一个ThreadLocalMap的数据结构，存储一系列键值对，它的键是ThreadLocal对象 值是这个对象的值

  通过threadlocal.get() /set() 来读取和设置其对应的值

* 避免ThreadLocal引起的内存泄漏  注意在使用完ThreadLocal之后，需要显示的调用remove（） 方法

### CAS

* Compare and Swap，即比较再交换 是一种无锁算法 

* CAS有3个操作数，CAS(V, A, B) 内存值V，旧的预期值A，要修改的新值B。当且仅当预期值A和内存值V相同时，将内存值V修改为B，否则什么都不做。 整个比较并替换的操作是一个原子操作。 

* java并发包中使用循环CAS算法实现了很多的原子操作

  例如IncrementAndGet操作的实现

  ```java
  public final int incrementAndGet() {
          for (;;) {
              int current = get();
              int next = current + 1;
              if (compareAndSet(current, next))   //如果旧的值current等于现在的值this.get() 就表示当前的值还没有被其他线程设置，那么就用current+1更新当前值，实现自增
                  return next;
          }
  }
  ```

* CAS实现原子操作的几个问题：

  * ABA问题： 变量的更新顺序是 A->B->A，CAS检查不出变化，其实却发生了变化，解决ABA问题可以引入版本号，在变量值的前面追加版本号
  * 循环时间开销大 由于不阻塞线程所以CPU开销大
  * 只能保证一个共享变量的原子操作  JDK1.5之后提供了AtomicReference保证引用对象的原子性，可以将多个变量放到一个对象里来进行CAS操作