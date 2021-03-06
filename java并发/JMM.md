### 现代计算机理论模型和工作原理

![](https://raw.githubusercontent.com/itscj1014/PictureBed/master/img/20200520230408.png)

冯洛伊曼计算机模型

IO总线带宽

寄存器 > L1> L2 > L3> 内存条

内存条为啥不做成L1/L2/L3?

L1> L2 > L3 成本很高！

内存的读取速度跟不上CPU的速度



举例：main{a =1 +1 }

首先会把数据从内存中依次load到L3->L2->L1中，然后CPU计算，同步回缓存L1->L2->L3中，那么什么时候同步回内存中呢？

1.缓存空间不够用了 LRU FIFO

Lock   

**Mesi 缓存一致性协议**



CPU内存结构图：

![CPU内存结构图](https://raw.githubusercontent.com/itscj1014/PictureBed/master/img/20200520233002.png)



**CPU多核缓存架构**

每个CPU的核上会配置一个寄存器

每个主板可以搭配多个CPU

缓存一致性协议入门：（参考资料： https://www.infoq.cn/article/cache-coherency-primer/ ）



两个CPU的线程同时加载一个内存中的变量和执行相同操作，例如 X = 1  ,X=X+1，结果两个CPU计算了两次，最后得出的结果是2

如果是同一个CPU上的两个线程呢？

1.总线加锁

对内存的变量区域读写都加锁，数据超过一个缓存行（cache line）的时候，会使用总线锁



2.缓存一致性协议 MESI（参考资料： https://www.cnblogs.com/yanlong300/p/8986041.html  ）

多处理器系统和多核系统：

CPU、物理核、逻辑核（ https://blog.csdn.net/zuozewei/article/details/85251890 ）

多处理器系统是包含多个单核处理器（CPU）；

多核系统是指电脑只有一个处理器，处理器是多核的

超线程：一个ALU对应多个PC（程序计数器）|Registers（寄存器）

<img src="https://raw.githubusercontent.com/itscj1014/PictureBed/master/img/20200523024824.png" style="zoom:70%;" />

 有多个完全独立的 CPU 用于所有意图和目的，每个核心一个。实际上，从性能的角度来看，拥有单个多核处理器比在同一块板上的同等数量的单核 CPU 更好 。



CPU多核的好处:

一个多核的CPU也就是一个CPU上有多个处理器核心，这样有什么好处呢？比如说现在我们要在一台计算机上跑一个多线程的程序，因为是一个进程里的线程，所以需要一些共享一些存储变量，如果这台计算机都是单核单线程CPU的话，就意味着这个程序的不同线程需要经常在**CPU之间的外部总线上通信**，同时还要处理不同CPU之间不同缓存导致数据不一致的问题，所以在这种场景下多核单CPU的架构就能发挥很大的优势，**通信都在内部总线，共用同一个缓存**。



![](https://raw.githubusercontent.com/itscj1014/PictureBed/master/img/20200523125845.png)



CPU缓存中最小存储单元概念：缓存行 CacheLine 

**缓存行对齐，伪共享：**

指令重排

Lock 指令

总线嗅探机制

E(Lock) 独占-> S 共享 -> M 改变 -> I 无效

缓存行失效：

1. 如果变量存储长度大于一个缓存行，加总线锁

2. CPU本身不支持缓存一致性协议



### 什么是线程

进程是系统分配资源的基本单位，线程是调度CPU的基本单位，一个进程至少包含一个执行线程，线程寄生在进程上，每个线程都有一个程序计数器（记录要执行的下一条指令），一组寄存器（保存当前线程的工作变量）。堆栈（记录执行历史，其中每一帧保存了一个已经调用但未返回的过程）

实现线程的主要三种方式：

- 使用内核线程（1:1 实现）
- 使用用户线程（1：n 实现）
- 使用用户线程加轻量级进程混合实现（N:M）







线程分类：

1. 用户级线程（存在于用户空间）
2. 内核级线程（存在于内核空间）

CPU的调度级别(特权级别)：

1. Ring0 （内核空间才有资格）
2. Ring3（用户空间）

内核空间

用户空间

ULT 用户级线程

KLT 内核级线程



Java线程和内核线程的关系





### 为什么用并发，并发产生的问题

**什么是线程上下文？线程上下文切换？**

CPU通过时间片来分配算法来循环执行任务，当前任务执行一个时间片后会切换到下一个任务，但是呢，在切换之前会保存上一个任务的状态，以便下次切换回这个任务时能，可以再加载这个任务的状态。所以任务 从保存到再加载的过程就是一次上下文切换。

时间片：CPU分配给各个线程的时间

因为时间片非常短，所以CPU通过不停的切换线程执行，让我们感觉多个线程是同时执行的

并发并不等于并行：

并发是指多个任务交替进行，并行则是真正意义上的“同时进行”，

如果系统只有一个CPU，使用多线程时，是没法并行的，只能通过切换时间片的实行交替进行而成为并发执行任务。真正的并行只能出现在拥有多个CPU的系统中

内核栈 

任务状态段（TSS）

从新读回到CPU的寄存器中



**死锁现象（Demo）**

jps

jstack port



 计算机模型 ——硬件结构——CPU多级缓存架构——java 线程是依赖内核KLT——并发线程上下文切换死锁





### JMM

内存模型：在特定的操作协议下，对特定的内存或高速缓存进行读写访问的过程抽象

java内存模型的主要目的就是定义了程序中各种变量的访问规则

工作内存和主内存：

CPU 多级缓存架构的抽象

屏蔽底层操作系统的不同

Java规范，JMM是一组规范

**原子性、有序性、可见性**



线程间感知——MESI



jvm 进程，他去申请空间，大部分时候，操作的是逻辑空间，不是物理空间，这个逻辑空间是我们系统已经划分好的



**JMM的抽象理解**





**java内存模型内存交互操作**



可见性：代码sample



原子性：volatitle不保证原子性  代码sample

- 指令重排：前提是 as-if-serial
  - 发生时期：编译字节码阶段（执行器编译器）、CPU运行时指令重排

- 内存屏障



有序性： 禁止指令重排序优化



**除了volatile，还有什么措施可以禁止指令重排？**

还是得通过内存屏障——手动加内存屏障

Unsafe.loadFence()

Unsafe.storeFence()



### volatile



**总线风暴**



~~~java
public class Singleton {

    /**
     * 查看汇编指令
     * -XX:+UnlockDiagnosticVMOptions -XX:+PrintAssembly -Xcomp
     */
    private volatile static Singleton myinstance;

    public static Singleton getInstance() {
        if (myinstance == null) {
            synchronized (Singleton.class) {
                if (myinstance == null) {
                    myinstance = new Singleton();//对象创建过程，本质可以分文三步
                    //1. 申请内存空间
                    //2. 实例化对象
                    //3. 填充实例化数据
                    // 所以需要加上volatile，来禁止指令重排
                    //对象延迟初始化
                    //
                }
            }
        }
        return myinstance;
    }

    public static void main(String[] args) {
        Singleton.getInstance();
    }
}
~~~

