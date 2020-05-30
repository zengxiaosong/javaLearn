## SpringMVC学习笔记

### 1.前言

> 接下来我们将进入springMVC框架的学习，在学习这个框架之前，我们应该做一些知识的预备，比如说什么是MVC模式？``` M: model V:view C :controllor``` 可能到这里大家还是比较迷糊，那我们通过这个MVC来看看我们所应用的框架的工作模式和工作原理是怎么样的呢？我们通过原理图来看看吧！

![image-20200528214029882](one\image-20200528214029882.png)

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
        <!--<init-param> 用来描述文件的位置-->
        <!--<param-name>contextConfigLocation</param-name>-->
        <!--<param-value>classpath:springMVC-swervlet.xml</param-value>-->
        <!--</init-param>-->
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

![](one\image-20200528233053815.png) 

点击请求时，请求会通过上面的两个配置文件到达我们的控制层：我们在com.zxs.controller包下面创建类BaseController,如图所示，做转发请求：

![](one\image-20200528233419951.png)

大家可以看到这里我们的返回值是 "success",什么意思呢？大家可以看到我们对springMVC的内部资源视图解析器的配置，是定义在```/WEB-INF/view/```目录下的，我们来看看这个目录下有什么呢？

![image-20200528233949021](one\image-20200528233949021.png)

这下想必就十分清晰了。返回的就是视图jsp的名称。然后大家点击运行就能查看效果了。

当然，最好的是在我们的springMVC的配置文件上加上这样一个配置：

```xml
<!--注解扫描的配置-->
    <mvc:annotation-driven/>
```

### 3.几个基本的注解

#### @Controller 

以前在编写```Controller``` 方法的时候，需要开发者自定义一个```Controller``` 类实现Controller接口，实现```handleRequest``` 方法返回```ModelAndView``` 。并且需要在Spring配置文件中配置Handle，将某个接口与自定义```Controller``` 类做映射。

这么做有个复杂的地方在于，一个自定义的```Controller``` 类智能处理一个单一请求。而在采用```@Contoller``` 注解的方式，可以使接口的定义更加简单，将```@Controller``` 标记在某个类上，配合```@RequestMapping``` 注解，可以在一个类中定义多个接口，这样使用起来更加灵活。

被```@Controller``` 标记的类实际上就是个```SpringMVC Controller``` 对象，它是一个控制器类，而```@Contoller``` 注解在```org.springframework.stereotype``` 包下。其中被```@RequestMapping``` 标记的方法会被分发处理器扫描识别，将不同的请求分发到对应的接口上。  

####  @RequestMapping （重要）

设置路由信息的注解，也就是说，当我们的浏览器端发起请求时，请求通过```web.xml``` ,再通过```springMVC-servlet.xml``` 到我们通过注解@Controller注解控制的类时，会去和```@RequestMapping``` 上设置的注解进行比较，以此到达所设置的接口方法。  值得注意的是：注解 ```@RequestMapping```  即可以设置在方法上，也可以设置在类上。通过下面的例子说明：

```java
@Controller
//在最前面可以加这个 /  也可以不加，这个在不加的时候，系统可以默认给加上
//@RequestMapping(value="first/")
@RequestMapping("/first/")
public class BaseController {

    @RequestMapping("hello1")
    public String hello(){
        return "success";
    }

}
```

当然，```@RequestMapping``` 不可能就这么简单，上面我们都是使用的默认参数，接下来我们看看里面还有几个参数，我们直接来看看源码是怎么的: 

```java
@Target({ElementType.METHOD, ElementType.TYPE})
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Mapping
public @interface RequestMapping {
    String name() default "";

    @AliasFor("path")
    String[] value() default {};

    @AliasFor("value")
    String[] path() default {};

    RequestMethod[] method() default {};

    String[] params() default {};

    String[] headers() default {};

    String[] consumes() default {};

    String[] produces() default {};
}
```

1. value: 这是我们的默认参数，也是我们使用的最频繁的。表示路由信息。

   ```java
   @RequestMapping(value = "first/hello")
   public String hi(){
       return "success";
   }
   ```

2. method：指定请求的method类型， GET、POST、PUT、DELETE等,一共有八种类型，不过，这里我们最常用的也就是这四种类型，这四种类型也会与我们的Restful风格进行匹配。当然，后面我们会对这种风格进行大致的讲解。

   ```java
   @RequestMapping(value = "first/hello", method = RequestMethod.GET)
   public String hi(){
       return "success";
   }
   ```

   为什么说是八种呢，从上面的代码中，我们可以看出，method参数的值是一个枚举类型，我们看看这个枚举类型有哪些：

   ```java
   package org.springframework.web.bind.annotation;
   
   public enum RequestMethod {
       GET,
       HEAD,
       POST,
       PUT,
       PATCH,
       DELETE,
       OPTIONS,
       TRACE;
   
       private RequestMethod() {
       }
   }
   ```

3. params： 指定request中必须包含某些参数值是，才让该方法处理。什么意思呢？就是说当你发起请求后，你请求后面的参数必须包含哪些内容，不能包含哪些内容，在这个参数里面就做出了规定。

   ```java
   /*
    * 表示参数中必须让id=23 名字name必须不能是admin,否者将不能匹配到这个方法中，
    */
   @RequestMapping(value = "first/hello", method = RequestMethod.GET, params = {"id=23","name!=admin"})
   public String hi(){
       return "success";
   }
   ```

4. 其他的几种类型就不过多讲解了，用到的时候再去看看，了解下。

当然，@RequestMapping也是支持Ant路径风格的，**Ant** **风格资源地址支持** **3** **种匹配符** 

```?``` 匹配文件名中的一个字符

 ```*``` 匹配文件名中的任意字符 

```**``` 匹配多层路径

举个例子：

hello/*/aaa              =>     hello/bba/aaa  或者是 hello/sfm/aaa;

hello/**/aaa             =>     hello/ccc/bbb/aaa  (嵌入了多层)

hello/aa??              =>      hello/aabb          或者是  hello/aacd

#### @PathVariable（与上对应）

当我们在发送请求时，我们可以将路由中的值通过参数绑定传递到我们的方法中去进行一定的事物处理。实现过程如下：

```
/*
 * 访问可以是：http:8080/test0528/first/hello1/22
 */
@RequestMapping("hello1/{id}")
public String hello(@PathVariable("id") Integer id){
    System.out.println(id);
    return "success";
}
```

### 4. Restul风格简单介绍

简单来说，这就是一个前后端框架开发的接口规范