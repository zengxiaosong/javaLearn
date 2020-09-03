**1.1 JdbcTemplate基本概念**：

> -  JDBC已经能够满足大部分用户最基本的需求，但是在使用JDBC时，必须自己来管理数据库资源如：获取PreparedStatement，设置SQL语句参数，关闭连接等步骤。JdbcTemplate就是Spring对JDBC的封装，***\*目的是使\*******\*JDBC\**\**更加易于使用\****。***\*JdbcTemplate\**\**是\**\**Spring\**\**的一部分\****。 JdbcTemplate处理了资源的建立和释放。他帮助我们避免一些常见的错误，比如忘了总要关闭连接。他运行核心的JDBC工作流，如Statement的建立和执行，而我们只需要提供SQL语句和提取结果。Spring源码地址：https://github.com/spring-projects/spring-framework 在JdbcTemplate中***\*执行\*******\*SQL\*******\*语句的方法\****大致分为3类：
>
> 1. execute：没有返回值，可以执行所有的SQL语句，一般用于执行DDL语句。
> 2. update：返回一个int值，影响的行数，用于执行INSERT、UPDATE、DELETE等DML语句。
> 3. queryXxx:用于DQL数据查询语句。

**1.2 学习jdbcTemplate的好处**：

> 1.  不需要我们管理连接了。
> 2. 不需要我们设置参数了。
> 3. 查询时候可以直接返回对应的实体类。

**1.3jdbcTemplate的使用步骤**：

> 1.  导入jdbctemplate的jar。
>
> 2. 创建jdbcTemplate对象。
>
>    ```html
>     JdbcTemplate jdbcTemplate = new JdbcTemplate(JDBCUtils.getDataSource())
>    ```

------

![](one\20180828092435261.png)

------

**2.1 JDBCTemplate实现创建表（execute）**：

如果执行DDL语句，建议大家使用execute方法。

```java
package jdbctemplate;
import Utils.JDBCUtils;
import org.junit.Test;
import org.springframework.jdbc.core.JdbcTemplate;
public class Demo {

    //获取jdbcTemplate核心类
    private JdbcTemplate  jdbcTemplate = new JdbcTemplate(JDBCUtils.getDataSource());
    @Test
    public void createTable(){
        String sql = "create table person(id int primary key auto_increment,name varchar(10),age int)";
        jdbcTemplate.execute(sql);
    }
}
```

**2.2 JDBCTemplate实现增删改**：

增删改都只是使用到了一个方法 update(sql,Object...args)

```java
/*
        增加
     */
    @Test
    public void add(){
        String sql = "insert into person(id,name,age)values(?,?,?)";
        jdbcTemplate.update(sql,1,"李建",18);
        jdbcTemplate.update(sql,2,"那英",19);
    }
    /*
        更新
     */

    @Test
    public  void update(){
        String sql = "update  person set age = ? where name =?";
        jdbcTemplate.update(sql,40,"李建");
    }
    /*
        删除
     */
    @Test
    public void delete(){
        String sql = "delete from person where id = ?";
        jdbcTemplate.update(sql,1);
    }
```

**2.3 jdbctemplate实现查询**

**2.3.1 queryForObject查询单个字段**：

> 1. queryForObject(sql,class) 参数说明：SQL执行的SQL语句，     class：查询结果的类型
> 2. queryForObject(sql,class,param) 参数说明：SQL执行的SQL语句    class：查询结果的类型，param就是SQL需要的参数。

```java
package jdbctemplate;

import Utils.JDBCUtils;
import org.junit.Test;
import org.springframework.jdbc.core.JdbcTemplate;
public class Demo02 {
    private JdbcTemplate jdbcTemplate = new JdbcTemplate(JDBCUtils.getDataSource());
    @Test
    public void queryCount(){
      String sql = "select id from person where name=?";
      int num = jdbcTemplate.queryForObject(sql,int.class,"张国荣");
      System.out.println(num);
    }
}
```

 **2.3.2 queryForMap查询单个对象**： 

> 1.  queryForMap查询单个对象的数据。 queryForMap 会把一行的数据存储到Map对象中。
> 2. 一个Map对象就存储一条记录， key存储的是列名， value是存储该行数据的值。

```java
 private JdbcTemplate jdbcTemplate = new JdbcTemplate(JDBCUtils.getDataSource());
@Test
    public void queryTest(){
        String sql = "select*from person where id =?";
        Map<String, Object> result = jdbcTemplate.queryForMap(sql, 3);
        System.out.println(result);//{id=3, name=李建, age=17}
    }
```

 **2.3.3 queryForObject查询单个实体对象**：

