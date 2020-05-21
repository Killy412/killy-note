# spring-actuator2.0笔记

## 暴露端点

由于端点可能包含敏感信息，因此应仔细考虑何时公开它们。下表显示了内置端点的默认曝光：

| 路径                                                         | JMX                                                 | Web   |
| ------------------------------------------------------------ | --------------------------------------------------- | ----- |
| auditevents:显示当前应用程序的审计事件信息                                  | Yes                                                 | No    |
| beans:显示一个应用中`所有Spring Beans`的完整列表                           | Yes                                                 | No    |
| conditions:提供一份自动配置生效的条件情况，记录哪些自动配置条件通过了，哪些没通过 | Yes                                                 | No    |
| configprops:描述配置属性(包含默认值)如何注入Bean | Yes                                                 | No    |
| env:获取全部环境属性         | Yes                                                 | No    |
| flyway:显示数据库迁移路径，如果有的话                                       | Yes                                                 | No    |
| health(显示应用的`健康信息`（当使用一个未认证连接访问时显示一个简单)的’status’，使用认证连接访问则显示全部信息详情） | Yes | Yes |
| heapdump:dump 一份应用的 JVM 堆信息                                  | N/A                                                 | No    |
| httptrace:显示HTTP足迹，最近100个HTTP request/repsponse              | Yes                                                 | No    |
| info:显示任意的`应用信息`                                             | Yes                                                 | `Yes` |
| jolokia                                                      | Yes                                                 | No    |
| logfile:返回log file中的内容(如果 logging.file 或者 logging.path 被设置)  | Yes                                                 | No    |
| loggers:显示和修改配置的loggers                                      | Yes                                                 | No    |
| liquibase:展示任何Liquibase数据库迁移路径，如果有的话                         | Yes                                                 | No    |
| metrics:报告各种应用程序度量信息，比如内存用量和HTTP请求计数       | Yes                                                 | No    |
| mappings:显示一个所有`@RequestMapping`路径的集合列表                      | Yes                                                 | No    |
| prometheus                                                   | N/A                                                 | No    |
| scheduledtasks:显示应用程序中的`计划任务`                                | Yes                                                 | No    |
| sessions:如果我们使用了 Spring Session 展示应用中的 HTTP sessions 信息 | Yes                                                 | No    |
| shutdown:允许应用以优雅的方式关闭（默认情况下不启用）                              | Yes                                                 | No    |
| threaddump:执行一个线程dump                                      | Yes                                                 | No    |

可以用来选择所有端点。例如，要通过HTTP公开除env和beans端点之外的所有内容，请使用以下属性：

```
management.endpoints.web.exposure.include=*
management.endpoints.web.exposure.exclude=env,beans
```

###  健康信息

您可以使用健康信息来检查正在运行的应用程序的状态。当生产系统停机时，它经常被监控软件用来提醒某人。`health`端点公开的信息取决于`management.endpoint.health.show-details`属性，该属性可以使用以下值之一进行配置：

| Name            | Description                                                  |
| --------------- | ------------------------------------------------------------ |
| never           | 细节永远不会显示。                                           |
| when-authorized | 详细信息仅向授权用户显示。授权角色可以使用`management.endpoint.health.roles`进行配置。 |
| always          | 详细信息显示给所有用户。                                     |

### 自定义应用信息

您可以通过设置`info.*` Spring属性来自定义`info`端点公开的数据。`info`key下的所有`Enviroment`属性都会自动公开。例如，您可以将以下设置添加到您的`application.yaml`文件中：

```yaml
info:
  app:
    name: @project.name@
    version: @project.version@
    env: @profileActive@
    desc: @project.description@
```

### 打开端点

可以打开所有的监控点

```
management:
  endpoints:
    web:
      exposure:
        include: '*'
```

也可以选择打开部分

```
management.endpoints.web.exposure.exclude=beans,trace
```

Actuator 默认所有的监控点路径都在`/actuator/*`，当然如果有需要这个路径也支持定制。

```
management.endpoints.web.base-path=/manage
```