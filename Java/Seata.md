# Seata

## 相关概念

分布式事物就解决方案,提供了AT/TCC/SAGA/XA事物模式.

- TC(transaction coordinator): 事物协调器,维护全局事物的运行状态. 对应的是seata服务
- TM(transaction manager): 控制全局事物的边界,负责开启一个全局事物
- RM(resource manager): 控制分支事物,负责分支注册,状态汇报,与TC交谈,接受TC的提交或回滚操作