# java的设计模式

> 学习一些java的设计模式，有利于后面框架的学习以及源码部分的理解
>
> 设计模式（Design pattern）是一套被反复使用、多数人知晓的、经过分类编目的、代码设计经验的总结。使用设计模式是为了可重用代码、让代码更容易被他人理解、保证代码可靠性。 毫无疑问，设计模式于己于他人于系统都是多赢的，设计模式使代码编制真正工程化，设计模式是软件工程的基石，如同大厦的一块块砖石一样。项目中合理的运用设计模式可以完美的解决很多问题，每种模式在现在中都有相应的原理来与之对应，每一个模式描述了一个在我们周围不断重复发生的问题，以及该问题的核心解决方案，这也是它能被广泛应用的原因。

![image-20200824164655193](img\image-20200824164655193.png)

## 1、创建型模式

### （1）单例模式

单例模式，其定义就是全局只能产生一个实例，并提供一个全局访问的点。

单例模式具备三个典型的特点：

					* 只有一个实例
					* 自我实例化
					* 只提供一个全局访问点

> 单例模式是[设计模式](http://c.biancheng.net/design_pattern/)中最简单的模式之一。通常，普通类的构造函数是公有的，外部类可以通过“new 构造函数”来生成多个实例。但是，如果将类的构造函数设为私有的，外部类就无法调用该构造函数，也就无法生成多个实例。这时该类自身必须定义一个静态私有实例，并向外提供一个静态的公有函数用于创建或获取该静态私有实例。

单例模式的主要优点就是节约系统资源、提高了系统效率，同时也能够严格控制客户对它的访问。也许就是因为系统中只有一个实例，这样就导致了单例类的职责过重，违背了“单一职责原则”，同时也没有抽象类，所以扩展起来有一定的困难

结构图如下：

![image-20200825092936494](img\image-20200825092936494.png)

测试代码如下：分别分为懒汉式和饿汉式：

饿汉式：（开始就直接构造好实例）

```java
package com.zxs.javaDesign;

/*
 *@author by java开发-曾
 *2020/8/25 10:25
 *文件说明：饿汉式
 */
public class Singleton {

    //静态成员变量
    private static final Singleton instance = new Singleton();
    //构造函数私有，只能通过静态类来使用，不允许外部构建实例
    private Singleton(){ }
    //公共全局访问方法
    public static Singleton getSingleton(){
        return instance;
    }

    public void doPrint(){
        System.out.println("实例已经被创建");
    }
}
```

测试：

```java
@Test
public void myTest(){
    Singleton singleton = Singleton.getSingleton();
    singleton.doPrint();
    System.out.println(singleton);

    //再次获取
    Singleton singleton1 = Singleton.getSingleton();
    System.out.println(singleton1);

}
```

懒汉式：（需要的时候再构造，不需要就不构造）

```java
package com.zxs.javaDesign;

/*
 *@author by java开发-曾
 *2020/8/25 10:39
 *文件说明：懒汉式
 */
public class Singleton2 {

    //静态常量成员，不需要
    private static Singleton2 instance = null;

    private Singleton2(){}

    public  static Singleton2 getSingleton(){
        if(instance==null){
            instance = new Singleton2();
        }else{
            System.out.println("该实例已经被构建");
        }
        return instance;
    }

}
```

测试：

```java
@Test
public void myTest(){
    Singleton2 singleton = Singleton2.getSingleton();
    System.out.println(singleton);

    Singleton2 singleton2 = Singleton2.getSingleton();
    System.out.println(singleton2);
}
```

## 2、结构型模式

###  （1）代理模式

代理模式的概念：比如说厂家生产了一个产品，他需要卖出去，于是找了代理商帮他卖，代理商在卖产品的时候做了销售前的包装，销售后的回访等，同样，顾客也可以直接去找厂家买，这就是代理模式。

特点：

- 代理模式在客户端与目标对象之间起到一个中介作用和保护目标对象的作用；
- 代理对象可以扩展目标对象的功能；
- 代理模式能将客户端与目标对象分离，在一定程度上降低了系统的耦合度；

缺点：

- 在客户端和目标对象之间增加一个代理对象，会造成请求处理速度变慢；
- 增加了系统的复杂度；

实现原理: 既然是代理，那么他们之间的方法应该是差不多的，因此他们应该有继承的接口或者抽象类。共有三个角色：抽象主题，真实主题，代理类，同样，代理模式也要分为静态代理和动态代理：

![](img\3-1Q115093011523.gif)

静态代理：

```java
//抽象接口
public interface Subject {
    void request();
}

//真实的主题类
public class RealSubject implements Subject {
    @Override
    public void request() {
        System.out.println("生产了一个苹果");
    }
}

//代理类
public class Proxy implements Subject{

    private RealSubject realSubject;

    @Override
    public void request() {
        if (realSubject==null){
            realSubject = new RealSubject();
        }
        preRequest();
        realSubject.request();
        posRequest();
    }

    private void preRequest() {
        System.out.println("真实处理之前的处理事件");
    }

    private void posRequest(){
        System.out.println("真实处理之后的处理事件");
    }
}

//测试类
public class myTest {
    @Test
    public void myTest(){
        Proxy proxy = new Proxy();
        proxy.request();
    }
}
```

动态代理：

动态代理比较复杂，我基本在代码中给了解释：多理解：

增加一个代理工具类 `ProxyUtil`:

```java
package com.zxs.proxy;

import java.lang.reflect.InvocationHandler;
import java.lang.reflect.Method;
import java.lang.reflect.Proxy;

/*
 *@author by java开发-曾
 *2020/8/27 15:12
 *文件说明： 设置动态代理工具类
 */
public class ProxyUtil {

    //获取动态代理需要的实际类
    private Object instance;

    public ProxyUtil() {
    }

    //设置获取的构造函数
    public ProxyUtil(Object instance) {
        this.instance = instance;
    }

    public Object getProxy(){
        //获取类加载器
        ClassLoader loader = this.getClass().getClassLoader();
        //获取实际类所调用的所有接口
        final Class [] interfaces = instance.getClass().getInterfaces();
        //获取相应的代理类
        return Proxy.newProxyInstance(loader, interfaces, new InvocationHandler() {
            /*
              代理类的执行方法，每个方法执行时应该做的事
              这里很明显，由于代理类继承了前面的interfaces,所以指的方法就是指接口中的方法
             */
            @Override
            public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {

                //增加前置通知
                System.out.println("这是前置通知");

                //既然要代理，那么实际执行的应该是原理实际类中执行的方法
                Object result = method.invoke(instance,args);

                //增加后置通知
                System.out.println("这是后置通知");
                return result;
            }
        });
    }

}
```

测试类：

```java
ProxyUtil proxyUtil = new ProxyUtil(new RealSubject());
Subject subject =(Subject) proxyUtil.getProxy();
subject.request();
```

实现效果：

![image-20200827153256629](img\image-20200827153256629.png)

## 3、行为型模式



