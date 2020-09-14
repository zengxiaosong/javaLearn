## SpringMVC学习笔记

### 一、前言

> 接下来我们将进入springMVC框架的学习，在学习这个框架之前，我们应该做一些知识的预备，比如说什么是MVC模式？``` M: model V:view C :controllor``` 可能到这里大家还是比较迷糊，那我们通过这个MVC来看看我们所应用的框架的工作模式和工作原理是怎么样的呢？我们通过原理图来看看吧！

![image-20200528214029882](one\image-20200528214029882.png)

> 我们可以清晰的看出，view层使用的是springMVC框架，也就是我们的视图层，主要负责视图转发，数据的绑定，拦截我们的转发请求，用户通过URL向我们的服务器发起请求，请求被我们的视图层框架springMVC拦截，拦截之后，进行路由检查，进入我们的控制层，控制层框架spring对路由参数等信息做出处理，然后进入model层，也就是持久层，去数据库取我们需要的数据，取回数据之后，返回到控制层进行数据的处理，再次返回到我们的view层，通过路由转发给我们的浏览器，浏览器接收信息并进行展示。

###  二、初始搭建 

> 这里我们引入springMVC相关的jar包，因为springMVC本身就是spring框架的一部分，因此下面的jar包我们都可以通过spring官方下载导入，当然，这里有一个比较特殊的jar包。```commons-logging-1.2.jar``` 需要自己通过maven仓库下载。

![image-20200528225400494](one\image-20200528225400494.png)



或者直接引入maven依赖环境：

```xml
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-webmvc</artifactId>
    <version>5.2.7.RELEASE</version>
</dependency>
```

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

### 三、几个基本的注解

#### @Controller 

以前在编写```Controller``` 方法的时候，需要开发者自定义一个```Controller``` 类实现Controller接口，实现```handleRequest``` 方法返回```ModelAndView``` 。并且需要在Spring配置文件中配置Handle，将某个接口与自定义```Controller``` 类做映射。

这么做有个复杂的地方在于，一个自定义的```Controller``` 类智能处理一个单一请求。而在采用```@Contoller``` 注解的方式，可以使接口的定义更加简单，将```@Controller``` 标记在某个类上，配合```@RequestMapping``` 注解，可以在一个类中定义多个接口，这样使用起来更加灵活。

被```@Controller``` 标记的类实际上就是个```SpringMVC Controller``` 对象，它是一个控制器类，而```@Contoller``` 注解在```org.springframework.stereotype``` 包下。其中被```@RequestMapping``` 标记的方法会被分发处理器扫描识别，将不同的请求分发到对应的接口上。  

####  @RequestMapping 

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

    String[] params() default {};    //携带的参数的规则

    String[] headers() default {};  //请求头所设置的要和浏览器中的请求信息一致才能行

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

**(在controller中设置)**

hello/*/aaa              =>     hello/bba/aaa  或者是 hello/sfm/aaa;

hello/**/aaa             =>     hello/ccc/bbb/aaa  (嵌入了多层)

hello/aa??              =>      hello/aabb          或者是  hello/aacd

#### @PathVariable

当我们在发送请求时，我们可以将路由中的值通过参数绑定传递到我们的方法中去进行一定的事物处理。实现过程如下：

```
/*
 * 访问可以是：http:8080/test0528/first/hello1/22
   后面的形参不一定要与占位符上的值相同
 */
@RequestMapping("hello1/{id}")
public String hello(@PathVariable("id") Integer id){
    System.out.println(id);
    return "success";
}
```

### 四、Restful风格简单介绍

简单来说，这就是一个前后端框架开发的接口规范（后面再介绍）

springMVC配置restful风格的设计

#### 1、配置过滤器

```xml
<!--restful风格设计，添加请求过滤器 HidenHttpMethodFilter-->
<filter>
    <filter-name>HiddenHttpMethodFilter</filter-name>
    <filter-class>org.springframework.web.filter.HiddenHttpMethodFilter</filter-class>
</filter>
<filter-mapping>
    <filter-name>HiddenHttpMethodFilter</filter-name>
    <!--过滤多有的请求-->
    <url-pattern>/*</url-pattern>
</filter-mapping>
```

#### 2、表单的提交写法

