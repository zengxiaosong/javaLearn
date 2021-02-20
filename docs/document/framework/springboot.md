# springboot学习路线

> springboot其实来讲，就是实现微服务的技术实现，而springcloud实质上是对众多微服务的一个管理。相比于ssm,springboot省略了很多的配置，能够非常智能的帮助我们做项目。注意。多看官方文档。

## 一、写在前面

### 1、springboot打包方式

> 在主模块下对子模块进行打包的命令： mvn -f  子模块名   clean package    运行jar包的方式： java -jar，或者使用界面化的maven进行打包

### 2、微服务

> 起源于2014年的martin fowler , 微服务应该是一种架构类型，一个应用应该是一组小型的服务，可以通过http方式进行互通。
>
> 对应的前面还有传统应用的**单体应用**，改一点东西，就会涉及到重新部署，将不同的功能进行动态组合，以此达到不同的效果。让每个小组合都是可独立替换的，可独立升级的软件单元。

## 二、环境搭建

### 1、设置maven

设置编译时的配置：

```xml
<profile>
    <id>jdk-1.8</id>
    <activation>
        <activeByDefault>true</activeByDefault>
        <jdk>1.8</jdk>
    </activation>
    <properties>
        <maven.compiler.source>1.8</maven.compiler.source>
        <maven.compiler.target>1.8</maven.compiler.target>
        <maven.compiler.compilerVersion>1.8</maven.compiler.compilerVersion>
    </properties>
</profile>
```

> 其他的就是jdk1.8,以及idea的配置环境。

## 三、简单的helloWorld

### 1、简单搭建使用

使用maven，搭建项目，并创建包，写上对应启动类和对应的**Controller**

![image-20201109194334955](one\image-20201109194334955.png)

controller源码：

```java
package com.zxs.controller;

import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class TestController {

    @RequestMapping("hello")
    public String hello(){
        return "hello Spring boot";
    }
}
```

Springboot启动类源码：

```java
package com.zxs;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class FirstApplication {
    public static void main(String[] args) {
        SpringApplication.run(FirstApplication.class,args);
    }
}
```

### 2、关于pom的说明

```xml
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>2.3.5.RELEASE</version>
</parent>
```

> 这个是springboot的父类，负责管理依赖，也就是说我们引入的依赖是不需要版本号的，springboot的parent就直接管理了。

```xml
<!--    web程序的依赖配置-->
    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
    </dependencies>
```

> 这个是我们web开发的基础依赖启动器，里面就包含了web开发大部分的依赖和jar包。

```xml
<!--    springboot程序打包的插件-->
    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
        </plugins>
    </build>
```

> 这是一个打包的插件，通过这个插件，我们可以显示的或者通过命令就能将springboot打包成jar包进行运行。

### 3、@SpringbootApplication 

> springboot相对于SSM框架来讲，基本上是没有配置的，那么，这个是如何实现的呢？我们从启动类的源码看起走。首先点开@SpringbootApplication源码的解释：

```java
@Target({ElementType.TYPE})
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Inherited
//主要关心下面的两个注解配置：
@SpringBootConfiguration
@EnableAutoConfiguration
@ComponentScan(
    excludeFilters = {@Filter(
    type = FilterType.CUSTOM,
    classes = {TypeExcludeFilter.class}
), @Filter(
    type = FilterType.CUSTOM,
    classes = {AutoConfigurationExcludeFilter.class}
)}
)
```

先来看看@SpringBootConfiguration： 顾名思义，就是配置类，再打开看看：

```java
Target({ElementType.TYPE})
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Configuration
public @interface SpringBootConfiguration {
    @AliasFor(
        annotation = Configuration.class
    )
    boolean proxyBeanMethods() default true;
}
```

可以看出内部就是spring中的@Configuration，也就说明，springboot是将这个类作为一个配置类，就类似于配置文件一样，那么，既然进行了配置，那么，比如我们的组件扫描这些是配置到哪里的呢？看下一个注解。

@EnableAutoConfiguration：自动配置，点开后：

```java
@Target({ElementType.TYPE})
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Inherited
//主要关注下面两个注解
@AutoConfigurationPackage
@Import({AutoConfigurationImportSelector.class})
```

点开后我们继续关注下@AutoConfigurationPackage,也就是自定配置包，换句话说就是我们的配置类要去扫描的包和类有哪些：

