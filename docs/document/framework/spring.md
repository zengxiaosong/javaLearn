# Spring框架学习

## 一、什么是框架？

具有约束性（*具有标准，面向对象等*），支撑性（*如`Mybatis`能够访问数据库，就是进行了封装*），用来实现功能的半成品(只是提供了架子，还不是一个完整的项目，还需要自己去实现功能，业务逻辑等)。

## 二、spring入门

简单来讲，`spring`主要包含`IOC`和`AOP`，那这两个东西是啥呢？ `IOC`叫依赖注入，`AOP`叫面向切面编程，对于`IOC`来说，也就是不用去创建对象，创建对象的工作由`spring`进行，并且对这个对象的生命周期进行管理，`AOP`与之相对应的叫做`OOP`(面向对象编程)，`AOP`是他的扩充。

## 三、入门实例

1、在`IDEA`中用maven创建一个简单的spring项目架构，这个`csdn`网上有很多教程，我就不一一细说，当然，要先去给`IDEA`配置`maven`很关键。下面是一些关键截图和配置信息：（说明下为什么要用maven依赖：有的同学去找jar包可能是去spring官网上面下载，或者去maven官网上面下载，当然，这些都可以，但是在搭建项目的过程中真的很浪费时间，如果我用maven,只需要引入依赖，他自己就给我下载好了这些东西，这不是很爽吗）

![image-20200820151602970](one\image-20200820151602970.png)

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

## 四、IOC的相关注入(配置文件)

所谓注入，也就是我们在注入对象的时候，会给对象的属性分配值，但是值得类型不同，有字面量（也就是能用字符串表达的） 以及一些引用类型，数组，列表，集合等元素，级联操作，等情况，那这些怎么来操作呢？下面分别介绍下

### 1、简单字面量表示

简单字面量表示，也就是如图上面我们入门例子操作的那样的做法，但是这里我们主要是介绍几种不同形式的方法来使用，一般来讲，依赖注入可以通过几种形式，前面我们用的`setter`方法来赋值，现在我们用构造方法赋值的形式进行注入，在前面项目的基础上进行内容的增加。

`ApplicationContext.xml`:

```xml
<bean id="person1" class="com.zxs.beans.Person">
    <constructor-arg name="id" type="java.lang.Integer" index="0" value="1"/>
    <constructor-arg name="name" type="java.lang.String" index="1" value="小红"/>
</bean>
```

`test`文件上面：

```java
Person person1 = ac.getBean("person1", Person.class);
System.out.println(person1);
```

> 下面我们来解释下代码的含义：这里我们出现了一个新的标签`<constructor-arg/>` 标签里面有如 name  type  index value 这些属性，具体含义，就如同字面，不做解释了。这个标签就是构造函数中对应的各个形参。

现在我们换一种形式，用p标签来表示，先来看用法：

在`applicationContext.xml`上面加上`xmlns:p="http://www.springframework.org/schema/p"`变成下面这种形式

```xml
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:p="http://www.springframework.org/schema/p"
       xsi:schemaLocation="http://www.springframework.org/schema/beans                              http://www.springframework.org/schema/beans/spring-beans.xsd">
```

添加一个新的person注入对象如下:

```xml
<bean id="person2" class="com.zxs.beans.Person" p:id="2222" p:name="小星星"></bean>
```

做个测试：（实际效果就不展示了）

```java
Person person2 = ac.getBean("person2", Person.class);
System.out.println(person2);
```

### 2、引用类型的级联

当然，这种引用方式也是有好几种的，先来看例子，我们先使用**p标签进行引用**

先增加一个Teacher类如下所示：

```java
package com.zxs.beans;
/*
 *@author by java开发-曾
 *2020/8/21 23:49
 *文件说明：
 */
public class Teacher {
    private Integer tid;
    private String tname;
    private String tsex;

    public Teacher() {
    }

    public Teacher(Integer tid, String tname, String tsex) {
        this.tid = tid;
        this.tname = tname;
        this.tsex = tsex;
    }

    public Integer getTid() {
        return tid;
    }

    public void setTid(Integer tid) {
        this.tid = tid;
    }

    public String getTname() {
        return tname;
    }

    public void setTname(String tname) {
        this.tname = tname;
    }

    public String getTsex() {
        return tsex;
    }

    public void setTsex(String tsex) {
        this.tsex = tsex;
    }

    @Override
    public String toString() {
        return "Teacher{" +
                "tid=" + tid +
                ", tname='" + tname + '\'' +
                ", tsex='" + tsex + '\'' +
                '}';
    }
}
```

再person类上面增加teacher的属性，并要同时增加setter/getter方法，修改toString() 以及构造方法，都要加上teacher属性。

在`applicationContext.xml`：

