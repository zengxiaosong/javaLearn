# 面试题相关

## java

### 一、静态static

​       1、When is the text “Hi there”displayed?

```java
public class StaticTest
{
    static
    {
        System.out.println("Hi there");
    }

    public void print()
    {
        System.out.println("Hello");
    }

    public static void main(String args[])
    {
        StaticTest st1 = new StaticTest();
        st1.print();
        StaticTest st2 = new StaticTest();
        st2.print();
    }
}
```

```
Never.
Each time a new object of type StaticTest is created.
Once when the class is loaded into the Java virtual machine.  //对
Only when the main() method is executed.
```

> static作为静态修饰符，可以修饰成员变量，成员方法，**代码块（如上面这种形式）** 当然，被修饰后仅仅在类被加载的时候才会执行。值得注意的是，静态方法不能调用非静态方法或者是非静态变量，解释如下：静态变量被加载存在时，非静态方法或者变量还不存在，因此不能被访问。

### 二、try/catch

2、下面代码运行结果是（）

```
public class Test{	
    public int add(int a,int b){	
         try {	
             return a+b;		
         } 
        catch (Exception e) {	
            System.out.println("catch语句块");	
         }	
         finally{	
             System.out.println("finally语句块");	
         }	
         return 0;	
    } 
     public static void main(String argv[]){ 
         Test test =new Test(); 
         System.out.println("和是："+test.add(9, 34)); 
     }
}
```

```
catch语句块
和是：43

编译异常

finally语句块
和是：43     //对

和是：43
finally语句块
```

> 说明下try...catch...finally的运行机制，按照上面这种情况，在try中执行完代码后当执行return时，会先将结果值保存到临时栈中，然后再去执行finally中的代码，如果finally中也有return语句，这个时候，就会去更新临时栈。

### 三、线程竞争

3、下面程序的运行结果是：（  ）

```java
public static void main(String args[]) {

    Thread t = new Thread() {
        public void run() {
            pong();
        }
    };
    t.run();
    System.out.print("ping");
}
static void pong() {
    System.out.print("pong");
}
```

> t.run直接执行代码，按顺序打印代码；
>
> t.start是另起线程，与当前线程同时竞争cpu资源，结果存在不确定性

### 四、字符类型char

4、语句：char foo='中'，是否正确？（假设源文件以GB2312编码存储，并且以javac – encoding GB2312命令编译）

> 正确的，Java语言中，中文字符所占的字节数取决于字符的编码方式，一般情况下，采用ISO8859-1编码方式时，一个中文字符与一个英文字符一样只占1个字节；采用GB2312或GBK编码方式时，一个中文字符占2个字节；而采用UTF-8编码方式时，一个中文字符会占3个字节。为什么java里的char是2个字节？因为java内部都是用unicode的，

顺便说下 java中的基本数据类型所占据的字节数：

double(8)>float(4)>long(8)>int(4)>short(2)>byte(1)   bool(1)    char(2)    

//同时要注意的是：这里的前八种大转小要用强转，但是小转大就能自动转换，  并且要注意的是： 对于byte, short, 在运算时，要转换为int    ,但是+= ，-= 这种运算，会自动强制转换类型。

### 五、接口的定义

接口Interface

​	1、接口中的成员变量默认都是public、static、final类型的，必须被显式初始化

​	2、接口中的方法默认都是public、abstract类型的。

​	3、接口中只能包含public、static、final类型的成员变量和public、abstract类型的成员方法。

​	4、接口没有构造方法，不能被实例化，在接口中定义构造方法是非法的。

​	5、一个接口不能实现另一个接口，但它可以继承多个其他接口。



### 六、运算符的优先级

![image-20200917161947670](img\image-20200917161947670.png)

> 题中符号的优先级排序是：'>'，'<'，'&&'，'||'。 选择B

### 七、Exception

![image-20200917165134568](img\image-20200917165134568.png)

<img src="img\Exception.jpg" alt="Exception" style="zoom:33%;" />

### 八、String（buffer）

![image-20200917165733793](img\image-20200917165733793.png)

解析：

> StringBuffer s = new StringBuffer(x); x为初始化容量长度
> s.append("Y"); "Y"表示长度为y的字符串
> length始终返回当前长度即y；
> 对于s.capacity()：
> 1.当y<x时，值为x
> 以下情况，容器容量需要扩展
> 2.当x<y<2*x+2时，值为 2*x+2
> 3.当y>2*x+2时，值为y

### 九、方法关键字

#### native

native关键字说明其修饰的方法是一个原生态方法，方法对应的实现不是在当前文件，而是在用其他语言（如C和C++）实现的文件中。Java语言本身不能对操作系统底层进行访问和操作，但是可以通过JNI接口调用其他语言来实现对底层的访问。

