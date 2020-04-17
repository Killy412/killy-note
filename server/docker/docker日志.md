# 日志

### 切换成json-file驱动

- vim /etc/sysconfig/docker  编辑文件

  ```shell
  --log-driver=json-file
  ```

  

- 日志在/var/lib/docker/containers/容器id/容器id-json.log