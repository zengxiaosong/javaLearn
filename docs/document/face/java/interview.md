# 面试题相关

## java

### 静态static

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

### try/catch

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

### 线程竞争

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

### 字符类型char

4、语句：char foo='中'，是否正确？（假设源文件以GB2312编码存储，并且以javac – encoding GB2312命令编译）

> 正确的，Java语言中，中文字符所占的字节数取决于字符的编码方式，一般情况下，采用ISO8859-1编码方式时，一个中文字符与一个英文字符一样只占1个字节；采用GB2312或GBK编码方式时，一个中文字符占2个字节；而采用UTF-8编码方式时，一个中文字符会占3个字节。为什么java里的char是2个字节？因为java内部都是用unicode的，
>

### 接口的定义

接口Interface

​	1、接口中的成员变量默认都是public、static、final类型的，必须被显式初始化

​	2、接口中的方法默认都是public、abstract类型的。

​	3、接口中只能包含public、static、final类型的成员变量和public、abstract类型的成员方法。

​	4、接口没有构造方法，不能被实例化，在接口中定义构造方法是非法的。

​	5、一个接口不能实现另一个接口，但它可以继承多个其他接口。



## sql

1、jdbc的事务必须在一个数据库连接上完成。编程时必须去掉数据库的自动提交功能。当成功后调用commit，当失败后调用rollback。判断这句话正确与否

> 正确，一般情况下，在执行类似executeUpdata()函数后，会自动提交，也就是会执行 connection.setAutoCommit(true)语句，但是在我们的事务发生时，需要将自动提交设置为false。