```xml
<bean id="person3" class="com.zxs.beans.Person" p:id="333" p:name="王海" p:teacher-ref="teacher1" ></bean>

<bean id="teacher1" class="com.zxs.beans.Teacher">
    <property name="tid" value="11"/>
    <property name="tname" value="王老师"/>
    <property name="tsex" value="男"/>
</bean>
```

```java
Person person3 = ac.getBean("person3", Person.class);
System.out.println(person3);
```

**一般形式的引用操作：**

```java
<bean id="person4" class="com.zxs.beans.Person" >
    <property name="id" value="444"/>
    <property name="name" value="小同学"/>
    <property name="teacher" ref="teacher1"/>
</bean>
```

**使用内部类的形式进行访问**：(当然，既然是内部类了，那肯定其他地方是不能访问这个内部类的bean了)

```xml
<bean id="person5" class="com.zxs.beans.Person" >
    <property name="id" value="555"/>
    <property name="name" value="大同学"/>
    <property name="teacher">
        <bean id="teacher2" class="com.zxs.beans.Teacher">
            <property name="tid" value="222"/>
            <property name="tname" value="红老师"/>
            <property name="tsex" value="女"/>
        </bean>
    </property>
</bean>
```

### 3、List类型的引用

这里我们分别在list中插入普通类型（即字面量类型）和插入特殊的其他引用类型的数据

在`Teacher`类中加入属性：（同样，要修改构造函数、toString ,增加setter/getter）

```java
private List<String> kinds;
```

```xml
<bean id="teacher2" class="com.zxs.beans.Teacher">
    <property name="tid" value="22"/>
    <property name="tname" value="张老师"/>
    <property name="tsex" value="男"/>
    <property name="kinds">
        <list>
            <value>数学</value>
            <value>英语</value>
            <value>化学</value>
        </list>
    </property>
</bean>
```

特殊类型的，即引用类型：

在`Teacher`类中加入属性：

```java
private List<Person> personList;
```

```xml
<bean id="teacher3" class="com.zxs.beans.Teacher">
    <property name="tid" value="33"/>
    <property name="tname" value="曾老师"/>
    <property name="tsex" value="男"/>
    <property name="kinds">
        <null/>
    </property>
    <property name="personList">
        <list>
            <ref bean="person1"/>
            <ref bean="person2"/>
        </list>
    </property>
</bean>
```

### 4、数组类型的引用

数组类型的定义和List类型相同，都使用`<list>`元素集合

### 5、Set类型的引用

同样，要先引入set的属性，再增加构造方法，setter/getter:

```java
private Set<Person> personSet;
```

```xml
<bean id="teacher4" class="com.zxs.beans.Teacher">
    <property name="tid" value="44"/>
    <property name="tname" value="何老师"/>
    <property name="tsex" value="女"/>
    <property name="kinds">
        <null/>
    </property>
    <property name="personList">
        <null/>
    </property>
    <property name="personSet">
       <set>
           <ref bean="person1"/>
           <ref bean="person2"/>
       </set>
    </property>
</bean>
```

当然，如果这个集合是字面量类型的，那set这个属性该成相应的字面量类型如

```java
private Set<String> personSet;
```

改变配置文件中的set标签如下：

```xml
<set>
    <value>学生1</value>
    <value>学生2</value>
</set>
```

### 6、Map类型的引用

Map类型不同于上面的的其他几种类型了，因为他涉及到键值对，因此要更加复杂一点，下面我们还是通过例子来看看吧：

添加属性，当然，这里的value我定义的Object,如果你需要，也可以自己去定义自己需要的，

```java
private Map<String,Object> stringObjectMap;
```

```xml
<bean id="teacher5" class="com.zxs.beans.Teacher">
    <property name="tid" value="55"/>
    <property name="tname" value="何老师"/>
    <property name="tsex" value="女"/>
    <property name="kinds">
        <null/>
    </property>
    <property name="personList">
        <null/>
    </property>
    <property name="personSet">
      <null></null>
    </property>
    <property name="stringObjectMap">
        <map>
            <entry key="111" value="张强"/>
            <entry key="222" value="111"/>
            <entry key="333" value-ref="person1"/>
        </map>
    </property>
</bean>
```

当然，对于前面的几种List, set, Map 都是内部的形式表示，外部想重复使用这些数据，是不能的，下面就介绍一种方法能够外部访问：

```xml
<util:list id="list">
    <ref bean="person1"/>
</util:list>

<util:map id="map">
    <entry key="hh" value="11"/>
</util:map>

<util:set id="set">
    <ref bean="person1"/>
</util:set>
```

> 总结下吧，当然，在以后做项目的时候，这种方式一般来说是不会用的，如果项目的业务逻辑比较盘庞大，这样配置就太麻烦了，所以后面会降到用注解，组件的方式来配置。

## 五、高级bean的配置

### 1、bean的作用域（scope）

什么叫bean的作用域呢？这里做下解释说明：这里的话，我们先简单介绍两种，singleton(单例)，prototype(多例)，下面结合代码来做分别的解释吧。

