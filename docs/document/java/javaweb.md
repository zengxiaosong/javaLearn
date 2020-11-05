# javaWeb学习路径

## 一、说在前面

> 要学习javaweb的相关技术，那么，首先是要了解 javase,java进阶，html,css,js的相关语言的。这时候再学习这些就比较简单了。后面主要是总结一些重要的东西，其他的就不做说明了。

目前来讲，设计软件一般分为两种，B/S   C/S。那怎么理解呢？

* B/S (browser/server): 也就是指浏览器端和服务器端
* C/S(client/server):也就是指客户端和服务器端

### 1.1 web客户端技术

* html(Hypertext markup language): 超文本标记语言，一种应用，也是一种规范。用来显示内容

* css(Cascading Style Sheet) : 用来表示文件样式的语言

* DOM(document object model) : 文档对象模型，是w3c组织推荐的处理可扩展标志语言的标准编程接口。在网页上，组织页面（或文档）的对象被组织在一个树形结构中，用来表示文档中对象的标准模型。就是DOM,如图：

  ![image-20201104105629512](img\image-20201104105629512.png)

### 1.2 web服务器端技术

一个动态的网站的开发，离不开服务端的技术的，目前最常用的服务端技术主要有 ASP,   ASP.NET,  PHP, JSP。这些大致了解下就行，这里我们主要看看jsp的相关技术。

JSP是以java为基础的，可以在Servlet和javaBean的支持下，完成强大的功能，jsp可以被预编译，提高了程序的运行速度，这里必须要说明的是：jsp = java + html,就是指在html中嵌入了java代码。实际上，jsp也是一个servlet,客户端发起请求，服务端将jsp编译为.class文件，最后响应客户端并输出html。

### 1.3 web的工作机制

**工作流程**

* 客户端通过浏览器发出访问页面的url地址，经过地址进行解析，找到ip和端口，向该web服务器发起请求。
* web服务器根据发来的请求，把url地址转换为页面所在服务器上的文件名称，找到相应的文件。
* 如果url指向html静态页面，web服务器就将该文档直接返回给浏览器端。由浏览器解释执行，如果是动态的jsp,则服务器在第一次访问的时候就将文件编译为.class文件，然后运行，将程序的执行结果（也就是html）发送到浏览器端进行解释执行。
* 如果存在相应的sql,通过java代码嵌入到jsp页面中，最终执行成为静态的html。

**tomcat执行解析**

* 调用tomcat容器，生成相关的实例，即对应的servlet。
* tomcat中有相关的server.xml,web.xml,这是全局配置，针对每一个webapp，都会生成对应servletContext，这就是每个应用程序的上下文。
* 通过路径映射，访问对应web的请求，会先加载每个应用程序中的tomcat中的jar,以及jdk中的jar,然后就是web_info/lib下的jar，最后访问应用程序下的web.xml，值得注意的是，我们在tomcat/conf/下也存在一个web.xml,因此，在请求映射进行加载的时候，会先加载tomcat下的web.xml，再加载应用程序下的web.xml,如果有相同的配置信息，后者会覆盖掉前者，这也就是为什么我们直接访问如： localhost:8080/webName/也能访问到index.jsp。

tomcat下具体的配置信息如下：

```xml
<welcome-file-list>
    <welcome-file>index.html</welcome-file>
    <welcome-file>index.htm</welcome-file>
    <welcome-file>index.jsp</welcome-file>
</welcome-file-list>
```

另外要说的是：在进行请求传输的过程中，是如何支持中文？在我们并没有配置的情况下。

请求的映射端口配置是在tomcat的server.xml中的，因此，只要我们修改这个里面的url字符编码，也就能够实现了，具体添加代码为：  ` URIEncoding="UTF-8"`

```xml
<Connector port="8080" protocol="HTTP/1.1"
           connectionTimeout="20000"
           URIEncoding="UTF-8"
           redirectPort="8443" />
```

#### 未解决问题

对于这个字符编码的实用场景，后面再进一步做描述。

## 二、javaweb相关组件

> 提及到相关的组件，就会和组件的生命周期以及作用域等相关知识联系在一起。

### 2.1 ServletContext

>上下文环境

**生命周期相关：**

* servletContext在服务器启动的时候就会被创建，现阶段就是指我们的tomcat启动的时候。
* servletContext是每个web应用占一份，也就是说，每次启动一个web应用，就生成一个servletContext对象。
* 对于web应用中的所有servlet,ServletContext是共享数据的。所以，他是一个全局共享的信息域。
* 当服务器关闭时，该对象销毁。

### 2.2 Servlet体系

![image-20201105133255177](img\image-20201105133255177.png)

下面看一个直接写的servlet的例子：

先在web.xml中进行配置：

```xml
<servlet>
    <servlet-name>ServletTest1</servlet-name>
    <servlet-class>com.zxs.servlet.ServletTest1</servlet-class>
    <!--下面的属性会在servletConfig中被读取-->
    <init-param>
        <param-name>name</param-name>
        <param-value>zxs</param-value>
    </init-param>
    <init-param>
        <param-name>pass</param-name>
        <param-value>123456</param-value>
    </init-param>
    <!---->
    <load-on-startup>1</load-on-startup>
</servlet>

<servlet-mapping>
    <servlet-name>ServletTest1</servlet-name>
    <!--映射路径-->
    <url-pattern>/test01</url-pattern>
</servlet-mapping>
```

