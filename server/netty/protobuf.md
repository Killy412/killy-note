# protobuf笔记

## 简介
> Protobuf是一种平台无关、语言无关、可扩展且轻便高效的序列化数据结构的协议，可以用于网络通信和数据存储。

- 可扩展的数据结构的协议
- 和xml/json类似,主要用于数据存储,传输协议格式等场合

```protobuf
syntax = "proto3";
option java_package = "";
message SearchRequest {
  string query = 1;
  int32 page_number = 2;
  int32 result_per_page = 3;
}
```

> syntax指定语法版本


## 基础
### 消息定义
1. 指定字段类型
- 数字类型:**double、float、int32、int64、uint32、uint64、sint32、sint64**,存储长度可变的浮点数,整型,无符号整数,有符号整数
- 固定长度的数字类型: **fixed32、fixed64、sfixed32、sfixed64**
- 布尔类型 : **bool**
- 字符串 : string
- 字节数组 bytes
- 消息类型 **messageType**
- 枚举类型 **enumType**
2. 分配标识号
3. 指定字段规则,字段修饰符必须是如下之一
	- required 表示该值必须包含 **proto3中去掉了该选项**
	- optional 该字段必须有0/1个值,不超过一个,默认值选项:**[default = 10]**
	- repeated  表示该字段可以被重复任意次,类比Java中的list,使用特殊选项**[packed=true]**保证编码高效
4. 定义枚举
	- 定义别名: **option allow_alias = true;**
	- 枚举常量必须事32位整型值范围内

### 导入定义
- 可以使用import语句导入其他文件声明的类型
```protobuf
import "other.proto";
```

### message嵌套
- 内部嵌套在内部可以直接使用,在外部需要指定父级message的名称,例:**Result.Type**

### map类型
```protobuf
map<key_type,value_type> map_field=N;
message Project{}
map<string,Project> projects=1;
```
- 键值类型可以是内置标量类型,也可以自定义
- 字段不支持repeated属性

## 定义服务(Service)

> 如果想要将消息类型用在RPC(远程方法调用)系统中，可以在.proto文件中定义一个RPC服务接口，protocol buffer编译器会根据所选择的不同语言生成服务接口代码。
> 
```protobuf
service SearchService {
    rpc Search (SearchRequest) returns (SearchResponse) {}
}
```

##  idea编译protobuf文件

### maven插件编译

1. properties标签添加

```xml
<properties>
    <protobuf.version>3.5.1</protobuf.version>
    <grpc.version>1.6.1</grpc.version>
</properties>
```

2. 依赖中添加

```xml
<dependency>
    <groupId>com.google.protobuf</groupId>
    <artifactId>protobuf-java</artifactId>
    <version>${protobuf.version}</version>
</dependency>
```

3. <build>标签中添加

```xml
<extensions>
    <extension>
        <groupId>kr.motd.maven</groupId>
        <artifactId>os-maven-plugin</artifactId>
        <version>1.5.0.Final</version>
    </extension>
</extensions>
```

4. 添加插件

```xml
<plugin>
    <groupId>org.xolstice.maven.plugins</groupId>
    <artifactId>protobuf-maven-plugin</artifactId>
    <version>0.5.0</version>
    <configuration>
<protocArtifact>com.google.protobuf:protoc:${protobuf.version}:exe:${os.detected.classifier}
        </protocArtifact>
        <pluginId>grpc-java</pluginId>
        <pluginArtifact>io.grpc:protoc-gen-grpc-java:${grpc.version}:exe:${os.detected.classifier}
        </pluginArtifact>
    </configuration>
    <executions>
        <execution>
            <goals>
                <goal>compile</goal>
                <goal>compile-custom</goal>
            </goals>
        </execution>
    </executions>
</plugin>
```

5. 执行命令

```shell
mvn compile
```

>
> 在target->generated-sources->protobuf文件中

### 命令行编译

```
D:>protoc-3.5.1-windows-x86_64.exe --proto_path F:\ideaproject\kylin-kylin-2.6.0\storage-hbase\src\main\java\org\apache\kylin\storage\hbase\cube\v2\coprocessor\endpoint\protobuf --java_out ./ CubeVisit1.proto
```

- protoc，编译命令；
- --proto_path,就是你的proto文件所在目录是哪。我这里是F:\ideaproject\kylin-kylin-2.6.0\storage-hbase\src\main\java\org\apache\kylin\storage\hbase\cube\v2\coprocessor\endpoint\protobuf。
- --java_out，标识输出的java文件应该放在哪个目录。这里的 ./ 是指当前目录。
- protoDemo.proto，就是我们要编译的文件