![](one\20160417164310654.png)

#### （1）singleton(单例)

`ApplicationContext.xml:`(*创建类的过程，后面非必要的情况下，就省略了*)

```xml
<bean id="stuedent1" class="com.zxs.scope.Student" scope="singleton">
    <property name="sid" value="111"/>
    <property name="sname" value="佳佳"/>
</bean>
```

> **这里必须要说的是，spring容器在构建bean的时候，默认就是单例模式，这点非常重要，要记住**

来看看怎么测试的：

```java
@Test
public void myTest(){
    ApplicationContext ac = new ClassPathXmlApplicationContext("applicationContext.xml");
    Student student1 = ac.getBean("student1", Student.class);
    System.out.println(student1);

    Student student2 = ac.getBean("student1", Student.class);
    System.out.println(student2);
}
```

看看效果如何：

![image-20200823110654676](one\image-20200823110654676.png)

从上面的效果上，我们可以看出，spring容器在构建bean的时候，只构建了一次。

#### （2）prototype(多例)

多例就是每次通过`getbean()`获取的时候，都会新构建一个对象，类似于new。

```xml
<bean id="student1" class="com.zxs.scope.Student" scope="prototype">
    <property name="sid" value="111"/>
    <property name="sname" value="佳佳"/>
</bean>
```

![image-20200823111102917](one\image-20200823111102917.png)

从效果上来看，两次构建的对象的内存地址是不同的，这就是多例模式。其他几种模式，现在没办法展示，后面再进行了解吧。

### 2、bean的生命周期

####  （1）简单生命周期

spring容器既然创建了bean，那么。就会对该bean的生命周期进行管理，也就是说，spring会对该bean在特定时期进行的任务（执行的方法）进行管理。

spring进行管理的过程如下：

* 通过构造器或者工厂方法创建bean 。
* 为bean设置属性以及其他bean的引用。
* 调用bean的初始化方法。
* bean的使用。
* 当容器关闭时，bean被摧毁了。

接下来，我们就对这几种方法进行代码化，：

先来创建一个bean,  具体部分的解释，我就放在代码里面了，请认真看代码：

```java
package com.zxs.live;

/*
 *@author by java开发-曾
 *2020/8/23 14:59
 *文件说明：
 */
public class Person {

    private Integer id;
    private String name;

    public Person() {
        System.out.println("1：创建生成");
    }

    public Person(Integer id, String name) {
        this.id = id;
        this.name = name;
    }

    public Integer getId() {
        return id;
    }

    public void setId(Integer id) {
        System.out.println("2：属性配置");
        this.id = id;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    /*
        下面的init(),destroy()是原来方法中没有的方法，
        这是我们自己加上去的，并且在配置文件中进行配置的生命周期方法

     */
    public void init(){
        System.out.println("3：bean初始化");
    }

    public void destroy(){
        System.out.println("5:bean摧毁");
    }


    @Override
    public String toString() {
        System.out.println("4:bean调用");
        return "Person{" +
                "id=" + id +
                ", name='" + name + '\'' +
                '}';
    }
}
```

`applicationContext.xml:`(*配置文件中增加了 `init-method`   `destroy-method`*)

```xml
<bean id="person" class="com.zxs.live.Person" init-method="init" destroy-method="destroy" >
    <property name="id" value="11"/>
    <property name="name" value="冰冰"/>
</bean>
```

测试代码Test类：

```java
@Test
public void  liveTest(){
    ClassPathXmlApplicationContext ac = new ClassPathXmlApplicationContext("applicationContext.xml");
    Person person = ac.getBean("person", Person.class);
    System.out.println(person);
    ac.close();
}
```

实际效果如下：

![image-20200823152216691](one\image-20200823152216691.png)

#### （2）复杂生命周期

为啥叫做复杂生命周期呢？那肯定增加了新的内容嘛，也就是说在`init()`方法的前后各自增添一个调用方法，于是五步的生命周期就成了7步了。**当然，这里必须说明的是，这种配置方式会给所有的实例，也就是容器中所有的对象添加。所以，在配置的时候要注意。**

要实现这种配置，首先我们要用到一个接口：`org.springframework.beans.factory.config.BeanPostProcessor;` 让一个类来调用实现：

```java
package com.zxs.config;

import org.springframework.beans.BeansException;
import org.springframework.beans.factory.config.BeanPostProcessor;

/*
 *@author by java开发-曾
 *2020/8/23 15:30
 *文件说明：
 */
public class AfterBean implements BeanPostProcessor{
    @Override
    public Object postProcessBeforeInitialization(Object bean, String beanName) throws BeansException {
        System.out.println("init之前要做的事");
        return bean;
    }

    @Override
    public Object postProcessAfterInitialization(Object bean, String beanName) throws BeansException {
        System.out.println("init之后要做的事");
        return bean;
    }
}
```

