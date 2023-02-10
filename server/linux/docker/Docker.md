# Docker

## 基础概念

### Docker镜像

Docker的镜像文件，相当于是一个只读层，不能往里面写入数据。我们可以通过编写Dockerfile文件，定义需要安装的程序，比如说MySQL、Redis、JDK、Node.js等等，然后执行这个dockerfile文件，创建出镜像文件。

手写dockerfile还是比较麻烦的，所以我们可以在Docker的镜像仓库里面寻找别人已经创建好的镜像，比如说你想部署Java程序，那么就在线下载Java镜像即可，非常简单。

毕竟Docker镜像是只读层，如果我们想要往里面部署层序应该怎么办呢？这个很简单，我们可以给镜像创建一个容器，容器是可读可写的，我们把程序部署在容器里就行了。

### Docker容器

我们说的在Docker虚拟机中创建实例，指的就是容器。因为镜像的内容是只读的，想要部署程序，我们需要创建出容器实例，容器的内容是可以读写的，可以用来部署程序。

而且容器之间是完全隔离的，我们不用担心一个容器中部署程序，会影响到另一个容器。就比如说我们在CentOS上直接安装MySQL8.0，它跟Percona Toolkit有冲突，跟Sysbench也有冲突，所以我们做在线修改表结构，以及做压力测试的时候，都是挑选新的虚拟机实例来安装这些程序，访问MySQL的。如果用上了Docker，我可以在A容器里安装MySQL，在B容器跑压力测试，根本不会有冲突。

再有，必须先有镜像，才能创建出容器，镜像和容器之间是关联的关系。而且一个镜像可以创建出多个容器，像是SaaS云计算，运营商可以把进销存系统打成镜像。有企业购买进销存系统，那么运营商就给客户创建一个容器，客户的进销存数据保存在容器A里面。再有客户购买进销存系统，运营商就创建容器B，以此类推。云计算服务商就是这么卖软件的。

### docker优点和缺点是什么

优点

- 资源隔离.
- 部署运行方便,统一环境.
- 一次编译,到处运行.

缺点:

- 隔离性: 多个容器共用宿主机的内核,隔离性不如虚拟机彻底

### Docker网络模式

- Bridge模式:docker进程启动时,会在主机上创建一个docker0的虚拟网桥,docker会连接网桥
- Host模式:不会创建一个network namespace,和宿主机共用一个
- Container模式:指定新创建的容器和已经存在的一个容器共享一个network
- None模式:拥有自己的network namespace,没有网卡ip路由等信息,需要自己配置

> 关于上述提到的三个网络解释如下：
>
> - Host：相当于Vmware中的桥接模式，与宿主机在同一个网络中，但没有独立的IP地址。众所周知，**Docker使用了Linux的Namespaces和cgroup技术来进行资源隔离**，如PID Namespace隔离进程，Mount Namespace隔离文件系统，Network Namespace隔离网络等。一个Network Namespace提供了一份独立的网络环境，包括网卡、路由、Iptable规则等都与其他的Network Namespace隔离。一个Docker容器一般会分配一个独立的Network Namespace。但如果启动容器的时候使用host模式，那么这个容器将不会获得一个独立的Network Namespace，而是和宿主机共用一个Network Namespace。容器将不会虚拟出自己的网卡，配置自己的IP等，而是使用宿主机的IP和端口。基于Host模式启动的容器，在容器内执行ifconfig时，看到的都是宿主机上的信息。该模式不够灵活，容易出现端口冲突问题。
> - None：该模式将容器放置在它自己的网络栈中，但是并不进行任何配置。实际上，该模式关闭了容器的网络功能，类似于会换地址，在以下两种情况下是有用的：容器并不需要网络（例如只需要写磁盘卷的批处理任务）。
> - overlay：顾名思义：覆盖，但它又不是覆盖，它的作用就是在容器原有的网络基础之上，再添加一块网卡，并为其分配一个IP地址，可以将所有的docker容器关联到同一个局域网中，适用于容器与容器是跨主机进行通信的场景。
> - Bridge：相当于Vmware中的NAT模式，容器使用独立的network Namespace，并且连接到docker0虚拟网卡（默认模式）。通过docker网桥以及IPtables nat表配置与宿主机通信；Bridge模式是Docker默认的网络设置，此模式会为每一个容器分配一个Network nameSpace、设置IP等，并将一个主机上的Docker容器连接到一个虚拟网桥docker0上。

