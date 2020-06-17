#### 并发同步器

多线程编程中，有可能会出现多个线程同时访问同一个共享、可变资源的情况，这个资源可能是：对象、变量、文件等

- 共享：资源可以由多个线程访问
- 可变：资源可以在其生命周期内被修改

临界资源



#### 加锁的分类和目的

显式锁：ReentrantLock，实现juc里的Lock，实现是基于AQS的，需要手动加锁和解锁

​	ReetrantLock :lock() unlock()

隐式锁：jvm内置锁，synchronized,不需要手动加锁和解锁，jvm会自动加锁和解锁



​	**跨方法加对象锁？？** 

​	**unsafeInstance.reflectGetUnsafe()**



加锁的目的：串行化的访问临界资源，即同一时刻只有一个线程访问临界资源（同步互斥访问）



**synchronized**

加static，加锁加在类对象上

不加static，加锁加在this当前实例对象上，当前bean由容器管理，必须bean作用域是单例（因为如果不是单例的话，每次从容器中拿到的类对象都是新的，但是锁是作用在类对象上的，所以锁就没有效果了）



synchronized 翻译成两个指令 monitorenter moniterexit  

互斥量



ObjectMonitor() 

WaitSet 队列： 处于wait状态的线程 会被加入到WaitSet中

**可重入？？什么意思**



#### 对象内存结构

实例对象内存中存储在哪？

如果实例对象存储在堆区时：实例对象内存存在堆区，实例的引用存在栈上，实例的元数据class存在方法区或者元空间

并不是所有的实例对象都存放在堆区，有的一部分实例对象存放在线程栈里

**线程逃逸**（产生逃逸分析优化的条件或者前提）

JIT编译

有无返回值：



锁的粗化

锁的消除

<img src="https://raw.githubusercontent.com/itscj1014/PictureBed/master/img/20200604015417.png" style="zoom:67%;" />



markOop.hpp

**JVM内置锁膨胀升级过程**

JDK1.6之后：

无锁——> 偏向锁——>轻量级锁——>重量级锁

锁升级不可逆



**轻量级锁：**

![](https://raw.githubusercontent.com/itscj1014/PictureBed/master/img/20200605025859.png)

自旋不会丢失CPU执行权

线程间交替执行场景，本身竞争不是很激烈

1.7之前手动设置自旋次数，超过自旋次数之后，会升级到重量级锁；

1.7之后自适应自旋，以上一次实际自旋次数为智能类推

偏向锁不会自动释放