JNI是Java本机接口（Java Native Interface），是一个本机编程接口，它是Java软件开发工具箱（java Software Development Kit，SDK）的一部分。JNI允许Java代码使用以其他语言编写的代码和代码库。Invocation API（JNI的一部分）可以用来将Java虚拟机（JVM）嵌入到本机应用程序中，从而允许程序员从本机代码内部调用Java代码。

### 十、关于Hash的问题

![image-20200917202344478](img\image-20200917202344478.png)

>1、首先我们要明白hashcode是啥，在Object中，hashcode为每个对象的地址映射而成的int值，一般来讲，不同的对象hashcode是不同的，但是他的生成不是绝对可靠的，但是速度快啊，因此，在类似于hashMap中，判断map中是否存在相同的对象，先是判断hashcode,然后是判断equal()。因为equal()是可靠的，但是效率低。（注意，这里我们指的equal()是指Object中没有重写过的，类似于String中已经重写过的，是用来判断值是否相同）

### 十一、关于super()/this()

![image-20200917210836421](img\image-20200917210836421.png)

>  super() == 父类构造器 
>
>  this() == 当前类其他类构造器 
>
>  类的生命周期包括:加载.连接.初始化,使用,卸载,5个阶段. 
>
>  初始化过程分为 
>
>- ​    **执行类构造器****<clinit>（）:主要是\**类变量的赋值动作的和静态语句块的中的语句合并.即static数据的处理\****      
>- ​    **执行类的构造器<init>():构造方法实例变量的初始化赋值**     
>
>   **所以static变量和static代码块是在构造方法之前执行的,那时候还没有构造方法.(D选项)**  
>
>  初始化的触发机制有5种,其中当初始化一个类的时候，如果发现**父类没有被初始化，需要初始化父类.所以一定是父类被初始化之后,才会有子类别初始化.** 
> 
>
>  **如果在子类调用父类的super()构造方法,可以理解为还是对父类的一种初始化,不放在第一行的话,不符合父类被初始化才会有子类初始化,逻辑混乱.所以必须放在第一行,** 
>
>  **如果在子类中掉用不同的this()构造方法,放在第一行理解为当前构造方法可以是被调用的构造方法的一种增强.不放在第一行的话,个人认为执行逻辑理解为会有两构造器执行,第一个构造器执行到一半,在执行一个构造器,逻辑上不通.** 
>
>  **this和super要求都在第一行,所以不能同时存在,而且逻辑上也不通.**

![image-20200918161823370](img\image-20200918161823370.png)

>解析：A中，不能使用this来调用类方法（即静态方法），因为类方法比对象先加载，C中除了可以调用静态方法，还可以调用静态变量，D中我们可以先在方法中实例化一个对象，再去调用该对象的实例化方法。

### 其他类别

![image-20200917162520393](img\image-20200917162520393.png)

解析：

> 每一个Java应用程序都会有一个带String[] args参数的main方法，这个参数表明main方法将接受一个字符串数组，也就是命令行参数。  若采用命令行“java Test one two three”运行程序，args数组将包含下列内容：  
>
>   args[0]: "one"  
>
>   args[1]: "two"
>
>   args[2]: "three"  
>
>   与C/C++中main方法中的参数int argc, char* argv[]不同，程序名Test并没有储存在args数组中，所以args[0]不是Test。

## sql

1、jdbc的事务必须在一个数据库连接上完成。编程时必须去掉数据库的自动提交功能。当成功后调用commit，当失败后调用rollback。判断这句话正确与否

> 正确，一般情况下，在执行类似executeUpdata()函数后，会自动提交，也就是会执行 connection.setAutoCommit(true)语句，但是在我们的事务发生时，需要将自动提交设置为false。

## JVM部分

![image-20200917164110790](img\image-20200917164110790.png)



```
public class Father { 
   public void say(){ 
     System.out.println("father"); 
   } 
   public static void action(){ 
     System.out.println("爸爸打儿子！"); 
   } 
} 
public class Son extends Father{ 
  public void say() { 
    System.out.println("son"); 

  }

  public static void action(){ 
     System.out.println("打打！"); 
   }

  public static void main(String[] args) { 
    Father f=new Son(); 
    f.say(); 
    f.action(); 
  } 

}
```

> 输出：son
>
> ​      爸爸打儿子！
>
> 当调用say方法执行的是Son的方法，也就是重写的say方法
> 而当调用action方法时，执行的是father的方法。
>
> 普通方法，运用的是动态单分配，是根据new的类型确定对象，从而确定调用的方法；
>
> 静态方法，运用的是静态多分派，即根据静态类型确定对象，因此不是根据new的类型确定调用的方法

![image-20200917170529991](img\image-20200917170529991.png)

> 答案AD ,(先记住)



![image-20200917170904348](E:\myStudy\docisfy\javaLearn\docs\document\face\java\img\image-20200917170904348.png)

> ABD 先记住

