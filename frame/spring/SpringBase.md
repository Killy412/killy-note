# spring相关知识点


## Spring使用到的设计模式

- 工厂模式：FactoryBean/BeanFactory/ApplicationContext
- 单例模式：Bean默认都是单例
- 代理模式： AOP
- 策略模式：
- 模板方法： `jdbcTemplate` `hibernateTemplate`
- 观察者模式： 对象行为模式，对象之间有一种依赖关系，当一个对象发生改变时，对象所依赖的对象也会做出反映。**Spring事件驱动模型**
  - 事件角色
  - 事件监听者角色
  - 事件发布者角色
- 适配器模式(Adapter Pattern):将一个接口转成另一个接口，可以使不兼容的类一块工作，别名是**包装器**
- 装饰者模式

## 控制反转(IOC)和依赖注入(DI)

### 控制反转

ioc是一种面向对象的设计思想,将原本需要在程序中创建对象的控制权,交给spring框架管理
将对象之间的依赖关系交给ioc管理,并由ioc完成属性的注入

### 依赖注入

指的是容器在实例化的时候把它依赖的对象注入给他.IOC是思想,DI是IOC的实现.
主要是为了对象之间的解藕,硬编码会造成对象之间的过度耦合.

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

## AOP

### Spring Aop实现原理

通过动态代理(jdk动态代理或者cglib动态代理) 增强需要增强的类,生成字节码

> 原理: 用代理类包裹切面,织入spring管理的bean中,
>
> 动态代理实现
>
> spring aop动态代理实现方式有两种,jdk动态代理和cglib动态代理
>
> - jdk动态代理通过反射来生成目标代理接口的匿名实现类.是面向接口的
> - cglib动态代理:如果目标类没有实现接口,springaop会使用cglib动态代理目标类.可以在运行时生成某个类的子类,通过继承的方式做的动态代理,如果某个类时final,无法使用cglib动态代理.

### Spring AOP和AspectJ区别

|Spring AOP | AspectJ |
|--- |--- |
| 在纯Java中实现 | 使用Java编程语言扩展实现 |
|运行时织入| 支持编译时织入/编译后织入/加载时织入 |
|仅支持方法级增强| 可以编织字段/方法/构造函数/最终类 |
| 只能在Spring Bean上实现 | 可以在所有域对象上实现 |
| 性能差一些 | 性能更好 |

### 事务不生效的情况

1. 同一个类中调用另一个`@Transaction`注解的方法，不会生效。 spring采用动态代理来实现事务控制，而动态代理对象最终要调用目标对象的方法，导致不会触发代理类的方法。解决方案：注入自己/拆分
2. 非public的方法：Spring AOP在代理时，`TransactionInterceptor`事务拦截器会在目标方法前后进行拦截，获取`@Transaction`注解的事务配置信息时，会检查方法的修饰符是否是`public`，如果不是的话，不会获取注解信息。
3. 数据库引擎不支持事务，例如：MyIsam
4. 抛出检查异常不会回滚(**需要抛出非检查异常，即运行期异常才能进行回滚**)

### BeanFactory和ApplicatContext 区别

BeanFactory是Spring框架的基础,可以创建并管理各种类的对象,面向Spring本身.DefaultListableBeanFactory是常用的BeanFactory的实现类
ApplicantContext建立在BeanFactory的基础上,面向Spring的开发者.由BeanFactory派生而来,提供了更多面向实际应用的功能.

### BeanFactory和FactoryBean的区别

- `BeanFactory`是一个接口，定义了Spring中工厂的顶级规范，是IOC容器的核心接口，定义了`getBean()` `containsBean()`等Bean的通用方法.
主要使用场景有从IOC容器获取Bean/检索IOC容器中是否包含指定的Bean/判断Bean是否是单例

```java
public interface BeanFactory {

	//对FactoryBean的转义定义，因为如果使用bean的名字检索FactoryBean得到的对象是工厂生成的对象，
	//如果需要得到工厂本身，需要转义
	String FACTORY_BEAN_PREFIX = "&";

	//根据bean的名字，获取在IOC容器中得到bean实例
	Object getBean(String name) throws BeansException;
   ...
	//提供对bean的检索，看看是否在IOC容器有这个名字的bean
	boolean containsBean(String name);

	//根据bean名字得到bean实例，并同时判断这个bean是不是单例
	boolean isSingleton(String name) throws NoSuchBeanDefinitionException;
   ...
	//得到bean实例的Class类型
	@Nullable
	Class<?> getType(String name) throws NoSuchBeanDefinitionException;

	//得到bean的别名，如果根据别名检索，那么其原名也会被检索出来
	String[] getAliases(String name);
}

```