```jsp
<a href="testRest/1001">测试获取数据</a>
<h3>表单测试restful:POST</h3>
<form action="testRest" method="post">
    <input hidden="hidden" name="_method" value="post"/>
    <input type="submit" value="测试"/>
</form>

<h3>表单测试restful:update</h3>
<form action="testRest" method="POST">
    <input hidden="hidden" name="_method" value="put"/>
    <input type="submit" value="update测试"/>
</form>

<h3>表单测试restful:delete</h3>
<form action="testRest/1101" method="POST">
    <input hidden="hidden" name="_method" value="delete"/>
    <input type="submit" value="delete测试"/>
</form>
```

#### 3、控制器的写法

```java
@RequestMapping(value = "/testRest/{id}",method = RequestMethod.GET)
public String getUserId(@PathVariable("id") Integer id){
    System.out.println("get: id:"+ id);
    return "success";
}

@RequestMapping(value = "/testRest",method = RequestMethod.POST)
public String insertUser(){
    System.out.println("插入数据：post");
    return "success";
}

/**
* @Description:  使用这种restful的形式的时候，必须要加上@ResponsBody,否则报405错误
* @Param: [] 
* @return: java.lang.String 
* @Author: 曾小松
* @Date: 2020/9/9 
*/ 
@ResponseBody
@RequestMapping(value = "/testRest",method = RequestMethod.PUT)
public String updateUser(){
    System.out.println("更新数据：update");
    return "update data success";
}

@ResponseBody
@RequestMapping(value = "/testRest/{id}",method = RequestMethod.DELETE)
public String deleteUser(@PathVariable("id") Integer id){
    System.out.println("delete data: id:"+id);
    return id+"delete data success";
}
```

### 五、处理请求的数据

> 前面我们都是对路由URL进行分析，我们再来看看如何对URL请求的参数进行处理，以及如何去获取我们加上的参数。

#### 1.直接获取参数

> 将我们从前端页面上获取的数据发送到服务器后台上，并打印出来，目前不支持中文（如果遇到中文乱码，请看解决乱码的部分）

html部分：

```html
<%--直接获取参数的表单--%>
<form action="getForm1" method="post">
   <input type="text" name="userName" /><br/>
   <input type="password" name="password" /><br/>
   <input type="submit" value="提交"><br/>
</form>
```

控制器部分：

```java
@RequestMapping(value = "/getForm1" )
public String getForm(String userName, String password){
    System.out.println("userName: "+userName + " password: "+password);
    return "success";
}
```

当然，这种直接获取参数的方法还是比较常用的，控制台就会直接打印对应的数据乐。但是我们要想一个问题，他是如何获取的？从上面我们可以看出，**接口方法的参数名称和我们表单提交的参数名称是相同的**，那么我们将接口方法中的```userName``` 改写成  ```username``` 是否能获取这个参数呢？肯定是不能的，所以我们在使用这种形式的时候要注意写法。

#### 2. @RequestParam

> 同样的，我们先来看看源码部分有什么内容，再通过这个内容来讲解下具体的用法。源码如下：

```java
@Target({ElementType.PARAMETER})
@Retention(RetentionPolicy.RUNTIME)
@Documented
public @interface RequestParam {
    @AliasFor("name")
    String value() default "";

    @AliasFor("value")
    String name() default "";

    boolean required() default true;

    String defaultValue() default "\n\t\t\n\t\t\n\ue000\ue001\ue002\n\t\t\t\t\n";
}
```

我们来详解下这个注解的参数是什么个意思，参数有：```name, value, required, defaultValue,``` 从上面的代码中我们可以看出其实 value，和name 是一样的，他们是互相引用值，所以在使用的过程中，只使用一个就行了，如果两个都用上并且值不一样的话，就会报错。

1. value,name 这两个参数表示传值的参数名，也就是表单中或者直接是GET请求中传递值的名称。比如```  <input type="text" name="userName" /><br/>```中的**userName**。
2. required 参数，他表示我们请求的这个参数是否是必须的，从源码中可以看出，默认值是true,也就是说默认是必须要传值的，当然，根据需要你可以改变需求为```required=false``` .
3. defaultValue表示默认值，也就是说，如果没有传这个值，后面跟随的参数将会被赋予一个默认值，这个默认值是String类型的。当然，这里值得注意的是，如果说没有默认值，也没有传递参数，那么后面定义参数的值就会被赋予null值。**这里有一个隐藏的错误点，要注意下，当我们被赋予null值时，是不是应该想下，null是引用类型的，那int  float  boolean等八种基本形式的数据类型能被这样赋值吗？肯定是不可以的啊，那不报错才怪了。 **

