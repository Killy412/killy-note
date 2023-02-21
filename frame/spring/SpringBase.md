# spring相关知识点

## 控制反转(IOC)和依赖注入(DI)怎么理解?

### 控制反转

ioc是一种面向对象的设计思想,将原本需要在程序中创建对象的控制权,交给spring框架管理
将对象之间的依赖关系交给ioc管理,并由ioc完成属性的注入

### 依赖注入

指的是容器在实例化的时候把它依赖的对象注入给他。IOC是思想，DI是IOC的实现。
主要是为了对象之间的解藕，硬编码会造成对象之间的过度耦合。

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

### Spring Aop实现原理

通过动态代理(jdk动态代理或者cglib动态代理) 增强需要增强的类,生成字节码

> 原理: 用代理类包裹切面,织入spring管理的bean中,
>
> 动态代理实现
>
> spring aop动态代理实现方式有两种,jdk动态代理和cglib动态代理
>
> - jdk动态代理通过反射来生成目标代理接口的匿名实现类.
> - cglib动态代理:如果目标类没有实现接口,springaop会使用cglib动态代理目标类.可以在运行时生成某个类的子类,通过继承的方式做的动态代理,如果某个类时final,无法使用cglib动态代理

### BeanFactory和ApplicatContext 区别

BeanFactory是Spring框架的基础，可以创建并管理各种类的对象，面向Spring本身。DefaultListableBeanFactory是常用的BeanFactory的实现类
ApplicantContext建立在BeanFactory的基础上，面向Spring的开发者。由BeanFactory派生而来，提供了更多面向实际应用的功能。

### Spring容器启动

1. 创建一个ApplicationContext
2. 读取所有的Bean定义并加载
   - 先加载一个bean (**ConfigurationClassPostProcessor**)
   - 由**ConfigurationClassPostProcessor** 扫描其他的Bean定义
     - 扫描注解或者XML
     - BeanDefinition 资源定位
       - ResourceLoader 加载
3. 按照Bean的定义创建Bean
4. Bean生命周期的加载,例如PostProcessor前后处理

###  SpringMvc流程

1. 客户端发送请求,直接请求到dispatcherservlet
2. DispatcherServlet根据请求信息调用HandlerMapping,解析请求对应的Handler
3. 解析到对应的Handler之后,开始由HandlerAdapter适配器处理.
4. HandlerAdapter会根据Handler来调用真正的处理器来处理请求,并处理对应的业务逻辑
5. 处理器处理完成之后,会返回一个ModelAndView对象,Model是返回的数据对象,View是逻辑上的View
6. ViewResolver会根据逻辑View查找真正的View
7. DispatcherServlet会把model传给view进行视图渲染
8. 返回给客户端

### Spring Bean的生命周期

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

### Spring事物实现的方式

1. 编程式事物,需要在代码中调用beginTransaction,commit,rollback等事物相关的方法
2. 基于注解@Transaction的声明式事物

### Spring解决循环依赖的方式

Bean创建的过程有三步，`1.创建对象 2.属性赋值 3.初始化`。Spring通过三级缓存解决循环依赖。
1. `singletonObjects` 一级缓存，存储所有初始化完成的单例Bean
2. `earlySingletonObjects` 二级缓存，早期曝光对象，存储创建好，但是没有进行属性赋值以及初始化的单例Bean
3. `singletonFactories` 三级缓存，早期曝光工厂，二级缓存中存储的就是从这个工厂里获取的对象
当A/B两个类发生循环依赖时，在A完成实例化对象后，会根据实例化之后的A创建一个对象工厂，并放入到三级缓存中，如果A被AOP代理，通过对象工厂获得的对象就是代理之后的对象，如果没有被AOP代理，获取的就是实例化后的对象。然后A进行属性注入，会去创建B，创建完成对B进行属性赋值时，会调用到`getBean(A)`方法获取A，此时会从缓存中获取，先获取到三级缓存中对象工厂，根据对象工厂获取对应的对象，然后注入给B。B走完生命周期走完之后，回到A的生命周期中继续进行属性赋值和初始化。

#### 为什么使用三级，二级缓存不行吗？

使用二级缓存解决循环依赖，意味着所有的Bean在创建之后就要完成AOP代理，违背了Spring的设计原则，Spring设计之初就是通过`AnnotationAwareAspectJAutoProxyCreator` 后置处理器在Bean生命周期的最后一步完成AOP代理，而不是在实例化之后立马进行AOP代理。