##  安装Docker虚拟机

### redhat系列安装

```shell
yum install -y yum-utils # 安装依赖软件包
yum-config-manager --add-repo https://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo #添加软件仓库 阿里云docker安装包
yum makecache fast  # 启用缓存
yum list docker-ce --showduplicates | sort -r  # 查看版本 选择一个
yum install docker-ce-<version> -y        # 安装
systemctl enable docker # 开机自启
systemctl start docker  # 启动
systemctl stop docker  # 停止
```
### debian系列安装

```shell
apt update
# 安装所需依赖
apt install -y  apt-transport-https ca-certificates curl gnupg2 software-properties-common
curl -fsSL https://mirrors.ustc.edu.cn/docker-ce/linux/debian/gpg | apt-key add -
# 设置镜像仓库
echo "deb [arch=amd64] https://mirrors.ustc.edu.cn/docker-ce/linux/debian stretch stable" >>/etc/apt/sources.list
apt update
apt install -y docker-ce
systemctl start docker
systemctl enable docker
```

### 设置镜像加速器

```shell
curl -sSL https://get.daocloud.io/daotools/set_mirror.sh | sh -s http://f1361db2.m.daocloud.io
```

```shell
{"registry-mirrors":["https://docker.mirrors.ustc.edu.cn"]}
```
#### 新方法 编辑 /etc/docker/daemon.json
```shell
{
  "registry-mirrors": [
    "https://dockerhub.azk8s.cn",
    "https://reg-mirror.qiniu.com",
    "https://registry.docker-cn.com"
  ]
}
sudo systemctl daemon-reload  # 更新修改的配置文件
```

## 常用命令

- 管理镜像

```shell
#搜索镜像
docker search 关键字
#下载镜像
docker pull [选项] [Docker Registry 地址[:端口]/]仓库名[:标签]
docker pull 镜像名字
#查看镜像
docker images -a 
#重命名镜像
docker tag 旧镜像 新镜像
#删除镜像
docker rmi 镜像ID
#导出镜像
docker save -o 压缩文件路径 镜像名字
#导入镜像
docker load < 压缩文件路径
```

- 构建镜像
```shell
docker build [选项] <上下文路径>  # 构建镜像
# 示例
docker build -t nginx:3 .
```

- 创建容器

```shell
#查看运行的容器
docker ps 查看运行的容器
#创建普通容器
docker run -it --name 别名 镜像名字 程序名字
#创建含有端口映射的容器   -d{后台运行容器}
docker run -d --name 别名 -p 宿主机端口:容器端口 镜像名字 程序名字
#创建含有挂载目录的容器
docker run -d --name 别名 -v 宿主机目录:容器目录 --privileged 镜像名字 程序名字
#删除容器
docker rm -f 容器id
#######################操作容器
#暂停容器
docker pasue 容器
#恢复容器
docker unpause 容器
#停止容器
docker stop 容器
#启动容器
docker start -i 容器
docker rm -f xxx 删除容器 
docker restart 容器id
docker update --restart=always   #容器id  更新启动方式  docker启动容器就启动
                       =no  # 不自动启动
docker logs -f -t  <容器ID>  # 实时查看docker日志 -t 显示时间戳
docker logs --tail=20 <容器id>  # 查看20行日志
docker inspect [容器id]  # 查看容器信息
docker volume prune  # 清理多余的数据卷
# 拷贝docker日志
docker container cp [容器id]:[日志路径] [拷贝的目标路径]
# docker运行日志路径
docker inspect --format='{{.LogPath}}' [容器id]
# 日志copy
docker logs <options> [容器id] >& logs/myFile.log
```