我们用上面的表单写一个具体的用法如下：(**补充下，比如我们前面在value中就规定了传递的参数，那么后面参数如：String name)就不用与表单保持一致了，**

```java
@Controller
public class BaseController {

    @RequestMapping(value = "/getForm1" )
    public String getForm(@RequestParam(value = "userName",
                                        required = false,
                                        defaultValue = "hello"
                                       )String name, String password){
        System.out.println("username: "+name + " password: "+password);
        return "success";
    }
```

那么可能有部分人就会怀疑了，既然我们能直接获取了，为啥还要使用这种形式呢？我们继续往后面看看吧。再来看两个注解。

#### 3. @RequestHeader

同样先看下源码：

```java
@Target({ElementType.PARAMETER})
@Retention(RetentionPolicy.RUNTIME)
@Documented
public @interface RequestHeader {
    @AliasFor("name")
    String value() default "";

    @AliasFor("value")
    String name() default "";

    boolean required() default true;

    String defaultValue() default "\n\t\t\n\t\t\n\ue000\ue001\ue002\n\t\t\t\t\n";
}
```

从这个源码来看，形式是一样的，这个就没啥讲的了，但是注意的是，请求头是包含了很多的信息的，具体我截了张图，大家可以参考下：

![image-20200601220437597](one\image-20200601220437597.png)

然后还是给一个例子吧：比如说获取accept-language: (当然这不是很常用，我们知道有这么个用法就可以了)

```java
@RequestMapping(value = "/getForm1" )
public String getForm(@RequestParam(value = "userName", required = false, defaultValue = "hello")String name,
                      String password,
                    @RequestHeader(value = "accept-language")String language){
    System.out.println("username: "+name + " password: "+password);
    System.out.println("language:" +language);
    return "success";
}
```

#### 4. @CookieValue

如同上面一样，看下源码是怎么回事吧：(模式都是相同的，我们就不做讲解了，就是通过获取键值对来获取)

```java
@Target({ElementType.PARAMETER})
@Retention(RetentionPolicy.RUNTIME)
@Documented
public @interface CookieValue {
    @AliasFor("name")
    String value() default "";

    @AliasFor("value")
    String name() default "";

    boolean required() default true;

    String defaultValue() default "\n\t\t\n\t\t\n\ue000\ue001\ue002\n\t\t\t\t\n";
}
```

#### 5. 通过POJO获取参数

什么是POJO,(Plain Ordinary Java Objects),就是一个普通简单的javaBean,怎么来用呢？我们都知道，比如我们的注册信息，注册的是一个用户的基本信息，那我们是不是可以说我们要传递一个类进去，信息都封装为属性，然后通过这个类将信息传递到后端中，是不是更加的方便写。也能够更加详细。

既然是javabean,那我们先创建相关的bean包，再在包下面创建相关的类。

首先是User类：

```java
package com.zxs.beans;

/*
 *@author by java开发-曾
 *2020/6/1 22:32
 *文件说明：
 */
public class User {
    private String userName;
    private String password;
    private Address address;

    public User() {
    }

    public String getUserName() {
        return userName;
    }

    public void setUserName(String userName) {
        this.userName = userName;
    }

    public String getPassword() {
        return password;
    }

    public void setPassword(String password) {
        this.password = password;
    }

    public Address getAddress() {
        return address;
    }

    public void setAddress(Address address) {
        this.address = address;
    }

    @Override
    public String toString() {
        return "User{" +
                "userName='" + userName + '\'' +
                ", password='" + password + '\'' +
                ", address=" + address +
                '}';
    }
}
```

然后是地址Address类：

