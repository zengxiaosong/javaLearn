# Spring框架学习

## 一、什么是框架？

具有约束性（*具有标准，面向对象等*），支撑性（*如`Mybatis`能够访问数据库，就是进行了封装*），用来实现功能的半成品(只是提供了架子，还不是一个完整的项目，还需要自己去实现功能，业务逻辑等)。

## 二、spring入门

简单来讲，`spring`主要包含`IOC`和`AOP`，那这两个东西是啥呢？ `IOC`叫依赖注入，`AOP`叫面向切面编程，对于`IOC`来说，也就是不用去创建对象，创建对象的工作由`spring`进行，并且对这个对象的生命周期进行管理，`AOP`与之相对应的叫做`OOP`(面向对象编程)，`AOP`是他的扩充。

## 三、入门实例

1、在`IDEA`中用maven创建一个简单的spring项目架构，这个`csdn`网上有很多教程，我就不一一细说，当然，要先去给`IDEA`配置`maven`很关键。下面是一些关键截图和配置信息：（说明下为什么要用maven依赖：有的同学去找jar包可能是去spring官网上面下载，或者去maven官网上面下载，当然，这些都可以，但是在搭建项目的过程中真的很浪费时间，如果我用maven,只需要引入依赖，他自己就给我下载好了这些东西，这不是很爽吗）

<img src="one\image-20200820151602970.png" alt="image-20200820151602970" style="zoom:50%;" />

2、这些做好，要修改下我们的`pom.xml`这个配置文件，因为很多东西都没啥用，还可能报错，就很离谱啊，修改后的代码如下：（代码里面部分东西我已经做解释了，后面不知道为啥的可以翻翻）

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0"
   xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
   xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd">

   <modelVersion>4.0.0</modelVersion>
   <groupId>com.zxs</groupId>
   <artifactId>springLearn</artifactId>
   <packaging>war</packaging>
   <version>1.0-SNAPSHOT</version>
   <!-- TODO project name  -->
   <name>quickstart</name>
   <description></description>

   <licenses>
      <license>
         <name>The Apache Software License, Version 2.0</name>
         <url>http://www.apache.org/licenses/LICENSE-2.0.txt</url>
         <distribution>repo</distribution>
      </license>
   </licenses>

   <dependencies>
      <!--  WICKET DEPENDENCIES -->
        <!--导入的spring依赖-->
      <dependency>
         <groupId>org.springframework</groupId>
         <artifactId>spring-context</artifactId>
         <version>5.0.10.RELEASE</version>
      </dependency>

      <!--  JUNIT DEPENDENCY FOR TESTING -->
        <!--做测试用的依赖，也就是我们用到注解@Test那里的东西-->
       <dependency>
             <groupId>junit</groupId>
             <artifactId>junit</artifactId>
             <version>3.8.2</version>
             <scope>test</scope>
       </dependency>

    </dependencies>

   <properties>
        <!--设置字符为中文字符编码-->
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
   </properties>

</project>
```

3、这些做好了，我们的项目就初步搭建好了，来看看实例吧，spring是怎么对对象进行管理的呢？ 既然管理对象，那就先创建类对吧，看看上面项目结构中的`person`类是怎么写的呢：

```java
package com.zxs.beans;

/*
 *@author by java开发-曾
 *2020/8/20 14:53
 *文件说明：
 */
public class Person {
    private Integer id;
    private String name;

    public Person() {
    }

    public Person(Integer id, String name) {
        this.id = id;
        this.name = name;
    }

    public Integer getId() {
        return id;
    }

    public void setId(Integer id) {
        this.id = id;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    @Override
    public String toString() {
        return "Person{" +
                "id=" + id +
                ", name='" + name + '\'' +
                '}';
    }
}
```

> 大家应该注意到了上面我用设计`id` 的时候用的是`Integer` 而不是基本数据类型`int` 那为什么要这么做呢？做过数据库的应该知道，我们在对字段设计的时候，可以设置是否为空，也就是`null` 那么如果我们的对象要取这个字段来进行赋值操作的话，用`int`能够是`int id=null`吗？  肯定不行的，基本数据类型不赋值也会有默认的赋值如int默认为0，double默认为0.0，null是当引用数据类型的时候默认赋的值，所以记住了，以后在创建`bean`的时候尽量避免用基本数据类型。

4、类创建好了，接下来就应该设置我们的spring管理容器了吧，来看看配置文件`applicationContext.xml`怎么写的吧：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">


    <!--配置要管理的对象
        id：表示唯一标志符，也就是我们实例化中访问的
        class表示映射的类，这里用类来创建对象
    -->
    <bean id="person" class="com.zxs.beans.Person">
        <!--对对象中的属性进行赋值，
        这些属性一定是要类里面都要有的才行，不然无效,
        当然，也可以不进行赋值操作-->
        <property name="id" value="1111"/>
        <property name="name" value="小明"/>
    </bean>
</beans>
```

5、基本的配置这些搞好了，怎么来看效果呢？这里由于不是实际项目，所以我们就写了`test`来运行看到效果：

```java
package com.zxs.beanTest;

import com.zxs.beans.Person;
import org.junit.Test;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

/*
 *@author by java开发-曾
 *2020/8/20 15:00
 *文件说明：
 */
public class MyTest {

    @Test
    public void myTest(){

        //获取容器对象，加载配置文件
        ApplicationContext ac = new ClassPathXmlApplicationContext("applicationContext.xml");
        //下转上，叫做上转型，上转下，叫做强制转换
        Person person = (Person) ac.getBean("person");
        System.out.println(person);

    }
}
```

> 当然，这里我们不一定用这种形式哈，你也可以用`main方法`来实现这个效果，但是一个类里面我们可以用多个`test`,却只能用一个`main方法` 不用说，都知道用谁了吧。点击`@Test` 左边，就能运行看到实际效果啦。

>从这个实例，我们来看看什么是控制反转，也就是说，最开始，我们是通过`new`来创建对象的，但是现在我们是通过容器，也就是将创建对象的这种行为由`程序员创建`变成了`程序自己创建`