- 进入容器

```shell
docker exec -it [容器id] bash
```
-  导出容器

```shell
docker export [容器ID] > [本地文件.tar]
```

- 删除none镜像
```shell
docker images|grep none|awk '{print $3}'|xargs docker rmi 
docker rmi $(docker images | grep "^<none>" | awk "{print $3}")
```

- 登录账号
```shell
# 阿里云镜像仓库登录
sudo docker login --username=killy412 registry.cn-beijing.aliyuncs.com
# 注销
docker logout
```

### 安装redis

- 拉取镜像

```shell
docker pull daocloud.io/library/redis:5.0.5
```

- 运行容器

```shell
docker run -p 6379:6379 --name redis -v /redis/redis.conf:/etc/redis/redis.config -v /redis/data:/data -d redis:5.0.5 redis-server --appendonly yes  --requirepass "1qaz@WSX" # 设定密码
```

### 安装mysql

- 拉取镜像

```shell
docker pull mysql:8.0.15
```
- 运行容器
```
docker run -p 3306:3306 --restart=always --name mysql -e MYSQL_ROOT_PASSWORD=1qaz@WSX -v /mysql/conf/my.cnf:/etc/my.cnf -d mysql:8.0.15 --default-authentication-plugin=mysql_native_password 
```
- 客户端连不上
1. select user, host, plugin from user;查询加密插件
2. ALTER USER 'root'@'%' IDENTIFIED WITH mysql_native_password BY 'password'; 修改加密方式

### 安装rabbitmq

- 运行容器
```shell
docker run -d --hostname rabbit-host --name rabbitmq --restart=always -e RABBITMQ_DEFAULT_USER=user -e RABBITMQ_DEFAULT_PASS=user -p 15672:15672 -p 5672:5672 rabbitmq:3.7.15-management
```
- --hostname：指定容器主机名称
- --name:指定容器名称
- -p:将mq端口号映射到本地

1. 进入容器 docker exec -it xxx bash
2. 添加用户
```cmd
rabbitmqctl add_user root 123456
```
3. 赋予权限
```shell
rabbitmqctl set_permissions -p / root ".*" ".*" ".*"
```
4. 赋予角色
```
rabbitmqctl set_user_tags root adminstrator
```
5. 查看用户列表以及角色

```
rabbitmqctl list_users
```

### 安装es

```shell
docker run -e ES_JAVA_OPTS="-Xms512m -Xmx512m" -d -p 9200:9200 -p 9300:9300 -v /etc/elsearch/elsearch.yml:/usr/share/elasticsearch/config/elasticsearch.yml --name es-master
elasticsearch:6.7.0
```

- 设置jvm线程数

```shell
vim /etc/sysctl.conf
# 添加这个
vm.max_map_count=262144 
# 保存后执行这个命令
sysctl -p
```

- 安装插件-

```shell
./bin/elasticsearch-plugin install https://github.com/medcl/elasticsearch-analysis-ik/releases/download/v6.7.0/elasticsearch-analysis-ik-6.7.0.zip
```

### 设置时区


#### dockerFile文件中添加如下两句

```bash
RUN echo "Asia/Shanghai" > /etc/timezone
RUN dpkg-reconfigure -f noninteractive tzdata
```

#### 运行中的容器

```bash
# 进入容器中
docker exec -it <CONTAINER NAME> bash
# 方法一 -> 执行以下
echo "Asia/Shanghai" > /etc/timezone
dpkg-reconfigure -f noninteractive tzdata
# 方法二  -> 
ln -sf /usr/share/zoneinfo/Asia/Shanghai /etc/localtime # 并且重启
```

#### Docker run时指定时区

```
docker run -d <容器> -e TZ=Asia/Shanghai
```

## 其它扩展

- [Docker Compose](./docker%20compose.md)
- [Dockerfile文件详解](./Dockerfile%E8%AF%A6%E8%A7%A3.md)
- [IDEA打包jar部署docker](./idea-docker.md)
- [Kubernetes介绍](./Kubernetes.md)