```java
package com.zxs.beans;

/*
 *@author by java开发-曾
 *2020/6/1 22:34
 *文件说明：
 */
public class Address {
    private  String country;
    private  String email;

    public Address() {
    }

    public String getCountry() {
        return country;
    }

    public void setCountry(String country) {
        this.country = country;
    }

    public String getEmail() {
        return email;
    }

    public void setEmail(String email) {
        this.email = email;
    }

    @Override
    public String toString() {
        return "Address{" +
                "country='" + country + '\'' +
                ", email='" + email + '\'' +
                '}';
    }
}
```

我们既然是通过属性绑定的，那么表单上元素的name属性是怎么命名的：（**注意与User类对照看，属性与名称是相同的**）

```html
<%--POJO获取参数的表单--%>
<form action="getForm1" method="post">
   姓名：<input type="text" name="userName" /><br/>
   密码：<input type="password" name="password" /><br/>
   国家：<input type="text" name="address.country"/><br/>
   邮箱：<input type="text" name="address.email"/><br/>
   <input type="submit" value="提交"><br/>
</form>
```

再来看看控制器怎么操作的吧：

```java
@RequestMapping(value = "/getForm1" )
public String getForm(User user){
    System.out.println(user);
    return "success";
}
```

当然，要获取请求传递过来的参数，还可以通过原生的ServletAPI实现，当然要涉及到源码和原理部分，这里我们就不去细讲了，有兴趣的可以多去了解下。

### 六、控制器如何处理数据

#### 1、获取Servlet API

```java
@RequestMapping(value = "/testServlet",method = RequestMethod.POST)
public String ServletUser(HttpServletRequest request, HttpServletResponse response){
    String userName = request.getParameter("userName");
    System.out.println(userName);
    return "success";
}
```

#### 2、通过ModeAndView

```java
@RequestMapping(value = "/testMode",method = RequestMethod.POST)
public ModelAndView modeAndViewUser(String userName){
    //建立基本对象
    ModelAndView mv = new ModelAndView();
    //向作用域中添加数据  这里的作用域是指request  
    mv.addObject("userName",userName);
    //设置视图
    mv.setViewName("success");
    System.out.println(userName);
    return mv;
}
```

> 附带下作用域的说明

1、page里的变量没法从index.jsp传递到test.jsp。只要页面跳转了，它们就不见了。 
2、request里的变量可以跨越forward前后的两页。但是只要刷新页面，它们就重新计算了。 
3、session的变量一直在累加，开始还看不出区别，只要关闭浏览器，再次重启浏览器访问这页，session里的变量就重新                        计算了。 
4、application里的变量一直在累加，除非你重启tomcat，否则它会一直变大。 

#### 3、其他两种作用域放值

```java
@RequestMapping(value = "/testMap",method = RequestMethod.GET)
public String testMap(Map<String,Object> map){
    //这里只要是map类型的就都可以
    map.put("mapUser","mapUser");
    return "success";
}

@RequestMapping(value = "/testModel",method = RequestMethod.GET)
public String testMap(Model model){
    model.addAttribute("modeUser","modelUser");
    return "success";
}
```

### 七、视图解析

一共有三种视图形式：

1、`InternalResourceView:` 内部资源视图，通常我们的返回值直接是视图资源解析器下面的文件名，或者说是`"forward:success"`的形式。

2、`JstlView` : 继承自`InternalResourceView` ,功能更加强大，当前端使用数据的循环时，就可以用这种形式了

3、`redirectView:`  重定向视图，要在返回的页面字符串前面加上：`"redirect:"`

```java
@RequestMapping(value = "/testRedirect",method = RequestMethod.GET)
public String testRedirect(Model model){
    model.addAttribute("modeUser","modelUser");
    return "redirect:/test/redirect.jsp";
    //注意的是，请求重定向所访问的网页都是web根目录下的，但是并不能包含WEB-INF目录下。
}
```

**为啥要使用这个呢？比如我们先使用显示数据的请求到页面，当我们添加数据后需要返回到显示数据的页面时怎么搞呢？转发吗？不现实啊，路由也不支持转发请求啊，那这个时候就使用重定向功能了。**

### 八、spring配置文件补充

#### 1、核心控制器