然后将这个类配置到我们的配置文件中：（这里的id没有实际作用，可以不需要）

```xml
<bean id="beanAfter" class="com.zxs.config.AfterBean">
</bean>
```

产生的效果如下：（***再次提醒，是容器中所有对象都会被配置***）

![image-20200823155356772](one\image-20200823155356772.png)

### 3、引用外部属性文件

在java的开发过程中，我们要知道，一些静态常量，我们可以保存在静态类中，当然，也可以保存在一些属性文件中，静态类我们就不说了，那么属性文件怎么来用呢？特别是遇到像数据库这样的，用户名啊啥的。下面我们来看看实例吧。

先添加数据库配置信息如下：（配置的数据库的基本信息就是你自己的数据库基本信息了）

```xml
<bean id="dataSource" class="com.alibaba.druid.pool.DruidDataSource">
    <property name="driverClassName" value="com.mysql.jdbc.Driver"/>
    <property name="url" value="jdbc:mysql://localhost/test"/>
    <property name="username" value="root"/>
    <property name="password" value="你自己的密码"/>
</bean>
```

测试如下：

```java
@Test
public void  liveTest(){
    ClassPathXmlApplicationContext ac = new ClassPathXmlApplicationContext("applicationContext.xml");
    DruidDataSource dataSource = ac.getBean("dataSource", DruidDataSource.class);
    System.out.println(dataSource);
}
```

效果如下，成功显示：

![image-20200823163446285](one\image-20200823163446285.png)

当然，如果我们的数据库就这样写死了，是不太好的，加入属性文件一起来展示吧。先在你的资源文件下，也就是你的`applicationContext.xml`的同目录下添加文件`applcation.properties`,里面内容如下所示，当然，属性名你可以自己做修改：

```properties
# 配置数据库相关的信息
application.mysql.driver=com.mysql.jdbc.Driver
application.mysql.url=jdbc:mysql://localhost:3306/test
application.mysql.username=root
application.mysql.password=你自己的密码
```

当然，现在还是不能用的，要将这些属性引入到我们的配置文件中才行：（两种方式任选一种，不过第二种已经被弃用了，最好用第一种）

```xml
<!--引入外部属性文件-->
<context:property-placeholder location="application.properties"/>
```

```xml
<bean class="org.springframework.beans.factory.config.PropertyPlaceholderConfigurer">
    <property name="location" value="application.properties"/>
</bean>
```

最后修改我们数据库相关的配置如下：(得到同样的效果)

```xml
<bean id="dataSource" class="com.alibaba.druid.pool.DruidDataSource">
    <property name="driverClassName" value="${application.mysql.driver}"/>
    <property name="url" value="${application.mysql.url}"/>
    <property name="username" value="${application.mysql.username}"/>
    <property name="password" value="${application.mysql.password}"/>
</bean>
```

### 4、自动装配

什么是自动装配呢？自动装配针对的是啥？自动装配针对的是bean里面的引用类型属性，不去配置值，让他自动配置。如果你做过项目，肯定会遇到这样的注解： `@Autowired`, `@Resource`这就是自动装配。注解我们后面再讲，先通过配置文件的形式把原理看懂。

现在用的最多自动装配形式有两种：

					*    `byName` (通过名字自动装配)
					*    `byType` (通过类型自动装配)

#### （1）byName

在前面的基础上，添加一个Teacher类：

```java
package com.zxs.auto;

import com.zxs.scope.Student;

/*
 *@author by java开发-曾
 *2020/8/24 10:18
 *文件说明：
 */
public class Teacher {
    private Integer tid;
    private String tname;
    private Student student;

    public Teacher() {
    }

    public Teacher(Integer tid, String tname, Student student) {
        this.tid = tid;
        this.tname = tname;
        this.student = student;
    }

    public Integer getTid() {
        return tid;
    }

    public void setTid(Integer tid) {
        this.tid = tid;
    }

    public String getTname() {
        return tname;
    }

    public void setTname(String tname) {
        this.tname = tname;
    }

    public Student getStudent() {
        return student;
    }

    public void setStudent(Student student) {
        this.student = student;
    }

    @Override
    public String toString() {
        return "Teacher{" +
                "tid=" + tid +
                ", tname='" + tname + '\'' +
                ", student=" + student +
                '}';
    }
}
```

添加配置如下：

```xml
<bean id="student" class="com.zxs.scope.Student">
    <property name="sid" value="111"/>
    <property name="sname" value="佳佳"/>
</bean>


<bean id="teacher" class="com.zxs.auto.Teacher" autowire="byName">
    <property name="tid" value="111"/>
    <property name="tname" value="tom"/>
</bean>
```

可以看到的是，在teacher这个bean中，我们并没有给舒心student配置值，但是他依然能够有效果如下图：

