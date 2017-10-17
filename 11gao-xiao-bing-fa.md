## 一、硬件并发模型

![](/assets/hardwareconcurrent.png)

## 二、Java内存模型：

### 1.线程，主存，工作内存交换图

![](/assets/javamemory.png)

### 2.8种内存间交互操作

**lock**

作用于主内存的变量， 将一个变量标示为一个线程独占的状态。

**unlock**

作用于主内存的变量， 将一个处于锁定状态的变量释放，释放后的变量才能被其他线程锁定

**read**

作用于主内存的变量， 将变量的值从主内存传递到工作内存，供load动作使用。

**load**

作用于工作内存的变量，将read操作获取的变量值放入工作内存的变量副本中。

**use**

作用于工作内存的变量，将变量值传递给执行引擎，每当虚拟机需要使用这个变量的值时，将会执行这个操作。

**assign**

作用于工作内存的变量，将从执行引擎接收到的值赋给变量。虚拟机给变量赋值时，j将会执行这个操作。

**store**

作用于工作内存变量，将工作内存中的变量传递给主内存，供write使用

**write**

作用于主内存的变量，它把store操作从工作内存中得到的变量的值放入主内存的变量中。

![](/assets/javamemorywork.png)

## 三 、volatile简介

### 1.volatile简要说明

volatile保证可见性

，一个线程修改了volatile变量，新值对于其他变量是立即可见的。

volatitle禁止指令重排序优化。

举例：

 指令1：把地址A的值 +10

 指令2：把地址A的值\*2

 指令3：把地址B的值-3（指令3可以重新排序到 指令1，指令2之间）

 重新排序后，cpu看起来依旧有序。

 lock addI $0x0 \(%esp\) 指令保证所有修改同时进入到主要内存中。（volidate中会存在）

实际效果相当于禁止了指令重新排序。

### 2、满足2条规则下可以使用volatile

 运算结果并不依赖于变量的当前值，或能够保证只有一个线程修改这个变量。

 变量不需要与其他状态变量共同参与不变约束。

## 四、long和double的非原子协定

 规范虽然允许long和double对应64位的操作分成2个32位操作，但是sun也建议商业java虚拟机将long和double设置成原子操作，大部分虚拟机都将long和double的操作设置为原子的。

## 五、原子，可见，有序性，与happen-before原则

### ** 原子性：**

除了read,load,use，assgin,store,write外，JVM还提供了粗粒度的和原子性lock和unlock，

lock对应的字节码为 ：monitorenter

unlock对应的字节码为：monitorexit。

### **可见性：**

除了volatile外，synchronized和final 也提供可见性。

synchronized一个变量unlock之前，必须将变量值传递会主内存，从而获得了可见性。

final修饰的变量，一旦初始化完成（this未逃逸），其他线程就能看见这个值。

有序性：本线程内观察所有操作有序，

一个线程观察另外一个线程操作无序（指令重排，工作内存与主内存内容延迟的现象）。

### hanppen-before原则

**程序次序规则（Program Order Rule）**

     一个线程内按程序代码顺序，前面的操作先发送与书写在后面的操作.

**管程锁定规则（Monitor Lock Rule）**

    同一个锁unlock发生在lock之前（不同线程下）

**volatile变量原则：**

   时间顺序上，对一个volatile变量的写操作，先于后续的读操作。

**线程启动规则（Thread Start Rule）：**

   Thread的start方法，先于此线程的每一个 动作。

**线程终止规则（Thread Termination Rule）：**

   线程中每一个动作在线程终止前发生

**线程中断规则（Thread Interrupt Rule）：**

    对线程的interrupt\(\)操作，先行发生于对中断代码的检测。Thread.interrupted\(\)检测是否发生中断。

**对象终结规则（Finalizer Rule）;**

   对象的初始化，先行对象的finalize方法。

**传递性：**

    A先行于B,B先行于C，则A先行于C

## 六、Java与线程

### 1.线程的实现

**轻量级进程与内存线程之间1:1的关系**。（使用内核实现）

![](/assets/lightimpl.png)

图例：

LWP 轻量级进程（Light Weight Process） ，通常意义的线程

KLT 内核线程（Kernel-Level KLT）

P 进程

特点： 每个轻量级进程都成为一个独立的调度单元，即使有一个轻量级进程在系统中阻塞，也不会影响整个进程工作。

局限性：

a.所有线程操作都需要进行系统调用。而系统调用的代价较高，需要再User Mode和Kernel Mode进行切换。

b.轻量级进程都由内核线程的支持，因此轻量级进程的数量是有限的。

**进程与用户线程之间1：N的关系（使用用户线程实现）**

![](/assets/userThread.png)

图例：

LWP 轻量级进程（Light Weight Process） ，通常意义的线程

KLT 内核线程（Kernel-Level KLT）

P 进程

特点： 每个轻量级进程都成为一个独立的调度单元，即使有一个轻量级进程在系统中阻塞，也不会影响整个进程工作。

局限性：

a.所有线程操作都需要进行系统调用。而系统调用的代价较高，需要再User Mode和Kernel Mode进行切换。

b.轻量级进程都由内核线程的支持，因此轻量级进程的数量是有限的。

**进程与用户线程之间1：N的关系（使用用户线程实现）**

![](/assets/mix.png)



特点：内核线程和用户线程的混合实现方式

用户线程在用户空间创建，切换，析构，这些操作非常的廉价。

轻量级进程作为用户线程和内核线程之间的桥梁，这样可以使用内核线程提供的调度以及映射处理。

## **java线程的实现**

JDK1.2是纯用户线程实现（绿色线程），

虚拟机规范未限定。

Sun JDK：在Windows 和Linux下是一对一的模型。

Solaris下是一对一，多对多，都有，

通过-XX：+UseLWPSynchronization\(默认值\) 一对一

 -XX：+UseBoundThreads 一对多

### **java线程的调度方式**

 a.协同式线程调度

实现简单，一个线程一个线程的处理，易阻塞。

 b.抢占式线程调度

每个线程的执行时间由系统来分配执行，线程切换不由本身决定。Thread.yield\(\)可以让出执行时间。Java可以设置优先级，来“建议”系统，多分配时间，但是每个操作系统不同，优先级并不能与java的优先级一一对应。

### **状态转换**

![](/assets/ThreadState.png)

waiting 无限等待，没有设置timeout：

Object.wait\(\)

Thread.join\(\)

LockSupport.park\(\)

Time Waiting限时等待：

Thread.sleep\(\)

设置了TimeOut的 Object.wait\(\)

设置了TimeOut的Thread.join\(\)

LockSupport.parkNanos\(\)

LockSupport.parkUntil\(\)