```xml
<!--设置核心控制器，即我们后面发出的所有请求，都将经过核心控制器进行匹配-->
<servlet>
    <servlet-name>dispatcher</servlet-name>
    <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
    <init-param>
        <param-name>contextConfigLocation</param-name>
        <!--设置配置文件的路径，classpath表示类路径，也就是我们的src目录下，再具体定义到相关文件下-->
        <param-value>classpath:conf/springMVC.xml</param-value>
    </init-param>
    <!--配置servlet加载的时间，数字只能是正数才有效果，
    如果是负数或者是0都是没有效果的，
    如果有多个servlet，配置的数字不能相同-->
    <load-on-startup>1</load-on-startup>
</servlet>
<servlet-mapping>
    <servlet-name>dispatcher</servlet-name>
    <!--表示匹配所有项目路径的请求，交给controller进行处理，但是 .jsp不会匹配-->
    <url-pattern>/</url-pattern>
</servlet-mapping>
```

####  2、字符过滤器

```xml
<filter>
    <filter-name>CharacterEncodingFilter</filter-name>
    <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
    <init-param>
        <param-name>encoding</param-name>
        <param-value>UTF-8</param-value>
    </init-param>
</filter>
<filter-mapping>
    <filter-name>CharacterEncodingFilter</filter-name>
    <url-pattern>/*</url-pattern>
</filter-mapping>
```

> 需要说明的是，在配置字符编码的时候，应当将过滤器配置到所有过滤器的最顶部，否则，将失去作用。

### 九、访问静态资源配置

```xml
<!--设置默认的servlet,让请求静态资源能够支持-->

<!--这是因为<mvc:default-servlet-handler/>
将在SpringMVC上下文中定义一个DefaultServletHttpRequestHandler，
这个Handler的作用是去Servlet容器查找默认的Servlet来响应静态文件，
而这会导致配置文件映射器和适配器失效，从而Controller失效，
即RequestMapping下设置的方法失效了，从而界面无法跳转。-->
<mvc:default-servlet-handler/>

<!--注解驱动，支撑许多注解的使用-->
<mvc:annotation-driven/>
```

### 十、json的处理

> json的处理关键在于引入依赖包的版本问题，否则可能就会出现问题

步骤如下：

#### 1、引入依赖

版本问题需要注意

```xml
<dependency>
    <groupId>com.fasterxml.jackson.core</groupId>
    <artifactId>jackson-core</artifactId>
    <version>2.11.2</version>
</dependency>
<dependency>
    <groupId>com.fasterxml.jackson.core</groupId>
    <artifactId>jackson-databind</artifactId>
    <version>2.11.2</version>
</dependency>
<dependency>
    <groupId>com.fasterxml.jackson.core</groupId>
    <artifactId>jackson-annotations</artifactId>
    <version>2.11.2</version>
</dependency>
```

#### 2、一定的注解

```xml
<!--注解驱动，支撑许多注解的使用-->
<mvc:annotation-driven/>
```

####  3、controller

```java
@RequestMapping(value = "/testJson")
@ResponseBody
public User json(){
   User user = new User();
   user.setId(11);
   user.setUserName("javaZeng");
   return user;
}
```

额外需要注意的：如果不能正确使用，尝试如下：

![image-20200913205203372](one\image-20200913205203372.png)

### 十一、文件上传下载

#### 1、文件的下载

前端请求：

```html
<a href="download">下载图片测试</a>
```

后端controller：

```java
@RequestMapping("/download")
public ResponseEntity<byte[]> getImg(HttpSession session) throws IOException{
    //获取服务器的真实地址
    String serverPath = session.getServletContext().getRealPath("img");
    String realPath = serverPath + File.separator + "1.jpg";
    //得到输入流
    FileInputStream is = new FileInputStream(realPath);
    //设置主体参数,获取文件的最大字节数
    byte[] body =new byte[is.available()];
    //将信息传入字节中
    is.read(body);
    //设置请求头信息
    HttpHeaders header = new HttpHeaders();
    header.add("Content-Disposition","attachment;filename=my.jpg");
    //设置请求的状态
    HttpStatus status = HttpStatus.OK;
    ResponseEntity<byte[]>  entity = new ResponseEntity<byte[]>(body,header,status);
    return entity;
}
```

#### 2、文件的上传

前端代码：

```html
<h3>文件的上传</h3>
<form action="up" method="post" enctype="multipart/form-data">
    文件：<input type="file" name="uploadFile"/>
    描述：<input type="text" name="desc"/>
    <input type="submit" value="上传提交"/>
</form>
```

