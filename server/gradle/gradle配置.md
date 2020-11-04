### 环境配置

下载地址 `https://gradle.org/releases/`

1. 解压缩,配置环境变量

   ```shell
   vim /etc/profile
   
   export GRADLE_HOME="/usr/local/gradle"
   export PATH=$GRADLE_HOME/bin:$PATH
   export GRADLE_USER_HOME=$GRADLE_HOME/repo  #设置仓库路径
   
   source /etc/profile
   
   gradle -v #验证
   ```

   

2. 设置gradle使用maven仓库

   - 将<maven安装路径>/conf/settings.xml拷贝到用户目录的.m2目录下

   - sudo vi /usr/local/gradle/init.d/init.gradle

   - 添加

     ```
     allprojects {
         repositories {
             mavenLocal()
             maven { name "Alibaba" ; url "https://maven.aliyun.com/repository/public" }
             mavenCentral()
         }
     }
     ```

     注意:因为gradle的jar包路径和maven的jar包路径不一致,所以实际上使用了mavenLocal()后,如果maven仓库中有的jar包,gradle仅仅是不需要下载,会将maven仓库中的jar包拷贝到gradle的仓库中.