> 1. 使用queryForObject方法，实现查询返回单个实体类对象。
> 2. 结果映射器： BeanPropertyRowMapper。 

```java
/*
        queryForObject查询单个实体对象
        使用queryForObject方法，查询返回单个实体类对象。
        结果映射器：BeanPropertyRowMapper
     */
 private JdbcTemplate jdbcTemplate = new JdbcTemplate(JDBCUtils.getDataSource());
    @Test
    public void queryTest01(){
        String sql = "select*from person where id = ?";
       Student student = jdbcTemplate.queryForObject(sql,new BeanPropertyRowMapper<>(Student.class),4);
        System.out.println("查询的结果是："+student);//查询的结果是：Student{id=4, name='陈奕迅', age=34}
    }
```

 **2.3.4 queryForList查询多个对象**：*（不常用）*

```javascript
private JdbcTemplate jdbcTemplate = new JdbcTemplate(JDBCUtils.getDataSource());
   /*queryForList查询多个对象，使用queryForList方法，实现查询返回多个对象，List<Map>*/
    @Test
    public void querytest02(){
        String sql = "select*from person";
        List<Map<String, Object>> list = jdbcTemplate.queryForList(sql);
        //查询多条数据，返回一个List，List中存储了Map，一个Map就是一行数据
        System.out.println(list);
        //[{id=2, name=那英, age=19}, {id=3, name=李建, age=17}, {id=4, name=陈奕迅, age=34}]
    }
```

 **2.3.5 query查询多个对象，并且要求使用rowMapper**  （这种形式可以使得数据库字段可以不与bean字段中的属性名一致）

- **自定义RowMapper实现方式（\**多表查询\**）**：

```java
 /*query查询多个对象，并且要求使用rowMapper
    * 使用query方法实现一次性查询多个对象，返回的是一个List，并且List对象存储的是实体类对象
    * 自定义rowMapper对象没实现数据的封装
    * */
private JdbcTemplate jdbcTemplate = new JdbcTemplate(JDBCUtils.getDataSource());
    @Test
    public void querytest03(){
        String sql ="select*from person";
        List<Student> list = jdbcTemplate.query(sql, new RowMapper<Student>() {
            /*
                mapRow(ResultSet resultSet, int i)
                resultSet：jdbctemplate每得到一行数据，都会直接给了resultSet对象
                i:当前是第几行
             */
            @Overr
            public Student mapRow(ResultSet resultSet, int i) throws SQLException {
                //每一行数据其实就是一个Student对象
                Student s = new Student();
                //把查询的数据封装到Student对象中
                s.setId(resultSet.getInt("id"));
                s.setName(resultSet.getString("name"));
                s.setAge(resultSet.getInt("age"
                return s;                             
            }
        });
        System.out.println("查询的学生："+list);
        //查询的学生：[Student{id=2, name='那英', age=19}, Student{id=3, name='李建', age=17}, Student{id=4, name='陈奕迅', age=34}]
    }
```

**2.3.6 使用BeanPropertyRowMapper实现方式**：

```java
/*
    使用BeanPropertyRowMapper实现方式
    使用BeanPropertiesRowMapper对象， 实现数据的封装。
     */
  private JdbcTemplate jdbcTemplate = new JdbcTemplate(JDBCUtils.getDataSource());
    @Test
    public void queryTest04(){
        String sql = "select*from person";
        List<Student> list = jdbcTemplate.query(sql, new BeanPropertyRowMapper<>(Student.class));
        System.out.println("查询的学生："+list);
    }
```

------

- **查询总结**:

| **方法名**                                                | **作用**                                                   |
| --------------------------------------------------------- | ---------------------------------------------------------- |
| queryForObject(sql,数据类型.class)                        | 查询单个字段                                               |
| queryForMap(sql,参数)                                     | 查询单个对象，返回一个Map对象                              |
| queryForObject(sql, new  BeanPropertyRowMapper(类型),参数 | 查询单个对象，返回单个实体类对象                           |
| queryForList(sql,参数)                                    | 查询多个对象，返回一个List对象，List对象存储是Map对象      |
| query(sql,new BeanPropertyRowMapper(),参数)               | 查询多个对象，返回的是一个List对象，List对象存储的是实体类 |

 