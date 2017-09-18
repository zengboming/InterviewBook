### Spring的优点

1. **轻量级**：Spring在大小和透明性方面绝对属于轻量级的，基础版本的Spring框架大约只有2MB。
2. **控制反转\(IOC\)**：Spring使用控制反转技术实现了松耦合。**依赖被注**入到对象，而不是创建或寻找依赖对象。  
   在spring中，直接操控的对象的调用权交给容器，通过容器来实现对象组件的装配和管理，从而实现对象之间的松耦合。所谓的“控制反转”概念就是对组件对象控制权的转移，从程序代码本身转移到了外部容器。

3. **面向切面编程\(AOP\)**： Spring支持面向切面编程，同时把应用的业务逻辑与系统的服务分离开来。  
   原理：**动态代理机制**。对象无需自行创建或管理它们的依赖关系，IoC容器在运行期间，动态地将某种依赖关系注入到对象之中。依赖注入能让相互协作的软件组件保持松散耦合。

4. **容器**：Spring包含并管理应用程序对象的配置及生命周期。

5. **MVC框架**：Spring的web框架是一个设计优良的web MVC框架，很好的取代了一些web框架。

6. **事务管理**：Spring对下至本地业务上至全局业务\(JAT\)提供了统一的事务管理接口。

7. **异常处理**：Spring提供一个方便的API将特定技术的异常\(由JDBC, Hibernate, 或JDO抛出\)转化为一致的、Unchecked异常。

### AOP实现原理

实现AOP的技术：

1.采用动态代理技术，利用截取消息的方式，对该消息进行装饰，以取代原有对象行为的执行；

2.采用静态织入的方式，引入特定的语法创建“方面”，从而使得编译器可以在编译期间织入有关“方面”的代码。

Spring AOP 的实现原理其实很简单：

1. AOP 框架负责动态地生成 AOP 代理类，这个代理类的方法则由 Advice 和回调目标对象的方法所组成,并将该对象可作为目标对象使用。AOP 代理包含了目标对象的全部方法，但 AOP 代理中的方法与目标对象的方法存在差异，AOP 方法在特定切入点添加了增强处理，并回调了目标对象的方法。 
2. Spring AOP使用动态代理技术在运行期织入增强代码。使用两种代理机制：基于JDK的动态代理（JDK本身只提供接口的代理）；基于CGlib的动态代理。

**JDK动态代理是通过JdkDynamicAopProxy类实现,该类实现了InvocationHandler接口.invoke方法实现了增强器织入的主要逻辑。**JDK的动态代理主要涉及java.lang.reflect包中的两个类：Proxy和InvocationHandler。其中InvocationHandler只是一个接口，可以通过实现该接口定义横切逻辑，并通过反射机制调用目标类的代码，动态的将横切逻辑与业务逻辑织在一起。而Proxy利用InvocationHandler动态创建一个符合某一接口的实例，生成目标类的代理对象。 其代理对象**必须是某个接口的实现**,它是通过在运行期间创建一个接口的实现类来完成对目标对象的代理.只能实现接口的类生成代理,而**不能针对类。**

### 控制反转IOC

1. **控制反转**：是一种以给予应用程序中目标组件更多控制为目的设计范式。
2. **依赖注入**：在编译阶段尚未知所需的功能是来自哪个的类的情况下，将其他对象所依赖的功能对象实例化的模式。这就需要一种机制用来激活相应的组件以提供特定的功能，所以依赖注入是控制反转的基础。
3. **依赖注入三种方式**：
   1. 构造器注入
   2. Setter方法注入
   3. 接口注入
4. **Spring IOC**：把对象的创建、初始化、销毁交给spring来管理，而不是由开发者控制，实现控制反转。

   1. Spring中的 beans包和 context包构成了Spring框架 IoC容器的基础。

   2. BeanFactory是Spring IoC容器的具体实现，用来包装和管理各种bean。

   3. BeanFactory接口是Spring IoC 容器的核心接口，BeanFactory 接口提供了一个先进的配置机制，使得任何类型的对象的配置成为可能。

