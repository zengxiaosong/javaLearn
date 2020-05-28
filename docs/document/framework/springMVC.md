## SpringMVC学习笔记

### 1.前言



> 接下来我们将进入springMVC框架的学习，在学习这个框架之前，我们应该做一些知识的预备，比如说什么是MVC模式？``` M: model V:view C :controllor``` 可能到这里大家还是比较迷糊，那我们通过这个MVC来看看我们所应用的框架的工作模式和工作原理是怎么样的呢？我们通过原理图来看看吧！

<img src="one\image-20200528214029882.png" alt="image-20200528214029882" style="zoom:50%;" />

> 我们可以清晰的看出，view层使用的是springMVC框架，也就是我们的视图层，主要负责视图转发，数据的绑定，拦截我们的转发请求，用户通过URL向我们的服务器发起请求，请求被我们的视图层框架springMVC拦截，拦截之后，进行路由检查，进入我们的控制层，控制层框架spring对路由参数等信息做出处理，然后进入model层，也就是持久层，去数据库取我们需要的数据，取回数据之后，返回到控制层进行数据的处理，再次返回到我们的view层，通过路由转发给我们的浏览器，浏览器接收信息并进行展示。

###  2.初始搭建 

> 这里我们引入springMVC相关的jar包，因为springMVC本身就是spring框架的一部分，因此下面的jar包我们都可以通过spring官方下载导入，当然，这里有一个比较特殊的jar包。```commons-logging-1.2.jar``` 需要自己通过maven仓库下载。

![image-20200528225400494](one\image-20200528225400494.png)

编写web.xml : 我们应该清楚，web.xml是整个网站请求访问的拦截器，我们的框架都是在javaweb的原生基础上进行开发的。而且，springMVC的核心是DispatchServlet,浏览器转发的请求都要经过它来处理。下面是web.xml的文件代码：   

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
         version="4.0">
    <servlet>
        <servlet-name>springMVC</servlet-name>
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
    </servlet>
    <servlet-mapping>
        <servlet-name>springMVC</servlet-name>
        <url-pattern>/</url-pattern>
    </servlet-mapping>
</web-app>
```

 这里的```<url-pattern>/</url-pattern>``` 表示对所有的请求进行拦截。

在```WEB-INF```目录下编辑springMVC-servlet.xml文件：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd">
        <context:component-scan base-package="com.zxs.controller"></context:component-scan>
        <bean id="viewResolver" class="org.springframework.web.servlet.view.InternalResourceViewResolver">
            <property name="prefix" value="/WEB-INF/view/"/>
            <property name="suffix" value=".jsp"/>
        </bean>
</beans>
```

做下解释说明吧，为什么要编写springMVC-servlet.xml文件呢？ 这是对我们web.xml中DispatcherServlet的配置，简单来说就是对springMVC做配置。那这个文件为什么这么命名呢?又为啥要配置到这个WEB-INF目录下呢？首先来看第一个问题，这个文件的命名规则是 ```<servlet-name>-servlet.xml``` .这里的 <servlet-name>是我们前面web.xml文件里的servlet名称。第二个问题，之所以配置到这个位置，这是系统默认的路径，如果有需要，可以配置文件进行修改。

这里我们再讲讲里面的配置的作用是什么吧！来看第一个，``` <context:component-scan base-package="">``` 从我们上面的代码我们应该很清楚，这是在配置我们的控制层的代码，通过这个配置将控制层配置到```com.zxs.controller```这个包下，下面这个```InternalResourceViewResolver```什么意思呢？内部资源视图解析器，控制器通过返回参数将请求所需的资源通过这种形式返回给浏览器端。（如果听不懂，那我们就通过一个实例来看看吧！） 

在项目创建的index.jsp中创建一个请求：   

<img src="one\image-20200528233053815.png" alt="image-20200528233053815" style="zoom: 80%;" />

点击请求时，请求会通过上面的两个配置文件到达我们的控制层：我们在com.zxs.controller包下面创建类BaseController,如图所示，做转发请求：

<img src="one\image-20200528233419951.png" alt="image-20200528233419951" style="zoom: 50%;" />

大家可以看到这里我们的返回值是 "success",什么意思呢？大家可以看到我们对springMVC的内部资源视图解析器的配置，是定义在```/WEB-INF/view/```目录下的，我们来看看这个目录下有什么呢？

![image-20200528233949021](one\image-20200528233949021.png)

这下想必就十分清晰了。返回的就是视图jsp的名称。然后大家点击运行就能查看效果了。

### 3.几个基本的注解

