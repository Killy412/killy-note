# spring原理

#### 过滤器filter,拦截器 interceptor,aop的区别和联系

> 过滤器基于url,可以拦截所有的请求,控制最初的http请求
>
> 拦截器,是基于反射机制实现,不依赖servlet.
>
> aop只能拦截spring管理的bean实例

####  springmvc

经过dispatchServlet处理后交给controller,controller返回ModeAndView

controller生成数据,经过view渲染出来,可以渲染成json,xml,

#### spring容器启动

1. 创建一个ApplicationContext
2. 读取所有的Bean定义并加载
   - 先加载一个bean (**ConfigurationClassPostProcessor**)
   - 由**ConfigurationClassPostProcessor** 扫描其他的Bean定义
     - 扫描注解或者XML
     - BeanDefinition 资源定位
       - ResourceLoader 加载
3. 按照Bean的定义创建Bean
4. 一些Bean生命周期的加载,例如PostProcessor 

#### Spring Aop实现原理

通过动态代理(jdk动态代理或者cglib动态代理) 增强需要增强的类,生成字节码

#### springBean 生命周期

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