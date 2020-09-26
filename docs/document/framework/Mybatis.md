# sMybatis学习路线

## 一、MyBatis相关

###  1、Mybatis的相关历史

1）MyBatis 是 Apache 的一个开源项目 iBatis, 2010 年 6 月这个项目由 Apache Software Foundation 迁移到了 Google Code，随着开发团队转投 Google Code 旗下， iBatis3.x 正式更名为 MyBatis ，代码于 2013 年 11 月迁移到 Github 

2）iBatis 一词来源于“internet”和“abatis”的组合，是一个基于 Java 的持久层框架。 iBatis 提供的持久层框架包括SQL，Maps 和 Data Access Objects（DAO）

### 2、为啥要用Mybatis

1） JDBC 

​           SQL 夹在 Java 代码块里，耦合度高导致硬编码内伤 

​           维护不易且实际开发需求中 sql 是有变化，频繁修改的情况多见 

2） Hibernate 和 JPA 

​         长难复杂 SQL，对于 Hibernate 而言处理也不容易 

​         内部自动生产的 SQL，不容易做特殊优化 

基于全映射的全自动框架，大量字段的 POJO 进行部分映射时比较困难。导致数据 库性能下降

3） MyBatis 

​        对开发人员而言，核心 sql 还是需要自己优化 

​        sql 和 java 编码分开，功能边界清晰，一个专注业务、一个专注数据

### 3、官方文档

https://github.com/mybatis/mybatis-3/

主要是用官方文档，其他作用，暂时用不到

## 二、HelloWorld之mybatis

### 1、代码步骤

导入依赖：

```xml
<dependencies>
    <dependency>
        <groupId>org.mybatis</groupId>
        <artifactId>mybatis</artifactId>
        <version>3.5.5</version>
    </dependency>
    <dependency>
        <groupId>mysql</groupId>
        <artifactId>mysql-connector-java</artifactId>
        <version>5.1.32</version>
    </dependency>
    <dependency>
        <groupId>log4j</groupId>
        <artifactId>log4j</artifactId>
        <version>1.2.17</version>
    </dependency>
    <dependency>
        <groupId>junit</groupId>
        <artifactId>junit</artifactId>
        <version>RELEASE</version>
    </dependency>

</dependencies>
```

> 值得注意的是：由于log4j还需要对应配置一个`log4j.properties`文件，这是官方给的，**文件不论放在项目哪里，文件名一定要相同**

配置如下：

```properties
# Global logging configuration
log4j.rootLogger=ERROR, stdout
# MyBatis logging configuration...
log4j.logger.org.mybatis.example.BlogMapper=TRACE
# Console output...
log4j.appender.stdout=org.apache.log4j.ConsoleAppender
log4j.appender.stdout.layout=org.apache.log4j.PatternLayout
log4j.appender.stdout.layout.ConversionPattern=%5p [%t] - %m%n
```

建立核心配置文件 mybatis-config.xml:

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">

<!--加载核心配置文件-->
<configuration>
    <environments default="development">
        <environment id="development">
            <transactionManager type="JDBC"/>
            <dataSource type="POOLED">
                <property name="driver" value="com.mysql.jdbc.Driver"/>
                <property name="url" value="jdbc:mysql://localhost:3306/test"/>
                <property name="username" value="root"/>
                <property name="password" value="密码"/>
            </dataSource>
        </environment>
    </environments>
    <mappers>
        <mapper resource="com/zxs/conf/UserMapper.xml"/>
    </mappers>
</configuration>
```

建立数据库表与之对应的bean,注意，尽量将属性名与数据库表中的字段名相对应，方便一一映射，否则的话，可能就需要取一个别名

建立UserMapper接口：

```java
public interface UserMapper {

    public User getUserById(Integer id);
}
```

建立与接口相对应的mapper数据库配置文件：

```xml
<?xml version="1.0" encoding="utf-8" ?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd" >
<mapper namespace="com.zxs.mapper.UserMapper">
    <select id="getUserById" parameterType="java.lang.Integer" resultType="com.zxs.bean.User">
        select * from USER WHERE id = #{id};
    </select>
