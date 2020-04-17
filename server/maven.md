# maven

### maven安装
1. 下载maven包

```
wget http://mirror.bit.edu.cn/apache/maven/maven-3/3.6.3/binaries/apache-maven-3.6.3-bin.tar.gz
```

2. 解压
3. 配置
	* 修改conf文件下setting.xml,添加阿里云maven镜像
    ```xml
    <mirror> 
         <id>alimaven</id> 
         <name>aliyun maven</name> 
         <url>http://maven.aliyun.com/nexus/content/groups/public/</url> 
         <mirrorOf>central</mirrorOf> 
    </mirror>  
    ```
    * 配置环境变量 /etc/profile
    ```shell
    #maven
    M2_HOME=/usr/local/dev/maven/apache-maven-3.6.3
    export M2_HOME
    PATH=${PATH}:${M2_HOME}/bin
    export PATH
    ```
    * 刷新环境变量
    ```shell
    source /etc/profile
    ```

### maven打包
- 多模块pom,指定模块打包
	+ -am：如果 pl 参数存在，就同时构建 pl 参数所列模块依赖的其他模块
	+ -amd：如果 pl 参数存在，就同时构建依赖于 pl 参数所列模块的其他模块
	+ -pl：构建指定模块，多个模块用逗号（,）分隔
	```shell
	mvn clean package -pl [pom-name] -am -Dmaven.test.skip=true -P pro
	```
