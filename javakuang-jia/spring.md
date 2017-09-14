### Spring的优点

1. **轻量级**：Spring在大小和透明性方面绝对属于轻量级的，基础版本的Spring框架大约只有2MB。
2. **控制反转\(IOC\)**：Spring使用控制反转技术实现了松耦合。依赖被注入到对象，而不是创建或寻找依赖对象。
   在spring中，直接操控的对象的调用权交给容器，通过容器来实现对象组件的装配和管理，从而实现对象之间的松耦合。所谓的“控制反转”概念就是对组件对象控制权的转移，从程序代码本身转移到了外部容器。

3. **面向切面编程\(AOP\)**： Spring支持面向切面编程，同时把应用的业务逻辑与系统的服务分离开来。
   原理：动态代理机制。对象无需自行创建或管理它们的依赖关系，IoC容器在运行期间，动态地将某种依赖关系注入到对象之中。依赖注入能让相互协作的软件组件保持松散耦合。
4. **容器**：Spring包含并管理应用程序对象的配置及生命周期。
5. **MVC框架**：Spring的web框架是一个设计优良的web MVC框架，很好的取代了一些web框架。
6. **事务管理**：Spring对下至本地业务上至全局业务\(JAT\)提供了统一的事务管理接口。
7. **异常处理**：Spring提供一个方便的API将特定技术的异常\(由JDBC, Hibernate, 或JDO抛出\)转化为一致的、Unchecked异常。

### Spring Bean

Spring Beans是构成Spring应用核心的Java对象。这些对象由Spring IOC容器实例化、组装、管理。这些对象通过容器中配置的元数据创建，例如，使用XML文件中定义的创建。 在Spring中创建的beans都是单例的beans。在bean标签中有一个属性为”singleton”,如果设为true，该bean是单例的，如果设为false，该bean是原型bean。Singleton属性默认设置为true。因此，spring框架中所有的bean都默认为单例bean。

### Spring Bean的生命周期

Bean在Spring中的生命周期如下：

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

[https://zhengjianglong.gitbooks.io/note-of-interview/content/framework/spring.html](https://zhengjianglong.gitbooks.io/note-of-interview/content/framework/spring.html)