</mapper>
```

建立测试类：

```java
@Test
public void myTest() throws IOException{

    //源加载，注意这里的配置文件最好是全限定名，否则可能会出错
    InputStream is = Resources.getResourceAsStream("com/zxs/conf/mybatis-config.xml");
    //加载工厂
    SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(is);
    //加载session
    SqlSession sqlSession = sqlSessionFactory.openSession();
    //通过代理模式，获取代理类
    UserMapper userMapper = sqlSession.getMapper(UserMapper.class);
    //做测试
    System.out.println(userMapper.getClass().getName());
    //实现功能
    User user = userMapper.getUserById(5);
    System.out.println(user);

}
```

### 2、属性讲解

 #### envriments

```xml
<!--environments: 主要是指设置的环境，也就是指我们所设计的数据库配置有哪些
                  所以这里是复数
    default: 指系统默认应该指向哪一个数据库
-->
<environments default="mysql">
    <environment id="mysql">
        <!--事务的管理，为原始的JDBC事务，当然，type也可以为 MANAGEED
            表示当前事务被能管理的管理，比如spring
        -->
        <transactionManager type="JDBC"/>
        <!--type:  pooled: 表示具备mybatis自带的数据库连接池
                   UNPOOLED: 表示不具备数据库管理 
                   JNDI: 使用上下文数据资源-->
        <dataSource type="POOLED">
            <property name="driver" value="com.mysql.jdbc.Driver"/>
            <property name="url" value="jdbc:mysql://localhost:3306/test"/>
            <property name="username" value="root"/>
            <property name="password" value="zeng071494"/>
        </dataSource>
    </environment>
    <environment id="sqlServer">
        <transactionManager type="JDBC"/>
        <dataSource type="POOLED">
            <property name="driver" value="com.mysql.jdbc.Driver"/>
            <property name="url" value="jdbc:mysql://localhost:3306/test"/>
            <property name="username" value="root"/>
            <property name="password" value="zeng071494"/>
        </dataSource>
    </environment>
</environments>
```

#### properties

```xml
<!--配置属性文件，这个和我们前面的引用资源文件类似
    在下面的environment就可以这样写：
     <property name="driver" value="${driver}"/>
-->
<properties>
    <property name="driver" value="com.mysql.jdbc.Driver"/>
</properties>
```

```xml
<!--当然，也可以使用资源文件的形式
    resource: 表示我们的资源文件 这里使用的全路径
-->
<properties resource="com/zxs/conf/mybatis.properties"></properties>
```

#### setting

```xml
<!--具体的设置有很多，可以去查看官方文档
    这里的mapUnderscoreToCamelCase：表示映射下划线转驼峰
    也就是数据库字段为：user_name    我们在java中接收的字段可以是userName
    具体有很多，查看官方文档
-->
<settings>
    <setting name="mapUnderscoreToCamelCase" value="true"/>
</settings>
```

#### typeAliases

> 具体查看官方文档吧，这个一般不用他，没有多大的必要

## 三、简单的几种crud

### 1、属性自动映射

```xml
<!--public Boolean insertUser(User user);
    parameterType是可以不用写的，mtbatis中有自动匹配的机制,
    在对参数进行匹配时会自动去参数中找
 -->
