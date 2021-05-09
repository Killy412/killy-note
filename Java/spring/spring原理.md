# spring相关知识点

## 控制反转(IOC)和依赖注入(DI)怎么理解?

### 控制反转

ioc是一种设计思想,将原本需要在程序中创建对象的控制权,交给spring框架管理
将对象之间的依赖关系交给ioc管理,并由ioc完成属性的注入

### 依赖注入


### AOP

### spring bean线程安全的问题

正常单例bean是无状态的. 因此是线程安全的. bean如果有成员变量会存在线程安全的问题. 常用两种解决方法

1. 存储在ThreadLocal变量中
2. 更改bean的作用域为property,每次请求都会创建新的bean.

### 过滤器filter,拦截器 interceptor,aop的区别和联系

> 过滤器基于url,可以拦截所有的请求,控制最初的http请求
>
> 拦截器,是基于反射机制实现,不依赖servlet.
>
> aop只能拦截spring管理的bean实例

###  springmvc流程

1. 客户端发送请求,直接请求到dispatcherservlet
2. DispatcherServlet根据请求信息调用HandlerMapping,解析请求对应的Handler
3. 解析到对应的Handler之后,开始由HandlerAdapter适配器处理.
4. HandlerAdapter会根据Handler来调用真正的处理器来处理请求,并处理对应的业务逻辑
5. 处理器处理完成之后,会返回一个ModelAndView对象,Model是返回的数据对象,View是逻辑上的View
6. ViewResolver会根据逻辑View查找真正的View
7. DispatcherServlet会把model传给view进行视图渲染
8. 返回给客户端

### spring容器启动

1. 创建一个ApplicationContext
2. 读取所有的Bean定义并加载
   - 先加载一个bean (**ConfigurationClassPostProcessor**)
   - 由**ConfigurationClassPostProcessor** 扫描其他的Bean定义
     - 扫描注解或者XML
     - BeanDefinition 资源定位
       - ResourceLoader 加载
3. 按照Bean的定义创建Bean
4. 一些Bean生命周期的加载,例如PostProcessor 

### Spring Aop实现原理

通过动态代理(jdk动态代理或者cglib动态代理) 增强需要增强的类,生成字节码

### springBean 生命周期

1. 实例化Bean: 容器调用createBean()进行实例化.
2. 设置对象属性(依赖注入): 
   - 实例化后的对象被封装在BeanWrapper对象中,Spring根据BeanDefinition中的信息以及通过BeanWrapper提供的设置属性的接口完成依赖注入.
3. 处理xxxAware接口: spring检测该对象是否实现了xxxAware接口,并将相关的xxAware属性注入给bean
   - BeanNameAware: **setBeanName()方法**
   - BeanFactoryAware: **setBeanFactory()方法**
   - ApplicationContextAware: **setApplicationContext()方法**
4. BeanPostProcessor: **调用postProcessBeforeInitialization(Object o,String s)方法**
5. InitializingBean和init-method
   - 如果bean在spring配置文件中配置了init-method属性,会调用其配置的方法
6. BeanPostProcessor: **调用postProcessAfterInitialization(Object o,String s)方法**
7. DisposableBean: bean不需要的时候,会经过清理阶段,bean实现此接口,会调用**destory()**方法
8. destory-method:  如果bean在spring配置文件中配置了destory-method属性,会调用配置的销毁方法