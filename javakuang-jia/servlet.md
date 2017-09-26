Servlet生命周期分为三个阶段：

　　1，初始化阶段  调用init\(\)方法

　　2，响应客户请求阶段　　调用service\(\)方法

对于用户到达Servlet的请求，Servlet容器会创建特定于这个请求的ServletRequest对象和ServletResponse对象，然后调用Servlet的service方法。service方法从ServletRequest对象获得客户请求信息，处理该请求，并通过ServletResponse对象向客户返回响应信息。

　　3，终止阶段　　调用destroy\(\)方法