```java
@Target({ElementType.TYPE})
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Inherited
@Import({Registrar.class})
```

点开后我们主要关注：@Import({Registrar.class})，将Registrar.class点开后我们主要关注：

```java
public void registerBeanDefinitions(AnnotationMetadata metadata, BeanDefinitionRegistry registry) {
    AutoConfigurationPackages.register(registry, (String[])(new AutoConfigurationPackages.PackageImports(metadata)).getPackageNames().toArray(new String[0]));
}
```

通过调试，我们发现：getPackageNames()最终获得的是我们的配置类所在的包，因此，要想我们自己定义的类被扫描，那么就应该在此包下或者子包下。

下面来看第二个注解：

@Import({AutoConfigurationImportSelector.class})：自动配置选择器，怎么理解呢，也就是你要配置的哪些东西，比如AOP要进行的配置，MVC要进行的配置，通过点开类，我们主要看下:

```java
protected List<String> getCandidateConfigurations(AnnotationMetadata metadata, AnnotationAttributes attributes) {
    List<String> configurations = SpringFactoriesLoader.loadFactoryNames(this.getSpringFactoriesLoaderFactoryClass(), this.getBeanClassLoader());
    Assert.notEmpty(configurations, "No auto configuration classes found in META-INF/spring.factories. If you are using a custom packaging, make sure that file is correct.");
    return configurations;
}
```

通过调试，我们就大概知道要扫描哪些包了，现在我们打开扫描的包看下：

![image-20201109200005971](one\image-20201109200005971.png)

![image-20201109200059380](one\image-20201109200059380.png)

点开其中的部分，我们看看结果：

![image-20201109200325623](one\image-20201109200325623.png)

从上面这些都说明，都是通过官方的封装并进行自动的配置。因此，我们自己是不需要去做更多的配置的。

### 4、Springboot快速启动

> 例如idea,使用Spring initializr进行快速搭建,因为是从spring.io的官网上进行下载，所以要在有网络的环境下进行搭建。

![image-20201109202235225](one\image-20201109202235225.png)

通过查看文件目录，我们主要解释下几个：

* static: 主要用来存放静态资源文件的，如js, css之类的。
* templates: 主要用来存放模板页面信息的，后面支持的主要有如Freemarker, thymleaf。
* application.properties : 主要用来存我们自己定义的配置信息，如server.port等信息。

## 四、springboot的配置

### 1、yaml文件的配置

首先明确，在springboot中，我们的配置文件应该放在哪里，在spring-boot-starter-parent中就有做说明了。

![image-20201109213625508](one\image-20201109213625508.png)

现在来说说yaml文件的语法，yaml的配置文件不同于.properties/xml文件，而是像语法树一样，更注重的是数据内容本身，相对来讲，格式就更加简便。

**现在来说说yaml文件的语法：**

> k: v:  更加具体化的是 **k:(空格)v**,当然，yaml文件针对不同的数据类型，有不同的表达方式。

* 字面量的形式，如普通字符串

  ```yaml
  name: javaZeng
  ```

  一般来讲，字符串默认是不用加双引号或者是单引号的，但是加了会产生什么效果呢？

  * 加双引号，会对特殊字符进行转义 如："hello \n world"  ==> "hello(换行)world"。

  * 加单引号，不会对特殊字符进行转义如：'hello\n world' ==> 'hello\n world'。

    

* map,set的形式：

  * 一般形式：

    ```yaml
    user: 
      name:javaZeng
      age:25
    ```

  * 行内形式：

    ```yaml
    user: {name: javaZeng,age: 25}
    ```

* 数组的表现形式
  * 一般形式：

    ```yaml
    dog: 
      - tom
      - tink
      - mary
    ```

  * 行内形式：

    ```yaml
    dog: [tom,tink,mary]
    ```

**在springboot中如何将yaml文件中配置信息植入对象中呢？**

先构建类如下：

User类：

```java
/**
 * @Component:将该类作为bean,使得容器能够扫描。
 * @ConfigurationProperties(prefix = "person")将该类与配置文件中的类相对应，使得信息能够匹配
 */
@Component
@ConfigurationProperties(prefix = "person")
public class User {
    private String name;
    private Integer age;
    private Date birth;
    private Map<String,Object> map;
    private List<Object> list;
    private Dog dog;
```

dog类：

```java
public class Dog {
    private String name;
    private Integer age;
```