### Spring配置方式

1. 基于XML的配置  
   XML格式的配置文件，通常用&lt;beans&gt;开头，然后一系列的bean定义和专门的应用配置选项组成。Spring有以下主要的命名空间：context、beans、jdbc、tx、aop、mvc和aso。

2. 基于注解的配置  
   只需要在相关类上、方法上或者字段声明上使用注解即可。注解注入将会被容器在XML注入之前被处理，所以XML会覆盖掉前者对于同一个属性的处理结果。

   1. @Required：该注解应用于设值方法。

   2. @Autowired：该注解应用于有值设值方法、非设值方法、构造方法和变量。

   3. @Qualifier：该注解和@Autowired注解搭配使用，用于消除特定bean自动装配的歧义。

   4. @Resource：

3. 基于Java的配置  
   Spring对Java配置的支持是由@Configuration注解和@Bean注解来实现的。由@Bean注解的方法将会实例化、配置和初始化一个新对象，这个对象将由Spring的IoC容器来管理。被@Configuration所注解的类则表示这个类的主要目的是作为bean定义的资源。被@Configuration声明的类可以通过在同一个类的内部调用@bean方法来设置嵌入bean的依赖关系。

### Spring Bean

Spring Beans是构成Spring应用核心的Java对象。这些对象由Spring IOC容器实例化、组装、管理。这些对象通过容器中配置的元数据创建，例如，使用XML文件中定义的创建。 在Spring中创建的beans都是单例的beans。在bean标签中有一个属性为”singleton”,如果设为true，该bean是单例的，如果设为false，该bean是原型bean。Singleton属性默认设置为true。因此，spring框架中所有的bean都默认为单例bean。

### Spring Bean的生命周期

在一个bean实例被初始化时，需要执行一系列的初始化操作以达到可用的状态。当一个bean不在被调用时需要进行相关的析构操作，并从bean容器中移除。Bean在Spring中的生命周期如下：

**实例化**。Spring通过new关键字将一个Bean进行实例化，JavaBean都有默认的构造函数，因此不需要提供构造参数。

**填入属性**。Spring根据xml文件中的配置通过调用Bean中的setXXX方法填入对应的属性。 事件通知。Spring依次检查Bean是否实现了BeanNameAware、BeanFactoryAware、ApplicationContextAware、BeanPostProcessor、InitializingBean接口，如果有的话，依次调用这些接口。

**使用**。应用程序可以正常使用这个Bean了。

**销毁**。如果Bean实现了DisposableBean接口，就调用其destroy方法。

#### ---加载过程---

1.容器寻找Bean的定义信息并且将其实例化。

2.如果允许提前暴露工厂，则提前暴露这个bean的工厂，这个工厂主要是返回该未完全处理的bean．主要是用于避免单例属性循环依赖问题．

3.受用**依赖注入**，Spring按照Bean定义信息配置Bean的所有属性。

4.如果Bean实现了**BeanNameAware**接口，工厂调用Bean的**setBeanName\(\)**方法传递Bean的ID。

5.如果Bean实现了**BeanFactoryAware**接口，工厂调用**setBeanFactory\(\)**方法传入工厂自身。

6.如果**BeanPostProcessor**和Bean关联，那么它们的**postProcessBeforeInitialzation\(\)**方法将被调用。

7.如果Bean指定了init-method方法，它将被调用。

8.如果有**BeanPostProcessor**和Bean关联，那么它们的postProcessAfterInitialization\(\)方法将被调用

9.最后如果配置了destroy-method方法则注册**DisposableBean**.

到这个时候，Bean已经可以被应用系统使用了，并且将被保留在Bean Factory中知道它不再需要。

### Spring 用到的设计模式

1. 单例模式—在spring配置文件中定义的bean默认为单例模式。
2. 模板方法—用来解决代码重复的问题。比如. RestTemplate, JmsTemplate, JpaTemplate。
3. 工厂模式—BeanFactory用来创建对象的实例。
4. 代理模式—在AOP和remoting中被用的比较多。



