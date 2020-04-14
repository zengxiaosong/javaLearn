
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

        Configuration configuration = new Configuration().configure();
        SessionFactory sessionFactory =configuration.buildSessionFactory();
        Session session =sessionFactory.getCurrentSession();

        try {
            session.beginTransaction();

            Student student =new Student("小明", "男",25,"95.5");
            session.save(student);
            session.getTransaction().commit();
        }catch (Exception e){
            e.printStackTrace();
            session.getTransaction().rollback();;
        }
    }
```  
> 点击运行，查看数据库，基本的操作我们是做完了，接下来还是讲讲原理吧，可能说部分同学不知道是怎么出来的。

## hibernate基本原理  