继承最初的Servlet来实现：

```java
package com.zxs.servlet;

import javax.servlet.*;
import java.io.IOException;
import java.io.PrintWriter;

/*
 *@author by java开发-曾
 *2020/11/5 12:36
 *文件说明：
 */
public class ServletTest1 implements Servlet {

    ServletConfig servletConfig;
    //设置全局数据，观察init会执行几次
    Integer number= 0;


    @Override
    public void init(ServletConfig servletConfig) throws ServletException {
        this.servletConfig = servletConfig;
        System.out.println(servletConfig.getServletName());
        System.out.println(servletConfig.getInitParameter("name"));
        System.out.println(servletConfig.getServletContext());
        System.out.println("number:执行"+number++);

    }

    @Override
    public ServletConfig getServletConfig() {
        return servletConfig;
    }

    @Override
    public void service(ServletRequest servletRequest, ServletResponse servletResponse) throws ServletException, IOException {
        System.out.println("执行init");
        //这里会被多个线程进行执行，因此，该方法是非线程安全的，使用时需要注意使用全局数据。
        //该方法是用来转向的，如执行doPost,或者是doGet,但是这些方法是写在HttpServlet中的，使用时也是要使用
        //HttpServletRequest 以及HttpServletResponse
        servletRequest.setAttribute("zeng","hello Test01");
        //获取从页面中请求的数据
        String name = servletRequest.getParameter("name");
        PrintWriter writer = servletResponse.getWriter();
        //直接向前端传输数据
        writer.print(name);

        //如果是定向转发到页面，操作如下
        servletRequest.getRequestDispatcher("hello.jsp").forward(servletRequest,servletResponse);
        //从这里可以看出，转发是共用的request，以及response。

    }

    @Override
    public String getServletInfo() {
        return null;
        //不用管，根据实际情况来使用
    }

    @Override
    public void destroy() {
        System.out.println("执行 destory");
    }
}
```

> 从上面的代码中，我们看到了转发的底层实现逻辑，因此，转发实际上是共用的一个request，以及response。所以本质上是从服务器端将请求的相关数据从一个servlet转向另一个servlet中。而jsp这里就更好的说明了是html和servlet的整体。当然，在实际中，我们使用的是HttpServlet, 这个类对请求的封装是更加的全面的，因此，我们只需要重写doGet/doPost等方法就可以了。

### 2.3 转发与重定向的区别

> 从上面的代码中，我们就很明白了转发是怎么实现的了，那么重定向呢？

```java
protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
    System.out.println("进行重定向");
    System.out.println(req.getContextPath());
    resp.sendRedirect("/javaweb01/hello.jsp");
}
```

> 重定向是发生在浏览器端的行为，因此我们应该设置在响应中，也就是response,设置重定向就是浏览器端的新的一次请求，因此，地址栏发生了改变，但是并没有打开新的窗口。

什么时候使用重定向和转发呢？

> 转发使得页面地址不变，可以多次刷新，多次提交数据，因此，如果重要的数据，或者是不能重复的数据就要使用重定向，其他的使用转发问题不大。

### 2.4 四大作用域

> 首先，要清楚，有哪四大作用域，他们的生命周期，以及作用范围都有哪些？

#### 1）pageContext

* 生命周期：jsp请求时被实例化，响应结束时被销毁。
* 作用范围：整个页面，跳过这个页面则不能共用。

#### 2）request

* 生命周期：在产生请求的时候被实例，也就是在service方法被调用前实例。
* 作用范围：在整个请求链中被共用，也包括我们的请求转发（因为是共用）。

#### 3）session

* 生命周期：当调用request.getSession的时候，一直到服务器停止。
* 作用范围，一次会话，也就是从打开浏览器进入该站点到关闭浏览器（因为http是长连接，因此，断开连接在浏览器关闭的时候），作用在一个线程上。

#### 4）servletContext

* 生命周期：从web应用建立到服务器销毁。
* 作用范围，整个应用。

#### 5）访问顺序

```html
${username}
${page.username}
${request.username}
${session.username}
${application.username}

搜索顺序：page-->request-->session-->application
```

### 2.5 九大内置对象

Request,Response,Out,Session,Application,Cookie,Config,Page,Exception。

### 2.6 cookie与session

> 简单说明下，在浏览器用户第一次访问时，服务端可以通过getSession(true)产生sessionID以及cookie,然后将cookie保存在客户端，cookie中存了seessionID,然后携带访问服务端以此来进行跟踪。所以，可以使用这样的方式进行用户的登录。其次，session的生命周期是30分钟，也就是说，两次请求的时间间隔不能超过30分钟，或者自己进行销毁。

如果客户端禁用了cookie,如何登录，以此保证会话跟踪？

* url重写

```java
response.encodeURL("http://www.laozizhu.com");
response.encodeRedirectURL("http://www.laozizhu.com");
```

* 表单隐藏，通过一个隐藏的表单值来使得传入sessionID。

## 三、监听器与过滤器

Filter: 就是对请求的内容进行过滤，将满足条件的进行下一步操作，不满足的就过滤掉。

Listener: 表示监听器，就是指哨兵模式，也就是接口回调，在我们具体实现的监听中写入要监听的内容，然后在事件的发生过程中调用接口，来行成事件的监听，实现监听过程。



> 大致写到这里，后面再进行补充吧！！！



