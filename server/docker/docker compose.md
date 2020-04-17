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

