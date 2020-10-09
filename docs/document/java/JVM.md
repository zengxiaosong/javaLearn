# JAVA虚拟机

## 一、内存与回收机制

### 1、JVM及其体系结构

![image-20200917152216078](img\image-20200917152216078.png)

相关的概念： 

   *    OOM :  内存的溢出问题
   *    GC : 垃圾回收机制

结构层次：  高级语言---->  汇编语言 -------> 及其指令------> CPU

对比于c++,java能动态分配内存，以及具备垃圾回收机制，但是在c++中，我们都是由程序员来进行分配内存和回收垃圾。

#### 1、跨平台

java结尾的源文件，生成字节码文件（.class），然后在不同的JVM上去运行。

![image-20200922130459437](img\image-20200922130459437.png)

在java虚拟机平台上可以运行非java语言编写的程序，值得注意的是：java虚拟机并不关心在其内部运行的程序到底是何种语言编写的，它只关心“字节码”文件，只要其他编程语言的编译结果满足并包含java虚拟机的内部指令集，符号，及其他的辅助信息，他就是一个有效的字节码文件，就能被识别并装载运行。

#### 2、虚拟机

虚拟机可以分为系统虚拟机和程序虚拟机

* 系统虚拟机也就是Visual Box, VMware,这类就属于系统虚拟机，是在操作系统之上的软件层面对物理计算机的仿真，
* 程序虚拟机的典型带边就是java虚拟机，专门为执行单个计算机程序而设计的，**在java虚拟机中执行的指令我们成为java字节码指令**（javac编译之后形成的）。

#### 3、整体结构

![image-20200927145326480](img\image-20200927145326480.png)

> 个小部分的简单说明与介绍： 代码文件（.java） 通过前端编译器（javac）产生字节码文件（.class）,然后通过我们的类加载机制，加载到运行时数据区（内存空间），运行时数据区主要包含（方法区【method Area】，堆【heap】, java栈【java stack】, 本地方法栈【Native Method Stack】,程序技术器【Program Counter Register】) 值得注意的是，堆和方法区是多线程共享的。然后通过执行引擎（Execution Engine）翻译成机器指令供操作系统执行。

#### 4、执行流程

![image-20200927150640015](img\image-20200927150640015.png)



#### 5、相关指令

对于JVM来讲，是基于栈进行的，对于类似安卓系统来讲，是基于寄存器进行的。

> 这里顺便解释下为什么说java是解释性的语言，通过前面的学习，我们知道.java文件即使是被编译成.class，但是还是不能够被机器执行的，是需要通过jvm中的执行引擎通过逐一的解释，执行，才能被执行，因此，才说java是解释性的语言，所以，这里就有了前端编译器和后端解释器的概念。

栈：跨平台性，指令集小，指令多，执行性能比寄存器差。

这里先了解个概念：

关于反编译：也就是将class文件反编译成我们能够看的懂的源文件，有什么jad呀以及其他的什么之类的，以及我们的命令行参数javap命令，在相关的.class目录下使用命令 javap - v  program.class，就会生成相应的源文件码来使用。

#### 6、JVM的生命周期

**虚拟机的启动**：

* java虚拟机的启动时通过引导类加载器（bootstrap loader）创建一个初始类（initial class） 来完成的，这个类是由虚拟机的具体实现指定。

**虚拟机的执行：**

* 一个运行的java虚拟机有若干个清晰的任务：执行java程序。
* 程序开始执行时他才运行，程序结束时他就停止。
* 执行一个所谓的java程序时候，真真正正在执行的是一个java虚拟机的进程。

**虚拟机的退出：**

* 程序正常执行结束
* 程序在执行的过程中遇到异常或错误而异常终止
* 由于操作系统出现错误而导致java虚拟机进程终止。
* 某线程调动Runtime类或System类的exit方法，或Runtime类的halt方法，并且java安全管理器也允许这次exit或halt操作。



堆内存中存储的是new 出来的东西

方法区里面存储的是字节码

方法栈中存储的是方法，在被调用的时候使用，方法中的字面量存储在方法栈中，如果是引用数据类型，存储的是堆中的地址。