引入依赖：

```xml
<!--文件上传的相关包-->
<dependency>
    <groupId>commons-io</groupId>
    <artifactId>commons-io</artifactId>
    <version>2.6</version>
</dependency>

<dependency>
    <groupId>commons-fileupload</groupId>
    <artifactId>commons-fileupload</artifactId>
    <version>1.3.1</version>
</dependency>
```

进行配置：

```xml
<!--设置的id不能改变，因为容器中药通过这个id来解析文件内容的-->
<bean id="multipartResolver" class="org.springframework.web.multipart.commons.CommonsMultipartResolver">
    <!--设置字符集，这里字符集要与页面的字符统一-->
    <property name="defaultEncoding" value="UTF-8"/>
    <!--设置最大能上传的文件大小-->
    <property name="maxUploadSize" value="8888888"/>
</bean>
```

后端代码：进行了三种方法的使用

```java
 /**
    * @Description:  这里需要注意的是，我们前端是file，
    *                这里是MultipartFile,因此是需要一个文件解析器来解析的
    * @Param: [uploadFile, desc]
    * @return: java.lang.String
    * @Author: 曾小松
    * @Date: 2020/9/14
    */
    @RequestMapping(value = "/oldUp",method = RequestMethod.POST)
    public String oldUpFile(MultipartFile uploadFile,String desc,HttpSession session) throws IOException{
        //获取文件名
        String filename = uploadFile.getOriginalFilename();
        //获取要上传的地址
        String uploadPath = session.getServletContext().getRealPath("upload");
        //对图片的名字进行设置,防止命名重复
        String changeName = UUID.randomUUID() + filename.substring(filename.lastIndexOf("."));
        //获取真实的地址
        String realPath = uploadPath + File.separator + changeName;
        //封存到file中
        File file = new File(realPath);
        //获取输入流
        InputStream inputStream = uploadFile.getInputStream();
        //从输入流中获取文件到输出流中
        OutputStream outputStream = new FileOutputStream(file);

        //这里是进行单个字节的传输
//        int b;
//        //循环将字节读入输出流中
//        while ((b = inputStream.read())!= -1){
//            outputStream.write(b);
//            System.out.println(b);
//        }
        //这里进行多个字节的传输
        int i=0;
        byte[] b = new byte[1024];
        while ((i = inputStream.read(b))!= -1){
            outputStream.write(b,0,i);
        }
        //关闭输入输出流
        inputStream.close();
        outputStream.close();
        return "success";
    }

    @RequestMapping(value = "/up",method = RequestMethod.POST)
    public String upFile(MultipartFile uploadFile,String desc,HttpSession session) throws IOException{
        //获取文件名
        String filename = uploadFile.getOriginalFilename();
        //获取要上传的地址
        String uploadPath = session.getServletContext().getRealPath("upload");
        //对图片的名字进行设置,防止命名重复
        String changeName = UUID.randomUUID() + filename.substring(filename.lastIndexOf("."));
        //获取真实的地址
        String realPath = uploadPath + File.separator + changeName;
        //封存到file中
        File file = new File(realPath);
        //这里表示里面已经做了封装，直接使用即可
        uploadFile.transferTo(file);
        return "success";
    }
```

### 十二、拦截器相关

> 首先明确拦截器的作用是在哪里？ 请求从web浏览器端发送到后端后，先经过web.xml中的监听器，过滤器，然后交给dispatchServlet进行处理，处理后就经过拦截器，然后由拦截器（interceptor）控制请求在controller中的进程。

#### 1、自定义拦截器

拦截器（interceptor）通过实现接口`HandlerInterceptor` 或者是继承`HandlerInterceptorAdapter` 如下：

```java
package com.zxs.interceptor;


import org.springframework.web.servlet.HandlerInterceptor;
import org.springframework.web.servlet.ModelAndView;
import org.springframework.web.servlet.handler.HandlerInterceptorAdapter;

import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

/*
 *@author by java开发-曾
 *2020/9/14 16:47
 *文件说明:这是创建的拦截器
 */
public class FirstInterceptor implements HandlerInterceptor{
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        //这个方法在controller中的return之前执行，如果要顺利执行，就应该return true
        //如果返回的是false,后面两个方法都将不能执行
        System.out.println("first，pre");
        return true;
    }

    public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView modelAndView) throws Exception {
      //这个方法在return之后执行，也就是创建了modeAndView之后，但是还没有对客户端进行响应
        System.out.println("first，post");
    }

    public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) throws Exception {
        System.out.println("first，after");
        //这个方法类似于在finally中进行执行，
    }
}
```

