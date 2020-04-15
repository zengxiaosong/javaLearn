
# hibernate-xml
---

>这里我们讲讲以xml配置文件的方式来搭建hibernate框架

## 入门-简单配置  

> 引入所需要的jar包 [点击这里，就能去我的github上下载所需要的jar包](https://github.com/zengxiaosong/javaRepository)  

下载好jar包之后，将这些包复制到我们的项目中的==lib==文件中，（如何搭建web项目我们前面已经讲过，自己去看）    

> 提示：如果引入的jar包不能正常使用，右击==lib==  \>添加为库。  

![配置lib](one/lib.png)

> 接下来就是去配置我们的 ***hibernate.cfg.xml***  在我们的src目录下新建hibernate.cfg.xml,具体配置的文件内容如下：  

``` xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE hibernate-configuration PUBLIC
        "-//Hibernate/Hibernate Configuration DTD 3.0//EN"
        "http://www.hibernate.org/dtd/hibernate-configuration-3.0.dtd">

<hibernate-configuration>
    <session-factory>
        <!--定义数据库链接四要素 根据数据库的需要进行自己选择驱动 -->
        <property name="hibernate.connection.driver_class">com.mysql.jdbc.Driver</property>
        <property name="hibernate.connection.username">root</property>
        <property name="hibernate.connection.password">123456</property>
        <!--链接到我的数据库-->
        <property name="hibernate.connection.url">jdbc:mysql://127.0.0.1:3306/hibernate_test</property>

        <!-- 定义方言 -->
        <property name="dialect">org.hibernate.dialect.MySQL57Dialect</property>

        <!-- 定义C3PO连接池 -->
        <property name="hibernate.connection.provider_class">org.hibernate.c3p0.internal.C3P0ConnectionProvider</property>


        <!-- 注册上下文 -->
        <property name="hibernate.current_session_context_class">thread</property>

        <!-- 配置数据库自动创建表和显示sql -->
        <property name="hibernate.hbm2ddl.auto">update</property>
        <property name="hibernate.show_sql">true</property>
        <property name="hibernate.format_sql">true</property>

        <!-- 定义mapping文件  根据数目自行定义-->
        <mapping resource="com/zxs/bean/student.hbm.xml" />


    </session-factory>
</hibernate-configuration>
```  
> 上面代码中，我是使用的mysql数据库，使用什么数据库就看你自己的习惯了，从我们上面的代码中，我们应该知道，接下来就应该去配置我们的  ***student.hbm.xml*** 文件。以及配置我们的 ***bean /Student***  来看代码吧：  

``` student.hbm.xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE hibernate-mapping PUBLIC
        "-//Hibernate/Hibernate Mapping DTD 3.0//EN"
        "http://www.hibernate.org/dtd/hibernate-mapping-3.0.dtd">

<hibernate-mapping>
    <!-- hbm配置文件的作用是
    	（1）映射类和表
    	（2）映射属性和字段
     -->
    <class name="com.zxs.bean.Student" table="user_table">
        <id name="id" column="id">
            <!-- native由Hibernate根据底层数据库自行判断采用identity、hilo、sequence其中一种作为主键生成方式,
                 这种方式我在开法过程中经常用到，意思是把主键的生成方式交给底层数据库来决定。-->
            <generator class="native"/>
        </id>
        <property name="name" column="name"/>
        <property name="sex" column="sex"/>
        <property name="age" column="age"/>
        <property name="score" column="score"/>
    </class>

</hibernate-mapping>
```  
> 注意看代码，在class里面对应着我们的javabean,右边对应着我们的数据举重表的信息。接下来就是创建bean。  

``` java
package com.zxs.bean;

/*
 *@author by java开发-曾
 *2020/4/14 21:59
 *文件说明：
 */
public class Student {
    private Integer id;
    private String name;
    private String sex;
    private Integer age;
    private String score;

    public Student() {
    }

    public Student(String name, String sex, Integer age, String score) {
        this.name = name;
        this.sex = sex;
        this.age = age;
        this.score = score;
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

    public String getSex() {
        return sex;
    }

    public void setSex(String sex) {
        this.sex = sex;
    }

    public Integer getAge() {
        return age;
    }

    public void setAge(Integer age) {
        this.age = age;
    }

    public String getScore() {
        return score;
    }

    public void setScore(String score) {
        this.score = score;
    }

    @Override
    public String toString() {
        return "Student{" +
                "id=" + id +
                ", name='" + name + '\'' +
                ", sex='" + sex + '\'' +
                ", age=" + age +
                ", score='" + score + '\'' +
                '}';
    }
}

```  
> 接下来就是创建我们的测试文件了，创建一个测试包，在里面写如下代码：  

``` java
    @Test
    public void studentTest(){
        
		//加载hibernate.cfg.xml配置文件
        Configuration configuration = new Configuration().configure();
		//建立session工厂，
        SessionFactory sessionFactory =configuration.buildSessionFactory();
		//建立session
        Session session =sessionFactory.getCurrentSession();

        try {
		    //开启事物
            session.beginTransaction();
            Student student =new Student("小明", "男",25,"95.5");
            session.save(student);
            session.getTransaction().commit();
        }catch (Exception e){
            e.printStackTrace();
			//失败的话，对事物进行回滚。
            session.getTransaction().rollback();;
        }
    }
```  
> 点击运行，查看数据库，基本的操作我们是做完了，接下来还是讲讲原理吧，可能说部分同学不知道是怎么出来的。

## hibernate基本原理    
---

> 1.先讲下ORM的概念，（Object-Relation Map）即对象关系映射  

我们常见的数据库如NoSql下的Redis，和我们的关系数据库。甚至是表和文件等，当然，这里我主要讲讲 ***关系数据库*** 。关系数据一般来讲是以表来做设计的，对于一个表，我们可以看做是一个类，每行对应的所有列，可以看做是一个类对应的每个对象的所有属性。因为Java是面向对象做设计的。  

>  2.基本流程是怎么回事呢？  

首先用户要访问数据库中的某个表 ，但是我们是对象，然后通过hibernate框架作为中间件对数据库DB进行访问。那么这个中间过程是怎么实现的呢？  首先就是通过  ```hibernate.cfg.xml``` 对我们的数据库进行连接与配置。并配置我们有哪些对象要去做这样的操作呢，所以就有我们的 ```<mapping reasource= "----" />``` 在我们的 ```bean``` 层里面用上面讲到的```student.hbm.xml``` 对我们的bean进行一对一配置连接。这样，我们的基本配置就完成了，那怎么来操作呢？上面的 ```test``` 代码我已经做了详细的讲解了。  

> 3.session的三种状态。  

从上面的test例子中，我们可以看出，最终是我们的```session```在操作数据,那session的运行是怎么进行的呢？   

Session相关联，其有3种状态：临时状态（transient）：对象在内存中孤立存在，不与数据库中的数据有任何关系，持久化状态（persistent）：对象与一个session相关联，则从临时状态变为持久化状态，游离状态（detached）：Session关闭，对象进入游离状态。当然，这里还是希望读者大致去了解下数据库连接池，这里用的c3p0连接池。  

> 4.openSession()和getCurrentSession()的异同   （总的来说一般多用后者）

 1. 数据共享  
 
openSession每次返回一个新的session，无法实现共享数据，getCurrentSsession返回一个threadLocalSessionContext对象，该对象与当前访问的thread有关，如果thread不变，则session不变。在thread首次访问时创建，故同一个thread的不同请求之间共享session，可以实现数据共享。  

2. 关闭    

openSession需要手动关闭，getCurrentSsession无需手动关闭  

3. 使用方便  

openSession无需注册，直接使用，getCurrentSsession需要在XML中进行上下文注册  

4. 数据操作  

openSession的查询操作可以在事务外进行，增删改在事务内进行，getCurrentSsession的增删改查都需要在事务内进行，使用getCurrentSession必须在配置文件中注册上下文      

> 5. session的建立   

注意：SessionFactory非常占用系统资源，并且是多线程的，故做成单例对象（只创建一次），不进行手动关闭    当我们需要使用的时候，通过  ``` getSession()```方法去获取就行。   

```
public class HbnUtil {

    //静态属性，session工厂，静态属于全局
    private static SessionFactory sessionFactory;

    /**
    * @Description:  返回session
    * @Param: []
    * @return: org.hibernate.Session
    * @Author: 曾小松
    * @Date: 2020/4/14
    */
    public static Session getSession(){
        return getSessionFactory().getCurrentSession();
    }

    /**
    * @Description:  私有方法，创建工厂
    * @Param: []
    * @return: org.hibernate.SessionFactory
    * @Author: 曾小松
    * @Date: 2020/4/14
    */
    private  static SessionFactory getSessionFactory(){
        if (sessionFactory == null || sessionFactory.isClosed()){
            //如果没有工厂，就从配置文件中去创建一个
            sessionFactory = new Configuration().configure().buildSessionFactory();
        }
        return sessionFactory;
    }

}

```

## dao层的增删改查操作   
