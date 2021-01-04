### java 中堆和栈分别是什么?

> 堆是所有变量存储的地方,所有线程共享的区域.
>
> 栈是线程私有的,创建一个线程的时候,会创建对应的栈帧.

### java7 和 java8 在内存布局有什么变化

> java7 的永久代是存放在堆内存区域中的. 在 java8 中把永久代改成了元数据空间(Meta space),并单独占一块区域, 元数据空间不会占用堆内存空间.
>
> 方法区保存一些类静态变量,类信息等数据

### 堆的结构包含什么

- edan 区
- 两个 Survol 区
- 一个老年代

### 栈的结构包含什么

- 局部变量表
- 操作数栈
- 栈帧
- 动态连接

### 常用命令行工具

- javac 编译工具
- javap 查看字节码工具
- jps 查看运行 java 程序
- jinfo 查看指定 java 程序信息
- **jstack 查看指定 java 程序栈信息**
- **jmap 查看指定 java 程序内存信息/生成内存快照**
- jhat java 堆栈分析工具
- **jstat 查看指定 java 程序状态**
- jconsole

### 字节码

> 类加载的本质把一个类的二进制表示加载到 jvm 中.
> 某个 jvm 方法需要进行调用,需要用到某个类时,调用类加载子系统加载类.会使用 ClassLoader 加载某个类.

### 什么是双亲委派模型,有什么好处

### 类加载器类型

### instanceof 如何判断两个实例是否相同

### 什么是 GC

### 描述一下 GC 的过程

### GC roots 包含的哪些对象

### 如何判断一个对象是否存活

### 什么是分代 GC,分为哪些代

### 什么是强/软/弱/虚引用

### 什么是 stop the world

比较安全,会产生很多延迟.

### 听说过哪些 GC 算法

### 听说过哪些调优参数

### 线程安全是什么

- java 中如何保证线程安全
  - 使用不可变对象
  - 锁
  - 无锁容器
- 什么是死锁

### 线程池

- 为什么使用线程池
- 常见线程池和使用场景/参数
- 主要类`Exectors` `ThreadPoolExector`

### 线程的生命周期,状态

> `Thread.State`
> NEW->RUNNING->BLOCKED->WAITING->TIMED_WAITING->TERMI

### volatile 是干嘛的

jmm java 线程模型
可见性
每个线程都有自己的工作内存,会把主内存的变量复制到工作内存中. 如果有改变

### 什么是 java 线程模型

每个线程可以被分配到一个 cpu 上,有一个工作内存,
除了局部变量,所有变量都是 java 所有线程共享的

### ThreadLocal 原理

ThreadLocal 里的值放在 Thread 里,

### synchronized 和 lock 优缺点

synchronized 是一个关键字,比较简单,只能有一个线程持有锁.
lock 是 jdk 层面,有丰富的 api

### syncchronized 锁升级/锁粗化

### 无锁 cas 原理/常用 juc 工具类

- countDownLatch
- CyclicBaiier
- S
-

### 事物和隔离等级

事物四大特性
acid: 原子性/一致性/隔离性/持久性

隔离等级:事物并发情况

- 读未提交(read uncommitted): 产生脏读
- 读已经提交(read committed): 产生不可重复读
- 可重复读(repeatable read): 产生幻读 mysql 默认隔离等级
- 事物串行(Serializable):

#### 事物的幂等性

### 乐观锁/悲观锁

### 数据库基本数据结构

- B+树
- hash

索引如何排布:

### 索引优化

- 最左匹配
- 如何判断 sql 查询是否使用了索引
- 如何进行索引优化

### 如何避免 sql 注入

Mybatis Orm 框架

### Redis 为什么这么快

全内存操作
因为 redis 瓶颈不在网络 io 等,所以是单线程.避免多线程的线程切换开销.

### redis 数据结构

string 字符串
hash
list 列表,按照插入顺序排序
set 无序集合
zset 有序集合,每个值有个对应的分数
