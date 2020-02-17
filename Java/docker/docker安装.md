# docker安装

##  安装Docker虚拟机

```shell
yum install -y docker 安装docker
service docker start docker服务启动
service docker stop docker服务停止
```

#### 设置镜像加速器

```shell
curl -sSL https://get.daocloud.io/daotools/set_mirror.sh | sh -s http://f1361db2.m.daocloud.io
```

```shell
{"registry-mirrors":["https://docker.mirrors.ustc.edu.cn"]}
BashCC++C#CSSGoHaskellHTMLJavaJavaScriptJSONJSXkotlinPHPPowerShellPythonRubyRustSQLSwiftTypeScriptXML

## 新方法 编辑 /etc/docker/daemon.json
{
  "registry-mirrors": [
    "https://dockerhub.azk8s.cn",
    "https://reg-mirror.qiniu.com",
    "https://registry.docker-cn.com"
  ]
}
```

编辑/etc/docker/daemon.json文件，把结尾的逗号去掉

- 管理镜像

```shell
#搜索镜像
docker search 关键字
#下载镜像
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
-  创建容器

```shell
#查看运行的容器
docker ps 查看运行的容器
#创建普通容器
docker run -it --name 别名 镜像名字 程序名字
#创建含有端口映射的容器   -d{后台运行容器}
docker run -it --name 别名 -p 宿主机端口:容器端口 镜像名字 程序名字
#创建含有挂载目录的容器
docker run -it --name 别名 -v 宿主机目录:容器目录 --privileged 镜像名字 程序名字
#删除容器
docker rm -f 容器id
```

- 操作容器状态

```shell
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
docker update --restart=always 容器id  更新启动方式
docker logs -f -t --tail 行数 容器ID  实时查看docker日志
```

- 进入容器

```shell
docker exec -it [容器id] bash
```

## docker安装es

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


## docker安装redis

- 拉取镜像

```shell
docker pull daocloud.io/library/redis:3.2.9
```

- 运行容器

```shell
docker run -p 6379:6379 --name redis -v /redis/redis.conf:/etc/redis/redis.confi -v /redis/data:/data -d  redis:3.2.9 redis-server --appendonly yes
```

## docker 安装mysql
- 拉取镜像

```shell
docker pull mysql:8.0.15qaz@WSX
```
- 运行容器
```
docker run -p 3306:3306 --restart=always --name mysql -e MYSQL_ROOT_PASSWORD=1qaz@WSX -v /mysql/conf/my.cnf:/etc/my.cnf -d mysql:8.0.15 --default-authentication-plugin=mysql_native_password 
```
>

- 客户端连不上
1. select user, host, plugin from user;查询加密插件
2. ALTER USER 'root'@'%' IDENTIFIED WITH mysql_native_password BY 'password'; 修改加密方式

## docker安装rabbitmq
- 运行容器
```shell
docker run -d --hostname rabbit-host --name rabbitmq -e RABBITMQ_DEFAULT_USER=user -e RABBITMQ_DEFAULT_PASS=1qaz@WSX
-p 15672:15672 -p 5672:5672 rabbitmq:3.7.15-management
```

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

# **服务器重启,使用容器重启命令**

