
### 安装步骤

环境为Ubuntu 24
参考自[!K8S学习路径] https://github.com/caicloud/kube-ladder

1. 安装kubectl

``` 
curl -LO https://storage.googleapis.com/kubernetes-release/release/$(curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt)/bin/linux/amd64/kubectl && chmod +x kubectl && sudo mv kubectl /usr/local/bin/
 ```

上面命令可能失败，就分开几次执行

```
 curl -LO "https://storage.googleapis.com/kubernetes-release/release/$(curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt)/bin/linux/amd64/kubectl"
 chmod +x kubectl
 sudo mv kubectl /usr/local/bin/
```

这样可以正常使用kubectl命令了

2. 安装kvm2
```
 sudo apt-get install qemu-kvm libvirt-daemon-system libvirt-clients bridge-utils
```

3. 安装 minikube 需要科学上网，主要用于本地部署kubernetes集群
```
curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64 && sudo install minikube-linux-amd64 /usr/local/bin/minikube
```

启动
```
minikube start
# 有时配置更新之后，某个服务有问题，可以停止之后再启动
minikube stop
```

验证是否安装成功

```
minikube kubectl get nodes
```

需要docker登陆

```
sudo docker login --username=killy412 registry.cn-hangzhou.aliyuncs.com
```

创建depoyonment失败，提示登录失败，需要设置代理
```
vim /usr/lib/systemd/system/docker.service
[Service]
Environment="HTTP_PROXY=http://{ipv4}:7890"
Environment="HTTPS_PROXY=http://{ipv4}:7890"
Environment="NO_PROXY=localhost,127.0.0.1"


systemctl daemon-reload
systemctl restart docker
```

上述方法可以解决docker login 的问题，但是启动delopment时依然拉取不到镜像，代理的问题
最后解决方法是代理地址不用127.0.0.1，在clash中允许0.0.0.0访问，配置成本机ipv4地址


