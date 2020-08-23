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

> ##### 所谓注入，也就是我们在注入对象的时候，会给对象的属性分配值，但是值得类型不同，有字面量（也就是能用字符串表达的） 以及一些引用类型，数组，列表，集合等元素，级联操作，等情况，那这些怎么来操作呢？下面分别介绍下

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

#### （2）复杂生命周期



