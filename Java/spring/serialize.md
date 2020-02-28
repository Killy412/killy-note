# spring序列化

> 在 jackson 自定义序列化中提供了两个抽象类，com.fasterxml.jackson.databind.JsonSerializer和com.fasterxml.jackson.databind.ser.std.StdSerializer。一般，我们可以直接继承JsonSerializer抽象类就已经足够满足我们的自定义序列化需求了。

##### 自定义序列化注册Jackson

- SimpleModule
- @JsonSerialize(推荐)
- CustomSerializerFactory(新版已删)