<insert id="insertUser" >
    INSERT INTO user(id,name,age,sex) VALUES (null,#{name},#{age},#{sex})
</insert>
```

```java
//加载session,
//设置成true时表示事物自动进行提交
SqlSession sqlSession = sqlSessionFactory.openSession(true);
```

```java
System.out.println("进行新增了一次："+userMapper.insertUser(new User(null,"张麻子",25,2)));
```

> 简单的说明下：对于增删改来说，对于原始返回值如果是Integer类型的话，那就是受影响的行数，但是如果返回类型是Boolean的话，那就是成功位true, 失败为false。

### 2、单个对象map映射

```java
public Map<String,Object> getUserMapById(Integer id);
```

```xml
<!--public Map<String,Object> getUserMapById(Integer id);-->
<select id="getUserMapById" resultType="java.util.Map">
    select * from user where id =#{id}
</select>
```

```java
System.out.println("map:"+userMapper.getUserMapById(3));
```

### 3、所有对象的map映射

```java
//获取所有的，来构成map,并且将id作为键
@MapKey("id")
public Map<String,Object> getAllUser();
```

```xml
<!--  public Map<String,Object> getAllUser();-->
<select id="getAllUser" resultType="com.zxs.bean.User">
    select * FROM user
</select>
```

```java
System.out.println("map集合："+userMapper.getAllUser());
```

### 4、#{}与${}的区别

#{}:   使用这种方式传递参数的话，在sql语句中会给变量值加一个`' '` 这在大部分的sql语句中是必要的，如字符串的形式，否则是不能成功的

${}:   使用这种形式的话，传递参数是直接将参数放进sql中，类似于字符串拼接，因此必要情况下需要手动加上`' '` 

> 一般情况下建议是直接使用#{ }, 但是特殊情况下除外，（模糊查询和批量删除）

### 5、获取自动生成的主键

```xml
<!--useGeneratedKeys="true"使用自动生成的主键
    keyProperty="id"  ：将主键赋值给对象的id属性
-->
<insert id="insertUser" useGeneratedKeys="true" keyProperty="id">
    INSERT INTO user(id,name,age,sex) VALUES (null,#{name},#{age},#{sex})
</insert>
```

```java
User user = new User(null,"张麻子",25,2);
System.out.println("进行新增了一次："+userMapper.insertUser(user));
System.out.println(user.getId());
```

## 四、获取参数的几种形式

### 1、单个的参数获取

> 当传入的参数为单个String类或者是基本数据类型及其包装类

`#{}`：可以以任何的名字获取参数

`${}`: 只能以${value} 或者是${_parameter}获取参数，并且，**必要情况下还要加上引号，否则运行不成功**

例子：

```xml
<!--public User getUserById(Integer id);  参数值应该是id-->
<select id="getUserById"  resultType="com.zxs.bean.User">
    select * from USER WHERE id = #{many};
</select>
```

```xml
<!-- public User getUserByName(String name);-->
<select id="getUserByName" resultType="com.zxs.bean.User">
    select * from USER where name = '${value}';
</select>
```

```xml
<!-- public User getUserByName(String name);-->
<select id="getUserByName" resultType="com.zxs.bean.User">
    select * from USER where name = '${_parameter}';
</select>
```

### 2、传参为JavaBean时

`#{}` `${}`: 都可以按照属性名获取参数值，但是要注意`${}`的引号问题，前面已经说过

### 3、当传入为多个参数时

传入多个参数有两种方式：mybatis会将多个参数映射成map的形式，访问时按照对应的键就能访问对应的值，这个时候就不能按照属性名来访问了。

（1）：按照参数顺序访问0，1，2，..... n  **（注意的是，新版本的现在是arg0,arg1,.....argN）**

（2）：按照参数访问顺序param1,param2,param3，...... paramN

```xml
<!--public User getUserByNameAndId(Integer id,String name);-->
<select id="getUserByNameAndId" resultType="com.zxs.bean.User">
    select * from USER WHERE id = #{arg0} AND name =#{arg1};
</select>
```

```xml
<!--public User getUserByNameAndId(Integer id,String name);-->
<select id="getUserByNameAndId" resultType="com.zxs.bean.User">
    select * from USER WHERE id = #{param1} AND name =#{param2};
</select>
```

```xml
<!--public User getUserByNameAndId(Integer id,String name);-->
<select id="getUserByNameAndId" resultType="com.zxs.bean.User">
    select * from USER WHERE id = '${param1}' AND name ='${param2}';
</select>
```

```xml
<!--public User getUserByNameAndId(Integer id,String name);-->
<select id="getUserByNameAndId" resultType="com.zxs.bean.User">
    select * from USER WHERE id = '${arg0}' AND name ='${arg1}';
</select>
```

### 4、自定义map时的用法

> mybatis对于多参数是将参数转化为map，然后访问键来对应的，那我们自己命名一个map就可以访问自己的键了

```xml
<!-- public User getUserByMap(Map<String,Object> map);-->
<select id="getUserByMap" resultType="com.zxs.bean.User">
    select * from USER WHERE id = #{my_id} AND  name = #{my_name};
</select>
```

```java
Map<String,Object> map = new HashMap<String, Object>();
map.put("my_id",8);
map.put("my_name","li");
System.out.println(userMapper.getUserByMap(map));
```

### 5、使用注解的方法

```java
public User getUserByNameAndId(@Param("my_id") Integer id, 
                               @Param("my_name") String name);
```

```xml
<!--public User getUserByNameAndId(Integer id,String name);-->
<select id="getUserByNameAndId" resultType="com.zxs.bean.User">
    select * from USER WHERE id = '${my_id}' AND name ='${my_name}';
</select>
```

## 五、多表对应的关系

### 1、多对一自定义映射

 在前面的基础上建立相关的部门表dept，然后在user表中加入部门的id字段，添加Dept的bean,在User类中添加dept属性，并设置相应的getter/setter.

设置相应的接口：

```java
public interface UserAndDeptMapper {

    //获取所有的员工信息以及所对应的部门
    public List<User> getUserAndDept();

}
```

设置对应mapper.xml:

```xml
<!--public User getUserAndDept();
这里使用的是左外连接的规则的，但是要有自定义的结果集
-->
<!--type表示要映射的类。即我们要处理的类是哪个-->
<resultMap id="User" type="com.zxs.bean.User">
    <!--id：表示主键
        result : 表示我们要映射的相关属性，即非主键的映射全部写在这里
    -->
    <id column="id" property="id"/>
    <!-- property:表示在类中的属性名  
         column:表示我们查出来的列名，并不一定是数据库中的字段名，也可以是我们sql语句中定义的别名
    -->
    <result property="name" column="name"/>
    <result column="age" property="age"/>
    <result column="sex" property="sex"/>
    <!--多对一在多的一方引入的结果-->
    <result column="dept_id" property="dept.dept_id"/>
    <result column="dept_name" property="dept.dept_name"/>
</resultMap>

<select id="getUserAndDept" resultMap="User">
    SELECT u.id,u.name,u.age,u.sex,d.dept_id,d.dept_name FROM user u LEFT OUTER JOIN dept d ON u.dept_id=d.dept_id
</select>
```

> 一般情况下，这种方式使用的较少

### 2、association多对一映射

```xml
<resultMap id="User" type="com.zxs.bean.User">
    <!--id：表示主键
        result : 表示我们要映射的相关属性
    -->
    <id column="id" property="id"/>
    <result property="name" column="name"/>
    <result column="age" property="age"/>
    <result column="sex" property="sex"/>
    <!--多对一在多的一方引入的结果-->
    <association property="dept" javaType="com.zxs.bean.Dept">
        <id property="dept_id" column="dept_id"/>
        <result property="dept_name" column="dept_name"/>
    </association>
```

### 3、多对一分步查询

也就说，我们先查询user 再进行dept的查询：

```xml
<!-- public Dept getDeptById(Integer id);-->
<select id="getDeptById" resultType="com.zxs.bean.Dept">
    select * from dept where did= #{id};
</select>
```

```xml
<resultMap id="user" type="com.zxs.bean.User">
    <id column="id" property="id"/>
    <result column="name" property="name"/>
    <result column="sex" property="sex"/>
    <result column="age" property="age"/>
    <!--select表示分步去对应的接口方法中进行查询
        column：表示对上面查出来的dept_id做为参数传入到select中去
    -->
    <association property="dept" select="com.zxs.mapper.DeptMapper.getDeptById" column="dept_id"/>
</resultMap>

<!-- public User getUserByStep(Integer id);-->
<select id="getUserByStep" resultMap="user">
    select id,name,sex,age,dept_id from USER where id = #{value};
</select>
```

### 4、多对一分步查询延迟

> 注意的是：延迟只对分步查询有效

在mybatis的核心配置文件中：

```xml
<settings>
    
    <!--
        lazyLoadingEnabled:表示延迟加载
        aggressiveLazyLoading：表示一次性将数据查出来，
        因此两者是互斥的，不允许同时为true/false
    -->
   
    <setting name="lazyLoadingEnabled" value="true"/>
    <setting name="aggressiveLazyLoading" value="false"/>
</settings>
```

### 5、一对多自定义

```java
public Dept getDeptToUser(Integer id);
```

```xml
<resultMap id="deptToUser" type="com.zxs.bean.Dept">
    <id column="did" property="did"/>
    <result column="dname" property="dname"/>
    <collection property="users" ofType="com.zxs.bean.User">
        <id column="id" property="id"/>
        <result column="name" property="name"/>
        <result column="sex" property="sex"/>
        <result column="age" property="age"/>
    </collection>
</resultMap>


<!--public Dept getDeptToUser(Integer id);-->
<select id="getDeptToUser" resultMap="deptToUser">
    select d.did,d.dname,u.id,u.name,u.age,u.sex from dept d LEFT JOIN user u on d.did = u.did where d.did = #{id}
</select>
```

### 6、一对多分步查询

```java
public List<User> getUserByDid(Integer did);

public Dept getDeptStep(Integer id);
```

```xml
<!-- public User getUserByDid(Integer did);-->
<select id="getUserByDid" resultType="com.zxs.bean.User">
    select * from USER WHERE did=#{did};
</select>

<resultMap id="deptStep" type="com.zxs.bean.Dept">
    <id column="did" property="did"/>
    <result column="dname" property="dname"/>
    <collection property="users" select="com.zxs.mapper.UserAndDeptMapper.getUserByDid" column="did"/>
</resultMap>

<!--public Dept getDeptStep(Integer id);-->
<select id="getDeptStep" resultMap="deptStep" >
   select did, dname from dept where did=#{id};
</select>
```

### 7、mapper中定义延迟

```xml
<resultMap id="deptStep" type="com.zxs.bean.Dept">
    <id column="did" property="did"/>
    <result column="dname" property="dname"/>
    <collection property="users" select="com.zxs.mapper.UserAndDeptMapper.getUserByDid" fetchType="eager" column="did"/>
</resultMap>
```

比如在我们的返回集中，我们在分布查询的过程中对collection /association定义属性fetchtype   = lazy / eager  就能对该查询定义是延迟或者是全部查询

## 六、动态sql

> 一般来讲，动态sql都是运用在多条件查询情况下，比如在同一个查询语句中，有的条件有，有的条件可以没有。

### 1、动态sql之if

按照原来我们进行多条件进行查找：

```java
//进行多条件查询user结果
public List<User> getUserByMany(User user);
```

```xml
<!--public List<User> getUserMany();-->
<select id="getUserByMany" resultType="com.zxs.bean.User">
    select id,name,age,sex from user
    where
    id=#{id}
    and name=#{name}
    and age=#{age}
    and sex=#{sex};
</select>
```

按照上面这种形式，我们就每次查找值的时候，就必须添加所有的条件才行，现在我们添加if条件进行查值，这样来帮助我们可以只输入部分条件来进行查找结果集

```xml
<!--public List<User> getUserMany();-->
<!--
    <if>:标签用来进行判断值是否符合条件，当为==判断时，只能是数字，
    前面为什么要加上1=1： 是因为如果id值省略的话，那么前面就会多出一个and连接符
    语句运行不通
-->
<select id="getUserByMany" resultType="com.zxs.bean.User">
    select id,name,age,sex from user
    where 1=1
    <if test="id!=null">
       and id=#{id}
    </if>
    <if test="name!=null and name!=''">
        and name=#{name}
    </if>
    <if test="age!=null">
        and age=#{age}
    </if>
    <if test="sex==0 or sex==1">
        and sex=#{sex};
    </if>
</select>
```

### 2、动态语句之where

Where 用于解决 SQL 语句中 where 关键字以及条件中第一个 and 或者 or 的问题

```xml
<select id="getUserByMany" resultType="com.zxs.bean.User">
    select id,name,age,sex from user
    <where>
        <if test="id!=null">
           and id=#{id}
        </if>
        <if test="name!=null and name!=''">
            and name=#{name}
        </if>
        <if test="age!=null">
            and age=#{age}
        </if>
        <if test="sex==0 or sex==1">
            and sex=#{sex};
        </if>
    </where>
</select>
```

### 3、动态语句之trim

Trim 可以在条件判断完的 SQL 语句前后 添加或者去掉指定的字符 

prefix: 添加前缀 

prefixOverrides: 去掉前缀 

suffix: 添加后缀 

suffixOverrides: 去掉后缀 

下面来看我的例子：

```xml
<!--
     prefix="where" :表示在这个sql代码段中增加的前缀是什么
     prefixOverrides="" :表示在这个sql代码段中要截取的前缀是什么
     suffix=""  :表示在这个sql代码段中增加的后缀是什么
     suffixOverrides="and"   :表示在这个sql代码段中要截取的前缀是什么
-->
<select id="getUserByMany" resultType="com.zxs.bean.User">
    select id,name,age,sex from user
    <trim prefix="where" suffixOverrides="and">
        <if test="id!=null">
            id=#{id} and
        </if>
        <if test="name!=null and name!=''">
            name=#{name} and
        </if>
        <if test="age!=null">
            age=#{age} and
        </if>
        <if test="sex==1 or sex==0">
            sex=#{sex} and
        </if>
    </trim>;
</select>
```

### 4、动态语句之set

这个语句用在updata table set name=#{name}..... where id=#{id}中用来代替set的，并去掉末尾或者前面中可能多出来的逗号，但是这个功能用前面的trim也能够实现，相比较而言，这个功能就显得太不足了。结合前面就知道怎么用了。

### 5、动态语句之choose

这种查询方式更类似于java中的if /else if /else   的这种条件语句

```xml
<!--  public List<User> getUserByOne(User user);
     <otherwise></otherwise> :这个标签类似于else,可以要也可以省略
-->
<select id="getUserByOne" resultType="com.zxs.bean.User">
    select id,name,age,sex from user
    <where>
        <choose>
            <when test="id!=null">
                id=#{id}
            </when>
            <when test="name!=null and name!=''">
                name=#{name}
            </when>
            <when test="age!=null">
                age=#{age}
            </when>
            <when test="sex==1 or sex==0">
                sex=#{sex}
            </when>
            <otherwise>

            </otherwise>
        </choose>
    </where>
</select>
```

### 6、动态语句之foreach

> 说明这个之前，我们应该对前面的获取参数的几种形式再进行说明下，前面讲了单个参数以及map,但是并么有说明当遇到 `List/Array`时应该如何访问，这里说明下，访问的参数应该是  `list/array`

先简单介绍下：因为用这种形式就涉及到批量删除，查看等形式：我们在数据库中做批量删除或者查询是这样做的：

`SELECT * from user where id=1 or id=3 or id=4`

或者

`select * from user where id in (1,2,3,4)`

下面我们就按照这种方式分别以list或者是array的形式进行批量删除操作如下：

先简单说明下这个标签的属性含义：

> foreach 主要用户循环迭代 
>
> collection: 要迭代的集合 
>
> item: 当前从集合中迭代出的元素 
>
> open: 开始字符 
>
> close:结束字符 
>
> separator: 元素与元素之间的分隔符 
>
> index:
>
> 迭代的是 List 集合: index 表示的当前元素的下标 
>
> 迭代的 Map 集合: index 表示的当前元素的 key

```java
//使用list集合的形式进行删除
void deleteUser(List<Integer> uids);
```

```xml
<!--void deleteUser(List<User> users);-->
<delete id="deleteUser">
    DELETE from user where id in
    <foreach collection="list" open="(" close=")" separator="," item="curr_id">
        #{curr_id}
    </foreach>
</delete>
```

```java
List<Integer> list = new ArrayList<Integer>();
list.add(3);
list.add(4);
list.add(5);
mapper.deleteUser(list);
```

接下来我们换成数组的形式，并且使用注解自己命名键来做一遍：

```java
//使用数组的形式进行删除
void deleteUser(Integer[] ids);
```

当然，这里也可以使用`@Param("")`的方式给要传递的参数进行命名。

```xml
<!--void deleteUser(List<User> users);-->
<delete id="deleteUser">
    DELETE from user where id in
    <foreach collection="array" open="(" close=")" separator="," item="curr_id">
        #{curr_id}
    </foreach>
</delete>
```

数组的访问方式使用的是以array为键，当然，在前面我们通过加注解的形式之后，就可以直接访问数组中的名称就行了。

### 7、批量操作

> 对于使用foreach,我们在此基础上可以实现更多的功能，如：批量查询，批量添加，批量修改，批量删除（上面已经说过，不再复述）

**批量查询**：

```java
//通过用户的id名称做批量查询
public List<User> getBatchUser(List<Integer> ids);
```

```xml
<!-- //通过用户的id名称做批量查询
public List<User> getBatchUser(List<Integer> ids);-->
<select id="getBatchUser" resultType="com.zxs.bean.User">
    select id,name,sex,age FROM user where id in
    <foreach collection="list" item="currId" separator="," open="(" close=")">
        #{currId}
    </foreach>
</select>
```

**批量添加**：

```java
//对用户进行批量添加
public Integer insertUserBatch(List<User> users);
```

```xml
<!-- //对用户进行批量添加
public Integer insertUserBatch(List<User> users);
添加一般没有返回值，返回的一般是添加成功的行数，或者是是否添加成功的bool值
这里特别注意的是，添加之后的操作，是会多一个逗号出来的，所以我在外层加入了一个trim标签来隔断这个逗号
-->
<insert id="insertUserBatch" >
    INSERT into user(id,name,age,sex) VALUES
    <trim suffixOverrides=",">
        <foreach collection="list" item="currUser" close="">
            (null,#{currUser.name},#{currUser.age},#{currUser.sex}),
        </foreach>
    </trim>

</insert>
```

> 同时，这里我们应该注意的是，在参数传值，我们使用的是 `对象.属性名` 的方式，因此，这里并不存在java中具有的属性私有的表示

**批量修改**：

在数据库sql语句中，并不存在于多行修改的结构性语句，一般都是

`update  table set  id=? ,name=?, age=? where id=?` 因此这里我们就会直接将整个语句使用foreach进行循环使用

```java
//对用户进行批量的修改
public Integer updateUserByBatch(List<User> users);
```

```xml
<!-- //对用户进行批量的修改
public Integer updateUserByBatch(List<User> users);-->
<update id="updateUserByBatch" >
    <foreach collection="list" item="currUser">
        update USER set name=#{currUser.name},age=#{currUser.age},sex=#{currUser.sex} where id=#{currUser.id};
    </foreach>
</update>
```

> 值得注意的是，数据库对这样的操作是不认可的，要想认可这样的操作，还需要对数据库的连接进行设置如下：
>
> jdbc:mysql://localhost:3306/test?allowMultiQueries=true即设置属性：allowMultiQueries=true就可以了。

### 8、动态语句之sql

这个比较简单，就是使用一段公共的sql语言，

![image-20200921125012590](one\image-20200921125012590.png)

> 通过定义公共sql.然后进行引用就可以实现具体效果了。

## 七、mybatis缓存

### 1、一级缓存

mybatis中，一级缓存是默认开启的，同时，一级缓存的作用范围也是Sqlsession级别的。

在使用之前，我们要先在mybatis的核心配置文件中的settings标签中加入一个配置：

```xml
<setting name="logImpl" value="STDOUT_LOGGING" />
```

方便在控制台中打印日志

来看个例子：

```java
SqlSession sqlSession = sqlSessionFactory.openSession(true);
//通过代理模式，获取代理类
DeptMapper mapper = sqlSession.getMapper(DeptMapper.class);
System.out.println(mapper.getDeptById(1));
//sqlSession.clearCache();
System.out.println("--------------------");
System.out.println(mapper.getDeptById(1));
```

注意的是，这里我们进行了两次查询同一个信息：

![image-20200922182059190](one\image-20200922182059190.png)

可以看到，第二次查询的时候，我们是直接在缓存中进行获取的。那我们前面讲了一级缓存是针对sqlsession级别的，也就是对sqlsession具有作用，那下面看看什么时候有效，什么时候没有效果呢？

1) 不同的 SqlSession 对应不同的一级缓存 （构造了两个sqlsession）

2) 同一个 SqlSession 但是查询条件不同 (执行的查询语句不同)