> 关于setter/getter/toString方法就省略了。

引入依赖：（否则此配置注解不能被扫描）

```xml
<!--        配置文件处理器-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-configuration-processor</artifactId>
            <optional>true</optional>
        </dependency>
    </dependencies>
```

yaml文件中的内容：

```yaml
person:
  name: 曾哥
  age: 25
  birth: 2019/12/22
  map: {k1: 25,k2: java}
  list:
    - 12
    - java
    - main
  dog:
    name: hello
    age: 2
```

> 测试结果省略了。值得注意的是，yaml文件并不存在配置文件中文编码的问题。

### 2、properties文件配置

```properties
# 设置端口号
server.port=8081
person.name=曾哥
person.birth=2020/12/11
person.age=25
person.map.k1=11
person.map.k2=hello
person.dog.name=peter
person.dog.age=11
person.list=25,11,44
```

> 如果出现中文乱码，请到设置中调节编码格式。

### 3、使用@Value注解配置

**引入一个JSR303的校验规则依赖：**

```xml
<!--        JSR303 校验-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-validation</artifactId>
        </dependency>
```

```java

/**
 * @Value(value = "${person.name}")
 * 支持使用${}映射文件
 * 支持使用#{}来支持spring的spEL表达式
 * 不能支持复杂的复合值，如列表，集合，map等
 * 不能支持JSR303的校验规则
 */
@Component
@Validated
//@ConfigurationProperties(prefix = "person")
public class User {

    @Value(value = "${person.name}")
    @Email
    private String name;
    @Value(value = "#{20/10}")
    private Integer age;
    private Date birth;
```

### 4、两种方式比较

|                  |                          |                              |
| ---------------- | :----------------------: | :--------------------------: |
|                  | @ConfigurationProperties |            @Value            |
| 功能             |   批量注入文件中的属性   | 在属性上或者方法上依次注入值 |
| 松散绑定         |           yes            |              no              |
| SPEL             |            no            |             yes              |
| JSR303数据校验   |           yes            |              no              |
| 复杂类型数据封装 |           yes            |              no              |

如果使用前者，导致校验失败，就会出现

```java
java.lang.IllegalStateException: Failed to load ApplicationContext
```

> 那么，如果我们需要使用配置文件中的信息，到底使用@Configuration还是使用@Value呢？说明的是，如果是对整个javabean进行配置信息的获取，我们尽量使用前者，如果是单个配置信息的获取，我们可以使用后者。

### 5、@PropertySource

> 加载指定的配置文件，对于前者，都是使用默认的全局配置文件，但是我们在做实际项目开发过程中，是不能将所有的配置放入到一个配置文件中的，因此，使用我么自定义的配置文件。对上面的代码做改进：

```java
@Component
@Validated
//加载指定的配置文件
@PropertySource(value = "classpath:person.yml")
@ConfigurationProperties(prefix = "person")
public class User {

    //@Value(value = "${person.name}")
    @Email
    private String name;
    //@Value(value = "#{20/10}")
    private Integer age;
    private Date birth;
```

### 6、@ImportResource

> 即导入一个配置文件（这里指的是spring的配置文件，不是上面的属性配置），让配置文件在spring容器中能够生效。因为即使是我们自己写的配置文件，springboot也是不会去做扫描的，因此，需要使用这个注解放到一个配置类上面，才能够生效。

```java
//添加配置文件的信息
@ImportResource(locations = {"classpath:beans.xml"})
@SpringBootApplication
public class SpringquickApplication {

    public static void main(String[] args) {
        SpringApplication.run(SpringquickApplication.class, args);
    }
```

### 7、springboot推荐组件配置

```java
/** 以前spring使用的更多的是配置文件，
 * 现在springboot中推崇使用全注解的方式代替配置文件。
 * 要注入一个组件，对应<bean></bean>的是注解@Bean
 * @Configuration: 由于内部还是表示一个组件@Component
 * 因此还是要被主配置类扫描
 */
@Configuration
public class myApplicationConfig {

    @Bean
    public HelloService helloService(){
        System.out.println("helloService 被执行");
        return new HelloService();
    }
}
```

### 8、配置文件的占位符使用

```java
# 设置端口号
server.port=8081
# 配置文件可以使用随机数
person.name=曾哥${random.uuid}
person.birth=2020/12/11
person.age=${random.int}
person.map.k1=11
person.map.k2=hello
person.mydog=zeng
# 也可以使用其他配置信息的值,如果引用的没有值可以加 : 设置默认值
person.dog.name=${person.mydog:1123}peter
person.dog.age=11
person.list=25,11,44
```

