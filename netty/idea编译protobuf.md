# idea编译protobuf文件

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

### 在target->generated-sources->protobuf文件中