```java
@Test
public void myTest(){
    ApplicationContext ac =new ClassPathXmlApplicationContext("applicationContext.xml");
    Teacher teacher = ac.getBean("teacher", Teacher.class);
    System.out.println(teacher);
}
```

![image-20200824105653976](one\image-20200824105653976.png)

这就是`byName`的自动装配，也就是说当我们为这个bean设置了自动装配时，spring容器给teacher这个bean的一个Student类型的名为student的属性自动去配置值，而这个值需要的bean就是同类型的id值与上面的名student相同的那一个。如果没有找到匹配的，就会报错。

#### （2）byType

顾名思义，就是指通过类型自动装配，所以这里就要注意了，因为我们在配置bean的时候，可能就会有多个同类型的bean,如果通过类型来配置这种类型的bean,就会报错，因此，这种方式使用的话，配置的值的bean在同类型下只能有一个。

```xml
<bean id="student11" class="com.zxs.scope.Student">
    <property name="sid" value="111"/>
    <property name="sname" value="佳佳"/>
</bean>


<bean id="teacher" class="com.zxs.auto.Teacher" autowire="byType">
    <property name="tid" value="111"/>
    <property name="tname" value="tom"/>
</bean>
```

> 必须说明的是，在项目中，很少会使用配置文件来做自动装配的，因为太过于繁杂，因此，我们要通过注解的形式来做。

## 六、基于注解的配置

> 如果你前面都比较了解了，那你就可以从这里就开始看了

### 1、基本注解配置

spring中，一般性的注解大致有四个，也是最常见的四个注解：

				* @Component（value=default）: 没有特殊含义，就是在spring容器中配置成一个bean,value值就是他的id值，如果没有指定，就是类名的首字母小写之后的id,如：类为Teacher,那么id就是teacher,如果指定了id就为指定后的值。
				* @Repository（value=default）: 表示应该是运用在dao层，其他含义都相同。
				* @Service（value=default）: 表示运用在service层，其他含义都相同。
				* @Controller（value=default）: 表示运用在controller层，其他含义都相同。

> **值得注意的是：这些注解都应该用在普通类上面，而不是接口或者抽象类上面。**

下面新建一个项目用一个最简单的例子来说明吧：

先看项目结构如下：

![image-20200824142313456](one\image-20200824142313456.png)

bean层:

```java
package com.zxs.bean;

import org.springframework.stereotype.Component;

/*
 *@author by java开发-曾
 *2020/8/24 14:12
 *文件说明：
 */
@Component("student")
public class Student {

    public Student() {
        System.out.println("component");
    }
}
```

Dao层:

```java
package com.zxs.dao;

/*
 *@author by java开发-曾
 *2020/8/24 14:14
 *文件说明：
 */
public interface StudentDao {
}
```

```java
package com.zxs.dao;


import org.springframework.stereotype.Repository;

/*
 *@author by java开发-曾
 *2020/8/24 14:14
 *文件说明：
 */
@Repository
public class StudentDaoImpl implements StudentDao{

    public StudentDaoImpl() {
        System.out.println("Repository");
    }
}
```

service层：

```java
package com.zxs.service;

/*
 *@author by java开发-曾
 *2020/8/24 14:17
 *文件说明：
 */
public interface StudentService {
}
```

```java
package com.zxs.service;

import org.springframework.stereotype.Service;

/*
 *@author by java开发-曾
 *2020/8/24 14:17
 *文件说明：
 */
@Service
public class StudentServiceImpl {
    public StudentServiceImpl() {
        System.out.println("service");
    }
}
```

controller层：

```java
package com.zxs.controller;

import org.springframework.stereotype.Controller;

/*
 *@author by java开发-曾
 *2020/8/24 14:18
 *文件说明：
 */
@Controller
public class StudentController {
    public StudentController() {
        System.out.println("controller");
    }
}
```

这些配置好了还不行，还需要在配置文件构建扫描组件的配置，

```xml
<!--扫描组件的配置-->
<context:component-scan base-package="com.zxs">
<!--base-package就是指这个配置具体包含到那一个包当中-->
</context:component-scan>
```

测试下：

```java
@Test
public void myTest(){
    ApplicationContext ac = new ClassPathXmlApplicationContext("applicationContext.xml");

}
```

![image-20200824142945830](one\image-20200824142945830.png)

### 2、注解有效范围配置

| 类别       | 示例                      | 说明                                                         |
| ---------- | ------------------------- | ------------------------------------------------------------ |
| annotation | com.atguigu.XxxAnnotation | 过滤所有标注了 XxxAnnotation 的类。这个规则根 据目标组件是否标注了指定类型的注解进行过滤。 |
| assignable | com.atguigu.BaseXxx       | 过滤所有 BaseXxx 类的子类。这个规则根据目标组 件是否是指定类型的子类的方式进行过滤。 |
| aspectj    | com.atguigu.*Service+     | 所有类名是以 Service 结束的，或这样的类的子类。 这个规则根据 AspectJ 表达式进行过滤。 |
| regex      | com\.atguigu\.anno\.*     | 所有 com.atguigu.anno 包下的类。这个规则根据正 则表达式匹配到的类名进行过滤。 |
| custom     | com.atguigu.XxxTypeFilter | 使用 XxxTypeFilter 类通过编码的方式自定义过滤规则，该类必须实现org.springframework.core.type.filter.TypeFilter 接口 |

