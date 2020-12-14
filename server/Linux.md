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



## 文件结构

系统安装软件一般在/usr/share，
可执行的文件在/usr/bin，
配置文件可能安装到了/etc下等
	/etc/systemd/system/  # 用户自定义启动文件
文档一般在 /usr/share
lib文件 /usr/lib
	/usr/lib/systemd/system/  # 系统启动文件

### 正在运行的前台任务改为后台任务

首先在前台任务按　Ctrl+Z ,然后执行　```bg```命令

#### 查询子线程数量

```shell
pstree -p <pid>
top -Hp <pid>       //下面结果中的Tasks 对应的47即是线程的个数
ps hH p 19135| wc -l
```