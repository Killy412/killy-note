## JDK

### 位运算

**二进制中最高位为0 是正数  1是负数**

- **左移 <<**: 
- **右移 >>**
- **无符号右移  >>>**: 
- **位与 &:** 第n位的二进制数都为1,结果为1,否则为0
- **位或 |:** 第n位的二进制数,只要有一个为1,结果为1,否则为0
- **位异或 ^:** 第n位的二进制数相反,结果为1,否则为0
- **位非 ~:** 完全取反

### HashMap

#### 基本特征

HashMap 允许插入键为 null 的键值对。但是因为无法调用 null 的 hashCode() 方法，也就无法确定该键值对的桶下标，只能通过强制指定一个桶下标来存放。HashMap 使用第 0 个桶存放键为 null 的键值对。

#### 底层数据结构

> 底层数据结构是数组+链表. HashMap通过key的hash值 `(N-1)&hash` (n是指数组的长度)计算得到应该存入数组的位置,然后判断当前位置是否存在元素,存在元素就判断key值是否相同,如果相同直接覆盖,不同的话通过拉链法解决冲突.
>
> 拉链法是指将数组和链表相结合. 就是创建一个链表数组,数组中每一格都是链表,遇到hash冲突,将冲突的值插入链表中.
> 1.7之前是头插，1.7之后是尾插.
> 1.7为什么是头插:缓存的时间局部原则，最近访问过的数据下次大概率会再访问，把后添加的元素放在前面，减少查找时间。但是会导致多线程添加元素时，链表成环。
> 1.8为什么是尾插: 虽然解决了并发情况链表成环的问题，但是HashMap在任何版本的jdk中都是线程不安全的，也有很多其他并发问题。
>
> JDK1.8之后当链表长度大于8时,会转为红黑树.

#### HashMap属性

```java
public class HashMap<K,V> extends AbstractMap<K,V> implements Map<K,V>, Cloneable, Serializable {
    // 序列号
    private static final long serialVersionUID = 362498820763181265L;    
    // 默认的初始容量是16
    static final int DEFAULT_INITIAL_CAPACITY = 1 << 4;   
    // 最大容量
    static final int MAXIMUM_CAPACITY = 1 << 30; 
    // 默认的填充因子
    static final float DEFAULT_LOAD_FACTOR = 0.75f;
    // 当桶(bucket)上的结点数大于这个值时会转成红黑树
    static final int TREEIFY_THRESHOLD = 8; 
    // 当桶(bucket)上的结点数小于这个值时树转链表
    static final int UNTREEIFY_THRESHOLD = 6;
    // 桶中结构转化为红黑树对应的table的最小大小
    static final int MIN_TREEIFY_CAPACITY = 64;
    // 存储元素的数组，数量总是2的幂次倍
    transient Node<k,v>[] table; 
    // 存放具体元素的集
    transient Set<map.entry<k,v>> entrySet;
    // 存放的元素个数，注意这个不等于数组的长度。
    transient int size;
    // 每次扩容和更改map结构的计数器
    transient int modCount;   
    // 临界值 当实际大小(容量*填充因子)(size)超过临界值时，会进行扩容
    int threshold;
    // 加载因子
    final float loadFactor;
}
```

#### 为什么HashMap的长度是2的幂次方

为了能让HashMap存取高效,尽量减少hash碰撞,尽量把值存放均匀.

为了存放数据均匀,所以使用取余计算数据存放的位置索引,`hash%length`. 而计算机底层运算都是转为二进制去运算的,所以通过十进制的取余不如二进制的位运算效率更高. 而如果容量是2的幂次方,则 `hash%length==hash&(length-1)`.所以需要hashmap长度是2的幂次方.

> [并发下HashMap可能出现的问题](https://coolshell.cn/articles/9606.html)


#### HashMap的实现1.7和1.8的区别

底层数据结构都是数组和链表. 
- 1.8在链表长度大于8的时候,会把链表转为红黑树.
- 1.7插入链表是头插,1.8是尾插.
  头插法容易出现逆序并且环形链表死循环问题.头插法在插入时不需要遍历到尾部再插入,最近put的对象可能等下就被get,头插拿的概率比较大.
- 1.7是先扩容再put,1.8是先put再扩容.
- 扩容后数据存储位置的计算方式有改变.

### wait()和sleep()

- wait() 挂起当前，等待被唤醒，会释放锁。如果没有释放锁，会导致其他线程无法进入同步方法/同步块中，无法执行唤醒操作，导致死锁。
- sleep() 当前线程休眠，忙等待，不会释放锁。

## SPI机制

> 为了实现在模块装配的时候不在程序里动态指明,需要一种服务发现机制.Java SPI就提供了这样一个机制,**为某个接口寻找服务实现的机制. 根据实际需要,启用/扩展/替换框架的实现策略**

使用SPI,需要遵守如下规定:

1. 当服务提供者提供了一种具体实现后,在jar包的MEAT-INF/service目录下创建一个"接口全限定名"命名的文件,内容是实现类的全限定名
2. 接口实现类所在的jar包放在主程序classpath中.
3. 主程序通过java.util.ServiceLoader 动态装载实现模块,通过扫描MEAT-INF/service目录下的配置文件,把实现类加载到jvm中
4. SPI的实现类必须携带一个无参构造函数

优点:

- 实现解构,使得接口的定义和具体实现分离. 应用程序可以根据实际需要替换实现.

缺点:

- 不能按需加载,接口的实现会全部实例化. 只能通过遍历去获取实例.
- 多线程情况下ServiceLoader不是线程安全的.

## JDK密钥生成

- 生成私钥和公钥

```
生成私钥：ssh-keygen -t rsa -b 4096 -f ${private}.key -C ""
生成公钥：openssl rsa -in ${private}.key -pubout -outform PEM -out ${public}.key.pub
转换格式：openssl pkcs8 -topk8 -inform PEM -in jwtRS256.key -outform pem -nocrypt -out pkcs8.pem
```