包含形式：

```xml
<context:component-scan base-package="com.zxs" use-default-filters="false">
    <context:include-filter type="annotation" expression="org.springframework.stereotype.Repository"/>
    <context:include-filter type="assignable" expression="com.zxs.controller.StudentController"/>
</context:component-scan>
```

使用包含的过滤时，首先要将默认过滤定义为false,否则还是会通过全部进行扫描，type就是我们上面介绍的五种形式。

排除形式：

```xml
<!--扫描组件的配置-->
<context:component-scan base-package="com.zxs" use-default-filters="true">
   <context:exclude-filter type="assignable" expression="com.zxs.controller"/>
    <context:exclude-filter type="annotation" expression="org.springframework.stereotype.Repository"/>
</context:component-scan>
```

表示哪些包或者类不能被扫描，使用的时候 要使`use-default-filters="true"`或者为默认值，值得注意的是，包含形式与排除形式是不能混用的，无论从语法来讲，或者逻辑来件，都是不通的。

### 3、注解形式的自动装配

其实就是给需要装配的属性加一个注解，举个例子吧：

我们通过前面的dao层和service层演示下，给service层自动装配dao层的内容：

dao层：

```java
package com.zxs.dao;


import org.springframework.stereotype.Repository;

/*
 *@author by java开发-曾
 *2020/8/24 14:14
 *文件说明：
 */
@Repository("studentDao")
public class StudentDaoImpl implements StudentDao{

    public StudentDaoImpl() {
        System.out.println("Repository");
    }

    @Override
    public void userDao() {
        System.out.println("创建成功");
    }
}
```

service层：

```java
package com.zxs.service;

import com.zxs.dao.StudentDao;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.annotation.Qualifier;
import org.springframework.stereotype.Service;

/*
 *@author by java开发-曾
 *2020/8/24 14:17
 *文件说明：
 */
@Service
public class StudentServiceImpl implements StudentService{

    public StudentServiceImpl() {
        System.out.println("service");
    }

    @Autowired
    @Qualifier("studentDao")
    StudentDao studentDao;

    @Override
    public void userDao() {
        studentDao.userDao();
    }
}
```

这里就是我们通过@Autowired 实现按照类型来装配，因为这里是接口属性， @Qualifier("studentDao") 再加上这个注解，确定到名字，就对应着装配好了。当然，@Autowired(required = false) 也可以这样写，表示可以为空，不用去自动装配。其他还有注解@Resource,他是先通过名字取找，再通过类型，和上面是相反的。

## 七、AOP的相关操作

> 在看AOP之前，建议先去看看我写在java设计模式中的代理模式，更加方便理解

### 1、AOP相关的概念及术语

> 这些基本概念后面再进行讲解

### 2、基本操作

所谓通知，就是指在要执行的方法前后加上我们想要的操作并且不会改变原来程序的结构

#### （1）前置通知

这里我们使用AspectJ以及注解的方式来使用，AOP仅仅只是一个概念，真实的操作还是要依靠AspectJ来实现的。

我们先引入所需要的依赖环境：(当然，要加上前面所依赖的相关环境的)

```xml
<!--AspectJ 开始-->
<dependency>
  <groupId>org.springframework</groupId>
  <artifactId>spring-aspects</artifactId>
  <version>5.2.7.RELEASE</version>
</dependency>

<dependency>
  <groupId>org.aspectj</groupId>
  <artifactId>aspectjtools</artifactId>
  <version>1.9.5</version>
</dependency>

<dependency>
  <groupId>aopalliance</groupId>
  <artifactId>aopalliance</artifactId>
  <version>1.0</version>
</dependency>

<dependency>
  <groupId>org.aspectj</groupId>
  <artifactId>aspectjweaver</artifactId>
  <version>1.9.0</version>
</dependency>

<dependency>
  <groupId>cglib</groupId>
  <artifactId>cglib</artifactId>
  <version>3.3.0</version>
</dependency>
<!--AspectJ 结束-->
```

> 这里引入cglib的目的是如果该类没有接口，可以转换到父类中去。

既然我们做注解装配，还要加上能够解析这些注解的配置，在我们的`ApplicationContext.xml`文件中添加切面的配置：

```xml
<!--切面的自动代理-->
<aop:aspectj-autoproxy/>
```

现在在我们原先的基础上给我们的service服务层添加一个切面：

