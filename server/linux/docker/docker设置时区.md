# 设置时区

### dockerFile文件中添加如下两句

```bash
RUN echo "Asia/Shanghai" > /etc/timezone
RUN dpkg-reconfigure -f noninteractive tzdata
```

### 运行中的容器

```bash
# 进入容器中
docker exec -it <CONTAINER NAME> bash
# 方法一 -> 执行以下
echo "Asia/Shanghai" > /etc/timezone
dpkg-reconfigure -f noninteractive tzdata
# 方法二  -> 
ln -sf /usr/share/zoneinfo/Asia/Shanghai /etc/localtime # 并且重启
```

### Docker run时指定时区

```
docker run -d <容器> -e TZ=Asia/Shanghai
```