#### 7、常见的JVM版本

* Sun Classic VM: 最早的java虚拟机，执行引擎分由解释器（逐行进行解释）和JIT（just-in-time compilation）及时编译器（快速编译形成热点代码，并将机器指令存储在内存缓存中）组成，但是不能交替工作，只能单独工作，JIT是通过外挂的形式进行工作，不能识别数据是引用的地址还是基本数据类型。
* Exact VM :编译器能够与JIT混合工作，提升其效率，能够分别数据与引用，还具有热点探测的功能。
* BEA   JRockit : 牺牲了解释器，用JNT的模式 进行工作，常用在服务器端，是运行最快的虚拟机。
* Hotspot : 也就是我们最常见的。
* J9: 这个是IBM公司开发的，一般来说主要是公司内部产品的底层工具，性能较好，未开源。
* 以及其他的一些。

### 2、类加载子系统（class loader subSystem）

![image-20200930091742907](img\image-20200930091742907.png)

![image-20200930092318302](img\image-20200930092318302.png)

加载的过程：

![image-20201009091651571](img\image-20201009091651571.png)

#### 1、加载阶段

* 通过一个类的权限定名获取此类的二进制字节流
* 将这个二进制字节流所代表的静态存储结构转化为方法区的运行时数据结构
* 在内存中生成一个代表这个类的java.lang.Class对象，作为方法区这个类的各种数据的访问入口

![image-20201009092936825](img\image-20201009092936825.png)

#### 2、链接阶段

> 链接包含 验证（Verify），准备(Prepare)，解析(Resolve)

![image-20201009094739118](img\image-20201009094739118.png)

> 1、所谓验证，就是要在二进制流加载的过程中检验其字节码文件的合法性，class文件，也就是JVM所识别的文件，都会有一个 CA FE BA BE 的关键字节码。2、准备中的赋予初值要注意，如有：static int i = 1;是先赋值为0，在初始化阶段再赋值为1的。

#### 3、初始化阶段

![image-20201009095621706](img\image-20201009095621706.png)

对于执行<clinit>()方法的过程，我们用个例子来说明：

```java
public class MyTest {

    public static int num = 1;
    static {
        num = 5;
        number = 20;
        //System.out.println(number);   //非法向前引用
    }

    public static int number = 10;


    public static void main(String[] args) {
        System.out.println(MyTest.num);   //5
        System.out.println(MyTest.number);  //10
    }
}
```

> 在链接的准备阶段，对num=0;   number=0进行赋初值，然后到初始化阶段，执行<clinit>()进行赋值操作，num先赋值为1，然后赋值为5，number先赋值为20，然后赋值为10，注意的是，虽然可以进行赋值的左操作，但是不能进行右操作。同时，如果该类中没有静态变量，就不会执行<clinit>()函数。

关于多线程，指的是加载类的操作仅仅只会执行一遍，而不会执行多次。下面举个例子：

```java
/*
 *@author by java开发-曾
 *2020/9/26 16:11
 *文件说明：
 */
public class MyTest implements Runnable{

    public static void main(String[] args) {
       Runnable r = new Runnable() {
           @Override
           public void run() {
               System.out.println(Thread.currentThread().getName()+"开始");
               //类在线程中被加载
               Run run = new Run();
               System.out.println(Thread.currentThread().getName()+"结束");
           }
       };

       Thread th1 = new Thread(r,"线程1");
        Thread th2 = new Thread(r,"线程2");

        th1.start();
        th2.start();

    }

    @Override
    public void run() {
        System.out.println(Thread.currentThread().getName()+"开始");
        //类在线程中被加载
        Run run = new Run();
        System.out.println(Thread.currentThread().getName()+"结束");
    }
}

class Run{

    static {
        if(true){
            System.out.println("该类被加载"+Thread.currentThread().getName());
            while (true){

            }
        }

    }
}

//运行结果
线程1开始
线程2开始
该类被加载线程1
```

#### 4、关于加载器的简介