```java
package com.zxs.logger;

import org.aspectj.lang.JoinPoint;
import org.aspectj.lang.annotation.Aspect;
import org.aspectj.lang.annotation.Before;
import org.springframework.stereotype.Component;

import java.util.Arrays;

/*
 *@author by java开发-曾
 *2020/9/2 11:25
 *文件说明： 切面的配置
 */
@Component
@Aspect
public class MyLoggerAspect {


    //前置通知
    @Before("execution(public void com.zxs.service.StudentService.userDao(String))")
    public void beforeMethod(JoinPoint joinPoint){
        //获取通知方法中的方法名以及参数
        String methodName = joinPoint.getSignature().getName();
        Object[] args = joinPoint.getArgs();//获取方法中的参数
        System.out.println("这是前置的通知: methodName:"+methodName+"   args:"+ Arrays.toString(args));
    }
}
```

测试下：

```java
@Test
public void myTest(){
    ApplicationContext ac = new ClassPathXmlApplicationContext("applicationContext.xml");
    StudentService studentServiceImpl = ac.getBean("studentServiceImpl", StudentService.class);
    studentServiceImpl.userDao("java-develop");
}
```

看下效果：

![image-20200902181616420](one\image-20200902181616420.png)

> 必须说明的是，即时是作为切面加了@Aspect注解，也要加入组件的注解，因为AOP是在IOC基础上的扩展，因此也是要交给Spring容器进行管理的。

#### （2）切面表达式

在上面切面的基础上，我们对切面表达式进行更改如下：

```java
//前置通知
@Before("execution(public * com.zxs.service.StudentService.*(..))")
public void beforeMethod(JoinPoint joinPoint){
    //获取通知方法中的方法名以及参数
    String methodName = joinPoint.getSignature().getName();
    Object[] args = joinPoint.getArgs();//获取方法中的参数
    System.out.println("这是前置的通知: methodName:"+methodName+"   args:"+ Arrays.toString(args));
}
```

当然，也是具有效果的，对这种匹配，简单的说明下吧：

![image-20200902190000258](one\image-20200902190000258.png)

![image-20200902190035128](one\image-20200902190035128.png)

####  （3）后置通知

前面的前置通知是在方法执行之前进行通知的，而这里是在方法执行之后进行通知的，这里要考虑到异常的情况，也就是说，无论有没有异常，都要执行的，类似于`try...catch...`里面的finally中要执行的代码一样。

```java
/**
* @Description:  @After:这是后置的通知，执行于方法之后，也就是finally中的那一部分。
* @return: void
* @Author: java-zeng
* @Date: 2020/9/2
*/
@After(value = "execution(public * com.zxs.service.StudentService.add(..))")
public void afterMethod(){
    System.out.println("这是后置的通知");
}
```

#### （4）返回通知

```java
/**
* @Description: 知识返回通知，执行之后的返回值
* @Param: [result]
* @return: void
* @Author: 曾小松
* @Date: 2020/9/2
*/
@AfterReturning(value = "execution(public * com.zxs.service.StudentService.add(..))",returning = "result")
public void afterReturningMethod(Object result){
    System.out.println("方法执行的结果是："+result);
}
```

#### （5）异常通知

```java
/**
* @Description: 这是异常通知，表示异常信息时，要执行的方法
* @Param: [ex]
* @return: void
* @Author: 曾小松
* @Date: 2020/9/2
*/
@AfterThrowing(value = "execution(public * com.zxs.service.StudentService.add(..))",throwing = "ex")
public void afterThrowing(Exception ex){
    System.out.println("异常信息为："+ex);
}
```

#### （6）环绕通知

> 与我们前面所说的代理模式极为类似

```java
@Around(value = "execution(public * com.zxs.service.StudentService.add(..))")
public Object aroundMethod(ProceedingJoinPoint joinPoint){
    Object result;
    try {
        System.out.println("环绕中的前置");
        result = joinPoint.proceed();
        System.out.println("环绕中的返回:"+result);
        return result;
    } catch (Throwable throwable) {
        throwable.printStackTrace();
        System.out.println("环绕中的异常");
    }finally {
        System.out.println("环绕中的后置");
    }
    return null;
}
```

#### （7）公共切入点

也就是我们设置了一个切入点，其他的通知中可以重用这个切入点：

```java
//加上公共切入点
@Pointcut("execution(public * com.zxs.service.StudentService.add(..))")
public void test(){

}

//前置通知,这里重用了上面的公共切入点
@Before("test()")
public void beforeMethod(JoinPoint joinPoint){
    //获取通知方法中的方法名以及参数
    String methodName = joinPoint.getSignature().getName();
    Object[] args = joinPoint.getArgs();//获取方法中的参数
    System.out.println("这是前置的通知: methodName:"+methodName+"   args:"+ Arrays.toString(args));
}
```

#### （8）切面优先级