### 9、profiler的使用

> 在实际项目开发的过程中，我们可能会使用不同的配置文件，比如说开发环境，测试环境，生产环境等不同的环境

**对于properties配置文件**：命名规则是：application-{profile}.properties/yaml,默认情况下使用的是**application.properties**

![image-20201110163139059](one\image-20201110163139059.png)

```properties
# 设置端口号
server.port=8080
#默认使用该配置文件，但是在使用了下面的参数后，就会到相应的配置文件中进行执行
spring.profiles.active=dev
```

对于yaml文件，可以使用文档块

```yaml
server:
  port: 8080
#  设置运行时的环境
spring:
  profiles:
    active: prod
---

server:
  port: 8083
#  设置环境
spring:
  profiles:dev

---
server:
  port: 8084
spring:
  profiles:prod
```

当然，即时是我们设置的spring:profiles:active: prod 也是可以更改的，如通过命令：

* --spring.profiles.active=prod进行指定
* -Dspring.profiles.active=dev 这样的虚拟机参数进行指定

### 10、配置文件的加载顺序

> 先来看项目文件中配置文件的加载顺序与优先级,springboot在启动时会默认加载项目文件中的application.properties/application.yaml文件作为默认配置文件。加载顺序为：

* file:./config/ 目录下
* file:./目录下
* classpath:/config/ 目录下
* classpath:/ 目录下

> 以上配置文件的加载顺序按照高优先级到低优先级全部加载，不过，在相同配置条件下，高优先级会覆盖低优先级的配置文件。
>
> 当然，也可以通过命令行--spring.config.location=location来加载配置文件。

如果有多个配置文件，并且在堆项目中打了jar包的情况下（上面中file中的配置文件不会被打到jar包中），配置文件的优先级为：

![image-20201110190808317](E:\myStudy\docisfy\javaLearn\docs\document\framework\one\image-20201110190808317.png)

**1.命令行参数**

> 所有的配置都可以在命令行上进行指定
>
> java -jar spring-boot-02-config-02-0.0.1-SNAPSHOT.jar --server.port=8087 --server.context-path=/abc

2.来自java:comp/env的JNDI属性

3.Java系统属性（System.getProperties()）

4.操作系统环境变量

5.RandomValuePropertySource配置的random.*属性值

==**由jar包外向jar包内进行寻找；**==**（\*.properties>\*.yml）**

==**优先加载带profile**==

**6.jar包外部的application-{profile}.properties或application.yml(带spring.profile)配置文件**

**7.jar包内部的application-{profile}.properties或application.yml(带spring.profile)配置文件**

**8.--spring.config.location=C:/application.properties（它在这里）**

==**再来加载不带profile**==

**9.jar包外部的application.properties或application.yml(不带spring.profile)配置文件**

**10.jar包内部的application.properties或application.yml(不带spring.profile)配置文件**

11.@Configuration注解类上的@PropertySource

12.通过SpringApplication.setDefaultProperties指定的默认属性

> 先了解到有这么多，等实际运用再更加明确

### 11、springboot的配置原理

> 这里不明说，要了解，看视频尚硅谷雷丰阳18-19节的springboot的相关内容。

## 五、springboot与日志

> 简要说明，现在的日志框架都主要是面向抽象层进行开发。市面上常用的日志框架有：JUL,JCL,Jboss-logging,logback,log4j,log4j2,slf4j....

![image-20201110204304657](E:\myStudy\docisfy\javaLearn\docs\document\framework\one\image-20201110204304657.png)

> JCL基本不更新了，因此不进行选择。jboss-logging选用的框架实现太少，基本不用，logback是在log4j上提升出来的，log4j2是apache公司写的更加全面的日志框架。

### 1、如何在系统中使用slf4j

> 在开发的时候，日志记录方法的调用。不应该直接调用日志实现类，而是调用抽象类，比如上面所说的slf4j/logback,就是如此，给系统导入这两类的jar包。
>
> **同时，还应该注意的是，在使用日志框架的时候，是要导入配置文件的，在使用slf4j后，我们使用的配置文件应当是日志框架实现类的配置文件。**

具体的细节部分，看官方文档的slf4j。

### 2、系统如何统一日志

