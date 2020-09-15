# Linux


## 句柄
文件句柄,也叫文件描述符.在Linux系统中,文件可分为：普通文件、目录文件、链接文件和设备文件。文件描述符（File Descriptor）是内核为了高效管理已被打开的文件所创建的索引，它是一个非负整数（通常是小整数），用于指代被打开的文件。所有的IO系统调用，包括socket的读写调用，都是通过文件描述符完成的。
通过ulimit命令查看单个进程最大文件句柄数量

```
# 查看句柄数量
ulimit -n
# 修改句柄数量
ulimit -n 1000000
```
上面修改只在当前用户环境有效,退出shell,数值会恢复默认.编辑/etc/rc.local开机启动文件,在文件中添加如下内容
```
ulimit -SHn 1000000
```
-S表示软性极限值,-N表示硬性极限值

终极解除Linux系统的最大文件打开数量的限制，可以通过编辑Linux的极限配置文件/etc/security/limits.conf来解决，修改此文件，加入如下内容：
```
soft nofile 1000000 # 表示软性极限
hard nofile 1000000 # 表示硬性极限
```

## 查看CPU核数相关信息

```shell
# 查看CPU信息（型号）
[root@AAA ~]# cat /proc/cpuinfo | grep name | cut -f2 -d: | uniq -c
     24         Intel(R) Xeon(R) CPU E5-2630 0 @ 2.30GHz
 
# 查看物理CPU个数
[root@AAA ~]# cat /proc/cpuinfo| grep "physical id"| sort| uniq| wc -l
2

# 查看每个物理CPU中core的个数(即核数)
[root@AAA ~]# cat /proc/cpuinfo| grep "cpu cores"| uniq
cpu cores    : 6

# 查看逻辑CPU的个数
[root@AAA ~]# cat /proc/cpuinfo| grep "processor"| wc -l

```

## 命令

```shell
# 查看进程中子线程
top -Hp [pid]
# 查看磁盘情况
df -hl
# 搜索文本 grep命令相关
grep [-acinv] [--color=auto] '搜寻字符串' filename
选项与参数：
-a:将 binary 文件以 text 文件的方式搜寻数据
-c:计算找到 '搜寻字符串' 的次数
-i:忽略大小写的不同，所以大小写视为相同
-n:顺便输出行号
-v:反向选择，亦即显示出没有 '搜寻字符串' 内容的那一行！
--color=auto:可以将找到的关键词部分加上颜色的显示喔！
```