对于同一个要执行的方法，我们可以添加多个切面，那么这些切面执行的顺序是怎么样的呢？我们可以通过添加切面的优先级别来设置，具体就是通过添加注解`@Order(num)`，这里的num就是我们要设置的数字，默认是int基本类型的最大值，这里数字越小，级别越优先，当然，0和负数的优先级别是一致的。

在该切面添加注解如下：

```java
@Component
@Aspect
@Order(5)
public class MyLoggerAspect {
```

设置另一个切面：

```java
package com.zxs.logger;

import org.aspectj.lang.annotation.Aspect;
import org.aspectj.lang.annotation.Before;
import org.springframework.core.annotation.Order;
import org.springframework.stereotype.Component;

/*
 *@author by java开发-曾
 *2020/9/2 20:07
 *文件说明：
 */
@Component
@Order(8)
@Aspect
public class LoggerTest {

    @Before("execution(public * com.zxs.service.StudentService.add(..))")
    public void before(){
        System.out.println("这是测试优先级别的切面");
    }
}
```

#### （9）使用xml方式进行配置：

先写一个切面的类如下：(这只是一个普通类，现在我们来配置形成aspect)

```java
package com.zxs.logger;

/*
 *@author by java开发-曾
 *2020/9/2 20:11
 *文件说明：使用纯xml的形式来配置切面
 */
public class LoggerXml {

    //这是我们先设置的前置方法
    public void beforeMethod(){
        System.out.println("这是运用纯种xml的形式来配置xml");

    }
}
```

在applicationContext.xml中进行配置:

```xml
<!--用xml的形式来配置切面-->

<!--将切面配置成bean-->
<bean id="loggerXml" class="com.zxs.logger.LoggerXml"></bean>

<aop:config>
    <aop:aspect ref="loggerXml">
        <aop:before method="beforeMethod" pointcut="execution(public * com.zxs.service.StudentService.add(..))"></aop:before>
    </aop:aspect>
</aop:config>
```

再对原方法进行测试，就可以了。

## 八、spring中Jdbc的相关操作

这里我们是使用的maven方式，引入依赖：

```xml
<!--druid连接池-->
<dependency>
  <groupId>com.alibaba</groupId>
  <artifactId>druid</artifactId>
  <version>1.1.6</version>
</dependency>

<!--mysql数据源-->
<dependency>
  <groupId>mysql</groupId>
  <artifactId>mysql-connector-java</artifactId>
  <version>5.1.32</version>
</dependency>

<!--jdbcTemplate-->
<dependency>
  <groupId>org.springframework</groupId>
  <artifactId>spring-jdbc</artifactId>
  <version>5.2.7.RELEASE</version>
</dependency>
```

现在，我们使用前面提及的引用外部属性文件的方式配置数据源：

```xml
<!--数据源-->
<bean id="dataSource" class="com.alibaba.druid.pool.DruidDataSource">
    <property name="driverClassName" value="${jdbc.driver}"/>
    <property name="url" value="${jdbc.url}"/>
    <property name="username" value="${jdbc.username}"/>
    <property name="password" value="${jdbc.password}"/>
</bean>
```

再将数据源配置到我们的jdbctemplate中：

```xml
<!--配置jdbcTemplate-->
<bean id="jdbcTemplate" class="org.springframework.jdbc.core.JdbcTemplate">
    <property name="dataSource" ref="dataSource"/>
</bean>
```

配置成功后，进行测试：

```xml
package com.zxs.test;

import org.junit.Test;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;
import org.springframework.jdbc.core.JdbcTemplate;

/*
 *@author by java开发-曾
 *2020/8/24 14:20
 *文件说明：
 */
public class myTest {

    ApplicationContext ac = new ClassPathXmlApplicationContext("applicationContext.xml");
    JdbcTemplate jdbcTemplate=ac.getBean("jdbcTemplate", JdbcTemplate.class);


    /**
    * @Description:  实现单个数据的增删改
    * @Param: []
    * @return: void
    * @Author: 曾小松
    * @Date: 2020/9/3
    */
    @Test
    public void myTest() {
        String sql = "insert into user values(null,?,?,?)";
        jdbcTemplate.update(sql,"贾鑫",52,1);
    }
}
```

当然，这里不仅仅是只有一种操作，其他的操作我引用了别人写的案例，就没必要造轮子了，

链接如下：

[jdbc的相关操作]: jdbcTemplate.md
[别人的博客链接]: https://blog.csdn.net/Huangyuhua068/article/details/82142044?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522159913665419724839810606%2522%252C%2522scm%2522%253A%252220140713.130102334..%2522%257D&amp;request_id=159913665419724839810606&amp;biz_id=0&amp;utm_medium=distribute.pc_search_result.none-task-blog-2~all~top_click~default-2-82142044.first_rank_ecpm_v3_pc_rank_v4&amp;utm_term=jdbctemplate&amp;spm=1018.2118.3001.4187



## 九、声明式事务

### 1、事务的相关概念