> 针对不同的框架系统，都有该框架本身实现的日志系统，但是，我们如何统一呢，如果不导入该框架实现日志的包，系统又不能正常运行。

统一日志系统规范：

* 将系统中其他日志框架先排除出去。
* 用中间包来替换原有的日志框架。
* 我们导入slf4j其他的实现。

### 3、springboot的日志系统

![image-20201111200911632](one\image-20201111200911632.png)

我们从源码和结构上来看看是怎么做的替换包吧：

> 这里由于版本的变化，结构替换包和spring底层将commons-logging 包进行排除，2.0版本后没有怎么出现，所以这里的了解需要后面再看

#### 遗留问题待解决

### 4、日志的使用

> springboot官方已经做好了默认的日志的配置，我们只需要使用就行了

```java
 //高版本已经不能写在域信息中了
        Logger logger = LoggerFactory.getLogger(getClass());
//        从上到下依次由低到高级别的优先级，
//        springboot默认是info级别的优先级，可以通过配置文件进行优先级别的显示进行调整
        logger.trace("this is trace info");
        logger.debug("this is debug info");
        logger.info("this is info info");
        logger.warn("this is warning info");
        logger.error("this is error info");
```

当然，也可以通过配置文件进行默认的配置：

```properties
# 可以通过这种方式手动配置日志级别
logging.level.com.zxs.springquick=debug
#配置日志输出的位置,path指定的是项目所在的根目录下的springboot目录下
# 生成的文件名是spring.log
logging.file.path=/springboot/
# 指定文件名，如果不指定文件目录，就生成在项目根目录下
# 该配置已经过时了
#logging.file=mylogging.log

# 控制台下的日志输出模板
logging.pattern.console=
# 文件里的日志输出摸板
logging.pattern.file=
```

### 5、自定义配置

> 在对日志框架的配置过程中，我们也可以进行如logback的日志框架进行自定义配置，具体如何配置见官方文档

### 6、切换日志框架

> 对于切换日志框架，springboot中，我们首先要将不必要的包进行排除，同时，加上相应的适配包（相关的适配包在slf4j的官方文档中有说明），不做过多的介绍了。

## 六、springboot—web开发知识

### 1、开发流程

> 使用idea导入相应的模块，做相应模块的配置，然后操作业务逻辑

### 2、访问三方静态资源

```java
@Override
public void addResourceHandlers(ResourceHandlerRegistry registry) {
   if (!this.resourceProperties.isAddMappings()) {
      logger.debug("Default resource handling disabled");
      return;
   }
   Duration cachePeriod = this.resourceProperties.getCache().getPeriod();
   CacheControl cacheControl = this.resourceProperties.getCache().getCachecontrol().toHttpCacheControl();
   if (!registry.hasMappingForPattern("/webjars/**")) {
      customizeResourceHandlerRegistration(registry.addResourceHandler("/webjars/**")
            .addResourceLocations("classpath:/META-INF/resources/webjars/")
            .setCachePeriod(getSeconds(cachePeriod)).setCacheControl(cacheControl));
   }
   String staticPathPattern = this.mvcProperties.getStaticPathPattern();
   if (!registry.hasMappingForPattern(staticPathPattern)) {
      customizeResourceHandlerRegistration(registry.addResourceHandler(staticPathPattern)
            .addResourceLocations(getResourceLocations(this.resourceProperties.getStaticLocations()))
            .setCachePeriod(getSeconds(cachePeriod)).setCacheControl(cacheControl));
   }
}
```

> 从上面的WebMvcConfiguration类（都在自动配置包中）下可以看出，我们要使用第三方静态资源包的话，应该是要从/webjars/目录下访问。 具体怎么引入相关依赖，**访问webjars的官方网站即可。**

下面给个例子：

首先到webjars的官网中去依赖相关需要的包，并添加到项目的pom.xml文件中

![image-20201112144551736](one\image-20201112144551736.png)

查看该依赖我们发现：

![image-20201112150107036](one\image-20201112150107036.png)

从上面的代码中，我们发现，要能够访问到该文件，我们需要按照下面的形式进行访问：

> http://localhost:8080/zxs/webjars/jquery/3.0.0/jquery.js

### 3、静态资源的访问（中文乱码）