3) 同一个 SqlSession 两次查询期间执行了任何一次增删改操作  （改变了可能的数据）

4) 同一个 SqlSession 两次查询期间手动清空了缓存（执行了代码sqlSession.clearCache()）

### 2、二级缓存

前面讲了sqlsession是针对sqlsession级别的，二级缓存针对的是mapper级别的，也就是我们设计mapper.xml映射文件时的`namespace`,并且需要注意的是，二级缓存是不会默认开启的，是需要我们手动开启的。

设置步骤：

* 相关的pojo实现接口：

* 核心配置文件中配置：<setting name="cacheEnabled" value="true"/>

* mapper映射文件中配置：

  ```xml
  <!--开启二级缓存的相关支持 中的属性暂时不做说明，
      属性如：eviction可以参考尚硅谷的相关文档
  -->
  <cache eviction="FiFO"/>
  ```

具体实现过程：

```java
//设置成true时表示事物自动进行提交
SqlSession sqlSession = sqlSessionFactory.openSession();
DeptMapper mapper = sqlSession.getMapper(DeptMapper.class);
System.out.println(mapper.getDeptById(1));
sqlSession.commit();


System.out.println("--------------------");
SqlSession sqlSession1 = sqlSessionFactory.openSession(true);
DeptMapper mapper1 = sqlSession1.getMapper(DeptMapper.class);
System.out.println(mapper1.getDeptById(1));
```

> 值得注意的是：在我们第一次执行后要手动提交，这里就不能设置为自动提交，否则，程序会认为是在整个方法执行完之后再自动提交，就不能得到我们要的效果了。

### 3、第三方缓存

第三方技术随着工程的不同会有不同的操作，如EhCache,Redis等不同的缓存技术。

## 八、逆向工程



