# Spring Boot 自学笔记（一）

## Spring 能做什么

Spring 可以做到哪些事情？根据官网的解释：

1. 微服务；
2. 响应式编程；
3. 分布式，云开发；
4. Web 开发；
5. Serverless 开发；
6. Event Driven，事件驱动；
7. 数据访问；
8. 安全控制
9. 移动开发；
10. 命令行开发；
11. ……

所以 Spring 是一个框架吗？一个框架不可能为你完成如此之多的事情，这违背了程序设计的原则。

Spring 是一个生态圈，里面提供了多种解决方案，让我们可以解决各种问题——Spring Boot 就是其中之一，其他有所耳闻的还有 Spring MVC、Spring Framework、Spring Cloud……

但是如此多的功能与解决方案，当我们尝试去整合他们，制作一个完整的应用时，会不会很麻烦？

根据以往的经验，我们必然需要陷入 **“配置地狱”**——通过大量的配置文件来完成组装。

我们不喜欢配置地狱，所以有了 Spring Boot。

## Spring Boot 是什么？

Spring Boot 是一个**比其他 Spring 框架更高层的框架**，他的底层实际上是 Spring Framework。

目前 Spring Boot 提供了两套方案：Reactive Stack 与 Servlet（Server Applet） Stack。

分别是通过 Spring WebFlux 使用异步数据构建响应式应用的方案，与传统的 Spring MVC 方案。前者可以使用更少的资源，实现大量并发（JS 程序员感觉很亲切，有种 Node.js 中使用 RxJS 的感觉）

所以总的来说，Spring Boot 就是一个框架上的框架，可以帮你快速方便的构建生产应用。

## Spring Boot 的优点

**1. Spring Boot 可以创建独立的 Spring 应用。**

即 Spring Boot 涵盖了 Spring。

**2. 内嵌服务器。**

Spring MVC 需要我们将编写好的程序打包成 war，再将其部署至 Tomcat。Spring Boot 自带服务器，直接启动即可。

**3. 自动 starter 依赖，简化构建配置。**

所谓 starter 就是「启动器」。例如我们开发一个应用，我们不需要导入一堆的依赖，仅需这一个 starter 即可，并且它还会替我们保证版本的兼容性。

**4. 自动配置 Spring 以及第三方功能。**

还是万恶的配置地狱，Spring Boot 拒绝配置地狱！

**5. 提供生产级别的监控、健康检查、外部化配置。**

**6. 无代码生成，无需编写 XML。**