```java
String staticPathPattern = this.mvcProperties.getStaticPathPattern();
if (!registry.hasMappingForPattern(staticPathPattern)) {
   customizeResourceHandlerRegistration(registry.addResourceHandler(staticPathPattern)
         .addResourceLocations(getResourceLocations(this.resourceProperties.getStaticLocations()))
         .setCachePeriod(getSeconds(cachePeriod)).setCacheControl(cacheControl));
}
```

通过解析源码发现：

```java
String staticPathPattern = this.mvcProperties.getStaticPathPattern();
```

这段代码解析后是表示用户访问   / **  目录下的静态资源，那么如何做匹配呢？下面的代码中就说明了，点开该代码我们继续看源码发现：

```java
this.resourceProperties.getStaticLocations()
```

```java
private static final String[] CLASSPATH_RESOURCE_LOCATIONS = { "classpath:/META-INF/resources/",
      "classpath:/resources/", "classpath:/static/", "classpath:/public/" };
```

最终要的就是这几个，我们通过根项目如找寻 hello.txt这样的静态文件，就要到相应的文件中去找就行了。客户端就能访问到该位置了。这里注意的是，**classpath类路径可以是我们的java路径，也可以是我们的resources路径。**

```properties
server.servlet.encoding.force=true
```

> 如果遇到中文乱码，生成一个编码过滤器就可以解决。

### 4、欢迎页的配置

```java
@Bean
public WelcomePageHandlerMapping welcomePageHandlerMapping(ApplicationContext applicationContext,
      FormattingConversionService mvcConversionService, ResourceUrlProvider mvcResourceUrlProvider) {
   WelcomePageHandlerMapping welcomePageHandlerMapping = new WelcomePageHandlerMapping(
         new TemplateAvailabilityProviders(applicationContext), applicationContext, getWelcomePage(),
       //匹配的标准还是刚才的  "/**"
         this.mvcProperties.getStaticPathPattern());
   welcomePageHandlerMapping.setInterceptors(getInterceptors(mvcConversionService, mvcResourceUrlProvider));
   welcomePageHandlerMapping.setCorsConfigurations(getCorsConfigurations());
   return welcomePageHandlerMapping;
}

private Optional<Resource> getWelcomePage() {
    //路径的映射还是从刚才的静态文件中去找
   String[] locations = getResourceLocations(this.resourceProperties.getStaticLocations());
   return Arrays.stream(locations).map(this::getIndexHtml).filter(this::isReadable).findFirst();
}

private Resource getIndexHtml(String location) {
    //欢迎页的设置应该是index.html
   return this.resourceLoader.getResource(location + "index.html");
}
```

> 比如访问路径为:   localhost:8080/index.html

### 5、网站图标的设置

> 官网的解释

![image-20201112162956077](one\image-20201112162956077.png)

> 我这里使用的是2.3.5的版本，1.X的版本有不同的源码和演示，springboot2.0后，就将springboot官方默认的favicon.ico去掉了，现在如果要使用的话，依然是将   .ico文件放在静态资源目录下，然后在页面上加入如下代码：
>
> （注意的是，这里对文件名没有做要求了，1.X版本对文件名做了要求，必须是favicon.ico）

```html
<link rel="icon" href="ip.ico" >
```

### 6、自定义静态资源路径

![image-20201112193144068](one\image-20201112193144068.png)

定位到我们源码中的静态路径下，这是一个配置信息的类，通过这个类配置的信息的前缀，我们到配置文件中进行配置如下：

```properties
spring.resources.static-locations=classpath:/根路径下的文件，可以进行自定义
```

### 7、tymeleaf模板引擎

![image-20201112202149359](E:\myStudy\docisfy\javaLearn\docs\document\framework\one\image-20201112202149359.png)

> 根据不同的官方版本使用不同的模板引擎。

### 8、thymeleaf的使用原理

> 同前面，我们来查看thymeleaf的自动配置是怎么样的,通过源码，就能大致明白是如何解析的，以后怎么使用：

![image-20201112203955328](one\image-20201112203955328.png)

> 从上面的信息来看，我们就大致明白了应该将静态的html放在哪里，同时，应该怎么样做配置。

现在，通过thymeleaf官方文档和一些小的例子来看看，应该如何使用thymeleaf这个模板引擎吧。

首先，引入相关的依赖包：

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-thymeleaf</artifactId>
    //不用引入相关版本，springboot自己就已经配置好了
