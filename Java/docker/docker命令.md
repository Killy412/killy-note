# docker笔记

##  如何使用Docker虚拟机

#### Docker镜像

Docker的镜像文件，相当于是一个只读层，不能往里面写入数据。我们可以通过编写dockerfile文件，定义需要安装的程序，比如说MySQL、Redis、JDK、Node.js等等，然后执行这个dockerfile文件，创建出镜像文件。

手写dockerfile还是比较麻烦的，所以我们可以在Docker的镜像仓库里面寻找别人已经创建好的镜像，比如说你想部署Java程序，那么就在线下载Java镜像即可，非常简单。

毕竟Docker镜像是只读层，如果我们想要往里面部署层序应该怎么办呢？这个很简单，我们可以给镜像创建一个容器，容器是可读可写的，我们把程序部署在容器里就行了。

####  Docker容器

我们说的在Docker虚拟机中创建实例，指的就是容器。因为镜像的内容是只读的，想要部署程序，我们需要创建出容器实例，容器的内容是可以读写的，可以用来部署程序。

而且容器之间是完全隔离的，我们不用担心一个容器中部署程序，会影响到另一个容器。就比如说我们在CentOS上直接安装MySQL 8.0，它跟Percona Toolkit有冲突，跟Sysbench也有冲突，所以我们做在线修改表结构，以及做压力测试的时候，都是挑选新的虚拟机实例来安装这些程序，访问MySQL的。如果用上了Docker，我可以在A容器里安装MySQL，在B容器跑压力测试，根本不会有冲突。

再有，必须先有镜像，才能创建出容器，镜像和容器之间是关联的关系。而且一个镜像可以创建出多个容器，像是SaaS云计算，运营商可以把进销存系统打成镜像。有企业购买进销存系统，那么运营商就给客户创建一个容器，客户的进销存数据保存在容器A里面。再有客户购买进销存系统，运营商就创建容器B，以此类推。云计算服务商就是这么卖软件的。

####  安装Docker虚拟机

```shell
yum install -y docker
```

```shell
service docker start
```

```shell
service docker stop
```

####  操作Docker虚拟机

![](C:/Users/lxq41/AppData/Roaming/Typora/draftsRecover/笔记图片/1.png)

##### 设置镜像加速器

```shell
curl -sSL https://get.daocloud.io/daotools/set_mirror.sh | sh -s http://f1361db2.m.daocloud.io
```

编辑/etc/docker/daemon.json文件，把结尾的逗号去掉

#### 管理镜像

```shell
#搜索镜像
docker search 关键字
#下载镜像
docker pull 镜像名字
#查看镜像
docker images
#重命名镜像
docker tag 旧镜像 新镜像
#删除镜像
docker rmi 镜像名字
#导出镜像
docker save -o 压缩文件路径 镜像名字
#导入镜像
docker load < 压缩文件路径
```

#### 创建容器

```shell
#创建普通容器
docker run -it --name 别名 镜像名字 程序名字
#创建含有端口映射的容器
docker run -it --name 别名 -p 宿主机端口:容器端口 镜像名字 程序名字
#创建含有挂载目录的容器
docker run -it --name 别名 -v 宿主机目录:容器目录 --privileged 镜像名字 程序名字
```

#### 操作容器状态

```shell
#暂停容器
docker pasue 容器
#恢复容器
docker unpause 容器
#停止容器
docker stop 容器
#启动容器
docker start -i 容器
```



```shell
yum install -y docker
```

```shell
service docker start
```

```shell
service docker stop
```

##### 设置镜像加速器

```shell
编辑/etc/docker/daemon.json文件{"registry-mirrors":["https://docker.mirrors.ustc.edu.cn"]}BashCC++C#CSSGoHaskellHTMLJavaJavaScriptJSONJSXkotlinPHPPowerShellPythonRubyRustSQLSwiftTypeScriptXML
```

#### 管理镜像

```shell
#搜索镜像
docker search 关键字
#下载镜像
docker pull 镜像名字
#查看镜像
docker images
#重命名镜像
docker tag 旧镜像 新镜像
#删除镜像
docker rmi 镜像名字
#导出镜像
docker save -o 压缩文件路径 镜像名字
#导入镜像
docker load < 压缩文件路径
```

#### 创建容器

```shell
#查看运行的容器
docker ps 查看运行的容器
#创建普通容器
docker run -it --name 别名 镜像名字 程序名字
#创建含有端口映射的容器   -d{后台运行容器}
docker run -it --name 别名 -p 宿主机端口:容器端口 镜像名字 程序名字
#创建含有挂载目录的容器
docker run -it --name 别名 -v 宿主机目录:容器目录 --privileged 镜像名字 程序名字
```

#### 操作容器状态

```shell
#暂停容器
docker pasue 容器
#恢复容器
docker unpause 容器
#停止容器
docker stop 容器
#启动容器
docker start -i 容器
```

### 进入容器中

```shell
docker exec -it [容器id] bash
```

## docker安装es

```shell
docker run -e ES_JAVA_OPTS="-Xms512m -Xmx512m" -d -p 9200:9200 -p 9300:9300 -v /etc/elsearch/elsearch.yml:/usr/share/elasticsearch/config/elasticsearch.yml --name es-master
elasticsearch:6.7.0
```

### 设置jvm线程数

```shell
vim /etc/sysctl.conf
# 添加这个
vm.max_map_count=262144 
# 保存后执行这个命令
sysctl -p
```

### 安装插件-

```shell
./bin/elasticsearch-plugin install https://github.com/medcl/elasticsearch-analysis-ik/releases/download/v6.7.0/elasticsearch-analysis-ik-6.7.0.zip
```


## docker安装redis

#### 拉取镜像

```shell
docker pull daocloud.io/library/redis:3.2.9
```

#### 运行容器

```shell
docker run -p 6379:6379 --name redis -v /redis/redis.conf:/etc/redis/redis.confi -v /redis/data:/data -d  redis:3.2.9 redis-server --appendonly yes
```
