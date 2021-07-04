# docker三架马车

### docker-compose
docker-compose是docker官方编排的项目之一,负责快速的部署分布式应用,定位是定义和运行多个docker容器的应用

两个重点

- docker-compose.yaml  compose文件
- docker-compose命令行工具

两个重要概念

- 服务(service):一个应用的容器,可以包括若干运行相同镜像的容器实例
- 项目(project): 由一组关联的应用容器组成的一个完整业务单元在docker-compose.yaml文件中定义

compose默认管理的是项目

### docker machine
官方编排项目之一,负责在多种平台上快速安装docker环境

### docker swarm
使用swarmKit构建的Docker引擎内置的集群管理和编排工具,提供docker集群服务,和k8s,mesos类似

### 版本

| **Compose file format** | **Docker Engine release** |
| :---------------------- | :------------------------ |
| 3.8                     | 19.03.0+                  |
| 3.7                     | 18.06.0+                  |
| 3.6                     | 18.02.0+                  |
| 3.5                     | 17.12.0+                  |
| 3.4                     | 17.09.0+                  |
| 3.3                     | 17.06.0+                  |
| 3.2                     | 17.04.0+                  |
| 3.1                     | 1.13.1+                   |
| 3.0                     | 1.13.0+                   |
| 2.4                     | 17.12.0+                  |
| 2.3                     | 17.06.0+                  |
| 2.2                     | 1.13.0+                   |
| 2.1                     | 1.12.0+                   |
| 2.0                     | 1.10.0+                   |
| 1.0                     | 1.9.1.+                   |