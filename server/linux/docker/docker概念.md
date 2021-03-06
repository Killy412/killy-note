# docker概念

## Docker镜像

Docker的镜像文件，相当于是一个只读层，不能往里面写入数据。我们可以通过编写dockerfile文件，定义需要安装的程序，比如说MySQL、Redis、JDK、Node.js等等，然后执行这个dockerfile文件，创建出镜像文件。

手写dockerfile还是比较麻烦的，所以我们可以在Docker的镜像仓库里面寻找别人已经创建好的镜像，比如说你想部署Java程序，那么就在线下载Java镜像即可，非常简单。

毕竟Docker镜像是只读层，如果我们想要往里面部署层序应该怎么办呢？这个很简单，我们可以给镜像创建一个容器，容器是可读可写的，我们把程序部署在容器里就行了。

## Docker容器

我们说的在Docker虚拟机中创建实例，指的就是容器。因为镜像的内容是只读的，想要部署程序，我们需要创建出容器实例，容器的内容是可以读写的，可以用来部署程序。

而且容器之间是完全隔离的，我们不用担心一个容器中部署程序，会影响到另一个容器。就比如说我们在CentOS上直接安装MySQL 8.0，它跟Percona Toolkit有冲突，跟Sysbench也有冲突，所以我们做在线修改表结构，以及做压力测试的时候，都是挑选新的虚拟机实例来安装这些程序，访问MySQL的。如果用上了Docker，我可以在A容器里安装MySQL，在B容器跑压力测试，根本不会有冲突。

再有，必须先有镜像，才能创建出容器，镜像和容器之间是关联的关系。而且一个镜像可以创建出多个容器，像是SaaS云计算，运营商可以把进销存系统打成镜像。有企业购买进销存系统，那么运营商就给客户创建一个容器，客户的进销存数据保存在容器A里面。再有客户购买进销存系统，运营商就创建容器B，以此类推。云计算服务商就是这么卖软件的。

## docker优点和缺点是什么

优点

- 资源隔离.
- 部署运行方便,统一环境.
- 一次编译,到处运行.

缺点:

## docker网络模式

- Bridge模式:docker进程启动时,会在主机上创建一个docker0的虚拟网桥,docker会连接网桥
- Host模式:不会创建一个network namespace,和宿主机共用一个
- Container模式:指定新创建的容器和已经存在的一个容器共享一个network
- None模式:拥有自己的network namespace,没有网卡ip路由等信息,需要自己配置

> 关于上述提到的三个网络解释如下：
>
> - Host：相当于Vmware中的桥接模式，与宿主机在同一个网络中，但没有独立的IP地址。众所周知，Docker使用了Linux的Namespaces技术来进行资源隔离，如PID Namespace隔离进程，Mount Namespace隔离文件系统，Network Namespace隔离网络等。一个Network Namespace提供了一份独立的网络环境，包括网卡、路由、Iptable规则等都与其他的Network Namespace隔离。一个Docker容器一般会分配一个独立的Network Namespace。但如果启动容器的时候使用host模式，那么这个容器将不会获得一个独立的Network Namespace，而是和宿主机共用一个Network Namespace。容器将不会虚拟出自己的网卡，配置自己的IP等，而是使用宿主机的IP和端口。基于Host模式启动的容器，在容器内执行ifconfig时，看到的都是宿主机上的信息。该模式不够灵活，容易出现端口冲突问题。
> - None：该模式将容器放置在它自己的网络栈中，但是并不进行任何配置。实际上，该模式关闭了容器的网络功能，类似于会换地址，在以下两种情况下是有用的：容器并不需要网络（例如只需要写磁盘卷的批处理任务）。
> - overlay：顾名思义：覆盖，但它又不是覆盖，它的作用就是在容器原有的网络基础之上，再添加一块网卡，并为其分配一个IP地址，可以将所有的docker容器关联到同一个局域网中，适用于容器与容器是跨主机进行通信的场景。
> - Bridge：相当于Vmware中的NAT模式，容器使用独立的network Namespace，并且连接到docker0虚拟网卡（默认模式）。通过docker网桥以及IPtables nat表配置与宿主机通信；Bridge模式是Docker默认的网络设置，此模式会为每一个容器分配一个Network nameSpace、设置IP等，并将一个主机上的Docker容器连接到一个虚拟网桥docker0上。