> 类在被加载的过程，我们对加载器进行分类，分为两种 ：bootStrapClassloader(引导类加载器) 和 自定义加载器，
>
> 对于引导类加载器，他是使用c++/c语言编写的，对于自定义加载器，是直接或者间接的继承了Classloader,是在java中可以具体实现的。对于这几类加载器，是没有具体的继承等关系的，但是有上下级的关系。具体在下面的代码中可以看出：

```java
//获取系统类加载器
ClassLoader appClassloader = ClassLoader.getSystemClassLoader();
System.out.println(appClassloader);  //  sun.misc.Launcher$AppClassLoader@18b4aac2

//获取上层的扩展类加载器
ClassLoader exClassLoader = appClassloader.getParent();
System.out.println(exClassLoader);  //  sun.misc.Launcher$ExtClassLoader@1b6d3586

//获取上层的引导类加载器
ClassLoader bootClassloader = exClassLoader.getParent();
System.out.println(bootClassloader); //null: 因为引导类加载器是用c++/c 进行编写的
                                     //已经超出了java的范畴，是获取不到该对象的。

//看下我们具体使用的类是用哪些加载器来加载的

//我们的自定义类：MyTest  可以看出是用的系统类加载器
ClassLoader classLoader = MyTest.class.getClassLoader();
System.out.println(classLoader);   //sun.misc.Launcher$AppClassLoader@18b4aac2

//在看看我们的String  使用的是引导类   值得注意的是，我们java中的核心类库都是由引导类加载器进行加载的
ClassLoader classLoader1 = String.class.getClassLoader();
System.out.println(classLoader1);  //null
```

具体的介绍：

![image-20201009144559325](img\image-20201009144559325.png)

![image-20201009145113009](img\image-20201009145113009.png)

![image-20201009145543640](img\image-20201009145543640.png)

获取加载器加载的路径：

```java
//获取引导类加载器的类文件的目录
  URL[] urLs = Launcher.getBootstrapClassPath().getURLs();
  for (URL urL : urLs) {
      System.out.println(urL);
  }

  //同样，我们也可以来查看扩展类加载器加载的目录结构是什么
  String property = System.getProperty("java.ext.dirs");
  for (String url: property.split(";")) {
      System.out.println(url);
  }
```

![image-20201009151508212](img\image-20201009151508212.png)

![image-20201009152029560](img\image-20201009152029560.png)

#### 5、双亲委派机制

> java虚拟机对class文件采用的是按需加载的方式，也就是说当需要使用该类时才会将它的class文件加载到内存生成的class对象中，而且加载某个类的class文件时，java虚拟机采用的是双亲委派模式，即把请求交由父类处理，他是一种任务委派模式。

![image-20201009154453028](img\image-20201009154453028.png)

优势：

* 避免类被重复加载
* 起保护作用，比如自定义类的包名为java.lang ,但是引导类加载器具有权限，因此是不能被加载的。

#### 6、沙箱安全机制

一、概念

保证对java核心源代码的保护，就是沙箱安全机制。
就是一种保护机制，保护源代码，保护JVM不受恶意代码的破坏。

二、示例

自定义的String类（java.lang.String），但是加载自定义类的时候回率先使用引导类加载器加载，而引导类加载器在加载的时候先加载jdk自带的文件(rt.jar包中的java\lang\String.class)，不会加载自定义的String类，这样就保护了java的源码，不会受到污染。

#### 7、其他

1）在JVM中表示两个class对象是否为同一个类存在的必要条件：

* 类的完整类名必须一致，包括类名
* 加载这个类的Classloader(指ClassLoader实例对象)必须相同

> 换句话说，在JVM中，即时这两个类对象(class对象)来源于同一个class文件，被同一个虚拟机加载，但是加载器不同，两个类对象也是不相等的。

![image-20201009201637316](img\image-20201009201637316.png)

类的主动使用与被动使用

![image-20201009202132269](img\image-20201009202132269.png)

### 3、运行时数据区概述及线程

#### 1、简介

![image-20201009203817985](img\image-20201009203817985.png)

![image-20201009204406992](img\image-20201009204406992.png)

#### 2、程序计数器