- `FactoryBean`是一个能生产或修饰对象生成的工厂Bean，类似于设计模式中的工厂模式和装饰器模式，能在需要的时候生产一个对象，可以返回任何Bean的实例。

```java
public interface FactoryBean<T> {
	//从工厂中获取bean
	@Nullable
	T getObject() throws Exception;
	//获取Bean工厂创建的对象的类型
	@Nullable
	Class<?> getObjectType();
	//Bean工厂创建的对象是否是单例模式
	default boolean isSingleton() {
		return true;
	}
}
```

`FactoryBean`表现的是一个工厂的职责。一个Bean实现了FactoryBean的接口，就变成了一个工厂，根据名称获取的对象实际上是工厂调用`getObject`方法返回的对象。如果在名称前面加上`&`符号，返回的是对象工厂，否则返回的是对象。

1. getObject('name')返回工厂中的实例
2. getObject('&name')返回工厂本身的实例

`FactoryBean`在Spring中最典型的应用是用来**创建AOP的代理对象。** AOP实际上是Spring运行时创建的代理对象，属于工厂方法设计模式。AOP通过Java的反射机制，在运行时创建一个代理对象，在代理对象中根据业务要求织入相应的逻辑。典型对象`ProxyFactoryBean`。`FactoryBean`为我们提供了一个更为灵活的方式，可以创建出更为复杂的Bean。

#### 区别

`BeanFactory`是Spring容器的顶级接口，`FactoryBean`类似于用户自定义的工厂接口。`FactoryBean`本质上也是一个Bean，归`BeanFactory`管理。

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

### Spring事务实现的方式

1. 编程式事务,需要在代码中调用beginTransaction,commit,rollback等事务相关的方法
2. 声明式事务：基于注解@Transaction,底层通过AOP实现,不需要额外编码

### 声明式事务的原理



### Spring事务隔离级别

- ISOLATION_DEFAULT: 使用后端数据库默认隔离级别,MySQL是可重复读,Oracle是读已提交
- ISOLATION_READ_UNCOMMITED: 读未提交
- ISOLATION_READ_COMMITED: 读已提交
- ISOLATION_REPEATABLE_READ: 可重复读
- ISOLATION_SERIALIZABLE: 串行化执行

### Spring事务传播机制

| 传播机制|描述|
|-|-|
| REQUIERD | 默认,如果没有事务就新建一个事务,如果已经存在,就加入到这个事务中 |
| SUPPORTS | 支持当前事务,如果没有,以非事务方式执行 |
| MANDATORY | 使用当前事务,如果没有当前事务,就抛出异常 |
| REQUIRED_NEW | 新建事务,如果当前有事务,就挂起事务 |
| NOT_SUPPORTED | 以非事务方式执行,如果当前存在事务,就抛出异常 |
| NESTED | 如果当前存在事务,则在事务内执行,如果没有当前事务,则执行与REQUIRED类似操作 |

Spring默认的事务传播行为是 `PROPAFATION_REQUIRED`,它适合绝大多数情况,如果多个 ServiceX#methodX()都工作在事务环境下(均被Spring 事务增强),且程序中存在调用链 `Service1#method1()->Service2#method2()->Service3#method3()`,那么这3个服务类的三个方法通过 Spring 的事务传播机制都工作在同一个事务中.



### Spring解决循环依赖的方式

Bean创建的过程有三步,`1.创建对象 2.属性赋值 3.初始化`.Spring通过三级缓存解决循环依赖.
1. `singletonObjects` 一级缓存,存储所有初始化完成的单例Bean
2. `earlySingletonObjects` 二级缓存,早期曝光对象,存储创建好,但是没有进行属性赋值以及初始化的单例Bean
3. `singletonFactories` 三级缓存,早期曝光工厂,二级缓存中存储的就是从这个工厂里获取的对象
当A/B两个类发生循环依赖时,在A完成实例化对象后,会根据实例化之后的A创建一个对象工厂,并放入到三级缓存中,如果A被AOP代理,通过对象工厂获得的对象就是代理之后的对象,如果没有被AOP代理,获取的就是实例化后的对象.然后A进行属性注入,会去创建B,创建完成对B进行属性赋值时,会调用到`getBean(A)`方法获取A,此时会从缓存中获取,先获取到三级缓存中对象工厂,根据对象工厂获取对应的对象,然后注入给B.B走完生命周期走完之后,回到A的生命周期中继续进行属性赋值和初始化.

#### 为什么使用三级,二级缓存不行吗？

使用二级缓存解决循环依赖,意味着所有的Bean在创建之后就要完成AOP代理,违背了Spring的设计原则,Spring设计之初就是通过`AnnotationAwareAspectJAutoProxyCreator` 后置处理器在Bean生命周期的最后一步完成AOP代理,而不是在实例化之后立马进行AOP代理.