</dependency>
```

构建一个基本的页面：加上相关的代码，这样后面在写thymeleaf的代码时才会有相关的提示信息。

```html
<html lang="en" xmlns:th="http://www.thymeleaf.org">
```

如果使用了上面的代码没有相关的提示信息，查看plugin中是否引入了Thymeleaf。使用案例如下：

```html
<!DOCTYPE html>
<html lang="en" xmlns:th="http://www.thymeleaf.org">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
    这是一个关于thymeleaf相关的页面信息
    <p th:text="${hello}"></p>
</body>
</html>
```

```java
@RequestMapping("/first")
public String first(Map map){
    map.put("hello","今天天气真好");
    return "first";
}
```

> 上面的mapping 中进行的配置和springMVC是差不多的。

### 9、thymeleaf的语法

> 在实际运用中或者后面的了解学习中逐步了解。

## 七、springboot-crud



## 八、springboot注解解析

> 这里先就注解的使用等进行说明，再对原理以及源码等层次的东西进行理解。

### @Configuration

> @Configuration用于定义配置类，可替换xml配置文件，被注解的类内部包含有一个或多个被@Bean注解的方法，这些方法将会被AnnotationConfigApplicationContext或AnnotationConfigWebApplicationContext类进行扫描，并用于构建bean定义，初始化Spring容器。

### @ComponentScan

> 扫描对应存在的组件，也就是扫描包中有@Component注解的组件，将其组件放到bean容器中。

### @Data

> @Data注解属于元注解类型的，主要目的就是为了给一个类做配置。当然，对应的如下面的这些注解也是如此。

* @Data ：注解在类上；提供类所有属性的 getting 和 setting 方法，此外还提供了equals、canEqual、hashCode、toString 方法
* @Setter：注解在属性上；为属性提供 setting 方法
* @Getter：注解在属性上；为属性提供 getting 方法
* @Log4j ：注解在类上；为类提供一个 属性名为log 的 log4j 日志对象
* @NoArgsConstructor：注解在类上；为类提供一个无参的构造方法
* @AllArgsConstructor：注解在类上；为类提供一个全参的构造方法

### @ConditionalOnClass

> 通俗的来说，就是当这个代码标注在类上时，当类路径下存在参数中的类的时候，该被标注的配置类下的所有@bean才能生效。对于里面更加深层次的，也就是此注解中还有@Conditional, 下面做进一步说明：

### @Conditional

> @Conditional注解就是用来判断某个类是否应该被注入到spring容器中，具体是怎么执行的，看该注解的源码发现：传入进来的参数，也就是class数组中的类应该是要是是实现Condition接口的。

```java
@Target({ElementType.TYPE, ElementType.METHOD})
@Retention(RetentionPolicy.RUNTIME)
@Documented
public @interface Conditional {

   /**
    * All {@link Condition Conditions} that must {@linkplain Condition#matches match}
    * in order for the component to be registered.
    */
   Class<? extends Condition>[] value();

}
```

> 下面看看该接口是怎么样的呢？具体要实现哪些方法？

```java
@FunctionalInterface
public interface Condition {

   /**
    * Determine if the condition matches.
    * @param context the condition context
    * @param metadata metadata of the {@link org.springframework.core.type.AnnotationMetadata class}
    * or {@link org.springframework.core.type.MethodMetadata method} being checked
    * @return {@code true} if the condition matches and the component can be registered,
    * or {@code false} to veto the annotated component's registration
    */
   boolean matches(ConditionContext context, AnnotatedTypeMetadata metadata);

}
```

> 也就是说传入进来的参数必须实现该类接口并且返回值为true，才能在@@ConditionalOnClass下注入成功。

### @EnableConfigurationProperties

> 对于一般的配置，要想加载配置文件，首先是将配置文件中的属性用@ConfigurationProperties加载到bean中，但是这时，spring的bean容器中还是没有相应的bean的，要通过@EnableConfigurationProperties注解才能将该bean进行加载。

### @Import

> 使用@import的作用是将该类注入到spring容器中，值得注意的是，@import的注入是通过添加@Component注解来实现的。也就类似用@Configuration和@Bean向容器中放实例，不过两者间是有区别的。

### @ConditionalOnProperty

> 判断配置文件中的属性是否存在以及配置文件中的属性值是否与我们预定的属性值相同。

### @ConditionalOnMissingBean

> 判断当前类中是否存在该类bean，如果存在就不注入，如果不存在，就进行注入。