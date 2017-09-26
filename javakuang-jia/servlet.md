Servlet生命周期分为三个阶段：

1，初始化阶段  调用init\(\)方法

2，响应客户请求阶段　　调用service\(\)方法

对于用户到达Servlet的请求，Servlet容器会创建特定于这个请求的ServletRequest对象和ServletResponse对象，然后调用Servlet的service方法。service方法从ServletRequest对象获得客户请求信息，处理该请求，并通过ServletResponse对象向客户返回响应信息。

3，终止阶段　　调用destroy\(\)方法

Servlet何时被创建：

1，默认情况下，当WEB客户第一次请求访问某个Servlet的时候，WEB容器将创建这个Servlet的实例。

2，当web.xml文件中如果&lt;servlet&gt;元素中指定了&lt;load-on-startup&gt;子元素时，Servlet容器在启动web服务器时，将按照顺序创建并初始化Servlet对象。

Web应用何时被启动：

1，当Servlet容器启动的时候，所有的Web应用都会被启动

2，控制器启动web应用

Servlet终止阶段：

　　当WEB应用被终止，或Servlet容器终止运行，或Servlet容器重新装载Servlet新实例时，Servlet容器会先调用Servlet的destroy\(\)方法，在destroy\(\)方法中可以释放掉Servlet所占用的资源。