配置信息：

```xml
<!--配置拦截器，这里将会对所有的请求具有拦截作用-->
<mvc:interceptors>
    <bean class="com.zxs.interceptor.FirstInterceptor"></bean>
    <bean class="com.zxs.interceptor.SecondInterceptor"/>
</mvc:interceptors>
```

如果对指定请求进行配置，按照如下进行：

```xml
<mvc:interceptors>
    <mvc:interceptor>
        <!--路径按照controller里面的信息写-->
        <mvc:mapping path="/testJson"/>
        <bean class="com.zxs.interceptor.FirstInterceptor"/>
    </mvc:interceptor>
    <mvc:interceptor>
        <mvc:mapping path="/user/name"/>
        <bean class="com.zxs.interceptor.SecondInterceptor"/>
    </mvc:interceptor>
</mvc:interceptors>
```

值得注意的是：当一个请求遇到多个拦截器的时候，满足如下规则：

1、当所有的`preHandle`返回的是true时，执行顺序为：

`preHandle`： 按照配置顺序从头到尾执行

`postHandle`:   按照配置顺序逆序执行

`afterCompletion`：按照配置顺序逆序执行

2、当其中有的`preHandle`返回的值为false时，执行顺序为：

`preHandle`： 按照配置顺序从头到尾执行到false后，后面的将不再执行

`postHandle`:   全部都不执行

`afterCompletion`：从返回false的前一个拦截器开始逆序执行。

3、当其中所有的`preHandle`返回的值为false时，执行顺序为：

`preHandle`： 按照配置顺序执行第一个

`postHandle`:   全部都不执行

`afterCompletion`：全都不执行

> 具体原因看源码解析

### 十三、异常处理

系统默认为：`DefaultHandlerExceptionResolver` 通过这个，我们可以去查找常用的异常有哪些，

当然，为了方便用户看懂，我们需要自定义异常类，使用：`SimpleMappingExceptionResolver`

配置如下：（注意的是：这里配置的异常是我们内部异常）

```xml
<!--配置我们的异常信息-->
<bean class="org.springframework.web.servlet.handler.SimpleMappingExceptionResolver">
    <property name="exceptionMappings">
        <props>
            <!--这里的key必须是全限定名-->
            <prop key="java.lang.ArithmeticException">error</prop>

        </props>
    </property>
</bean>
```

如果是遇到405，404之类的异常，需要到web.xml中进行配置

```xml
<error-page>
    <error-code>405</error-code>
    <location>/WEB-INF/views/405.jsp</location>
</error-page>

<error-page>
    <error-code>408</error-code>
    <location>/WEB-INF/views/405.jsp</location>
</error-page>
```

### 十四、关于整合

配置spring的容器：

在web.xml中：

```xml
<listener>
    <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
</listener>
<!--设置配置文件的位置-->
<context-param>
    <param-name>contextConfigLocation</param-name>
    <param-value>classpath:conf/spring.xml</param-value>
</context-param>
```

这里需要注意的是，spring容器和springMVC容器两个容器里面都有扫描组件的操作，如果都扫描，就会对一个类配置两次，因此在springMVC中尽量配置的是controller：

```xml
<!--扫描组件的形式，用来扫描@Controller此类注解-->
<context:component-scan base-package="com.zxs.controllers"></context:component-scan>
```

在spring的配置文件中，尽量不要controller:

```xml
<context:component-scan base-package="com.zxs">
    <context:exclude-filter type="annotation" expression="org.springframework.stereotype.Controller"/>
</context:component-scan>
```

> 那么疑问来了，既然是两个组件，controller里面怎么来访问spring容器的bean呢？自动装配是怎么来实现的呢？简单说明的就是：spring容器属于父容器，springMVC属于子容器，子容器是可以访问父容器的，但是父容器是不能访问子容器的。