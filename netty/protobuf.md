# protobuf笔记

## 简介
> Protobuf是一种平台无关、语言无关、可扩展且轻便高效的序列化数据结构的协议，可以用于网络通信和数据存储。

- 可扩展的数据结构的协议
- 和xml/json类似,主要用于数据存储,传输协议格式等场合

```
syntax = "proto3";

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
2. 分配标识号
3. 指定字段规则,字段修饰符必须是如下之一
	- required 表示该值必须包含
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