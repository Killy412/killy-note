# idea docker插件使用部署springboot项目

## docker运行springboot项目
* docker开启远程访问
```shell
[root@izwz9eftauv7x69f5jvi96z docker]# vim /usr/lib/systemd/system/docker.service
#修改ExecStart这行
ExecStart=/usr/bin/dockerd  -H tcp://0.0.0.0:2375  -H unix:///var/run/docker.sock
#重新加载配置文件
[root@izwz9eftauv7x69f5jvi96z docker]# systemctl daemon-reload    
#重启服务
[root@izwz9eftauv7x69f5jvi96z docker]# systemctl restart docker.service 
#查看端口是否开启
[root@izwz9eftauv7x69f5jvi96z docker]# netstat -nptl
#直接curl看是否生效
[root@izwz9eftauv7x69f5jvi96z docker]# curl http://127.0.0.1:2375/info
```

* 在pom文件中添加Docker镜像前缀名
```xml
<properties>
    <docker.image.prefix>springboot</docker.image.prefix>
</properties>
```
* plugins中添加Docker插件

```xml
<plugin>
    <groupId>com.spotify</groupId>
    <artifactId>docker-maven-plugin</artifactId>
    <version>1.0.0</version>
    <configuration>
        <imageName>${docker.image.prefix}/${project.artifactId}</imageName> #镜像名
        <dockerDirectory>src/main/docker</dockerDirectory>
        <dockerHost>http://47.103.10.118:2375</dockerHost>
        <resources>
            <resource>
                <targetPath>/</targetPath>
                <directory>${project.build.directory}</directory> #jar包所在目录，缺省为target
                <include>${project.build.finalName}.jar</include> #jar包名，缺省为 ${project.artifactId}-${project.version}
            </resource>
        </resources>
    </configuration>
</plugin>
```

* 在目录/src/main/docker/下创建dockerfile文件,用来说明如何构建镜像

```shell
#指定基础镜像，在其上进行定制
FROM java:8

#维护者信息
MAINTAINER chuanfu <lxq413@126.com>

#这里的 /tmp 目录就会在运行时自动挂载为匿名卷，任何向 /data 中写入的信息都不会记录进容器存储层
VOLUME /tmp

#
ADD ./him-api-1.0.jar app.jar
#声明运行时容器提供服务端口，这只是一个声明，在运行时并不会因为这个声明应用就会开启这个端口的服务
EXPOSE 9000
EXPOSE 9001
# 设置东八区时区
ENV TZ=Asia/Shanghai
RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone
#指定容器启动程序及参数   <ENTRYPOINT> "<CMD>"
ENTRYPOINT ["java","-jar","app.jar"]
```

- maven插件构建镜像

```shell
# 本地多模块情况先执行父类打包
mvn package install
# 发布镜像
mvn package docker:build
```
- 查看服务器镜像
```shell
docker images
```

- 项目编译为docker镜像,首先把jar包和Dockerfile放在目录中,然后在目录下执行命令:

```shell
docker build -t moqu-gateway:1.0 .
```

- 运行容器

```shell
docker run -d --name him-api -p 9000:9000 --restart=always -e "SPRING_PROFILES_ACTIVE=dev" springboot/him-api:latest
```



