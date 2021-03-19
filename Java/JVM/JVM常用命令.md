### 查看虚拟机信息常用命令

#### 正在运行在 jvm 上的进程

```bash
jps -l
```

#### 虚拟机统计信息监控

```bash
jstat [option vmid [interval[s|ms]] [count]]
```

option 参数表示用户希望查看的信息 `jstat -gcutil 12000 1000 3` 隔一秒输出一次 GC 情况

- interval 间隔多少时间
- count 输出次数

![image-20200915110335620](../../img/image-20200915110335620.png)

#### java 配置信息

```bash
jinfo [option] pid
```

#### Java 内存映像工具(堆栈信息转褚工具)

```bash
jmap [option] vmid # jmap,查看堆信息
# 查看存活对象
jmap -histo:live <vmid> | head -10
```

option 参数如下

![image-20200915111022360](../../img/image-20200915111022360.png)

- **<font color="red">进程信息 dump 下来命令[ jmap -dump:live,format=b,file=dumpFileName pid ]</font>**

#### 虚拟机堆转储快照分析工具 jhat 配合 dump 下来的快照使用

#### Java 堆栈追踪工具 ,查看堆栈信息 `jstack`

```bash
jstack [option] vmid  # 查看堆栈信息
jstack vmid > fileName  # 转储堆栈信息
```

#### jdk 自带的可视化工具

```bash
jconsole
jvisualvm
# 它是一个多功能工具，可以用来导出堆，查看 Java 进程，导出线程信息，执行 GC 等。
jcmd -l
jcmd <vmid> help
```