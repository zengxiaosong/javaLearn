# Integer与int的区别 (== 与 equal) 
-----

> 先来看下Java中的8种基本类型和3种引用数据类型  
8种基本数据类型:``` boolean byte int char long short float double```  
3种引用数据类型: ``` 类 数组 接口```  

Java是面向对象的编程式语言，主要用类来表达，但是，为了方面，要用到基本数据类型。但是，最终的实现还是要依靠类来实现的。  
说白了Integer就是int的封装类，与之相应的是，其他几种基本类型对应的封装类分别是  

char|boolean|byte|int|long|short|float|double
:-:|:-:|:-:|:-:|:-:|:-:|:-:|:-:  
Charactor|Boolean|Byte|Integer|Long|Short|Float|Double

> 这里面有很多的东西，我们就只是选择```int和Integer```说明解释下就行，其他的读者可以自行去参考java的jdk参考文档，这里讲解的过程中，我们会同时说明下```== 和 equal 的区别```

> 首先来看看基本数据类型的存储方式   

**基本数据类型的存储原理：所有的简单数据类型不存在“引用”的概念，基本数据类型都是直接存储在内存中的内存栈上的，数据本身的值就是存储在栈空间里面**  

java中对象怎么存储的，我们应该清楚，new开辟内存空间进行存储，每new一次就开辟一次。  

**int定义时默认值时0，但是 Integer默认值时null**

> 接下来我们根据具体的操作来判定  

```
Integer i = 124
Integer j = 128
//注意，这里基本数据类型的表示是有差别的
```
在内存中，对于int基本类型的数据，会有一个缓存，```-128~127```，也就是说，我们对Integer直接赋予字面值的时候，如果值在这个区间内，不会开辟新的空间，会直接进行赋值。同样，我们知道在Java中，对象的引用要么是赋予另一个引用，要么就是new一个实例出来，那这里在Java中是怎么实现的呢？在编译```Integer i = 124```的时候，会翻译成```Integer i  = Integer.valueOf(124)``` 这里我们能看出来，是调用Integer的一个静态方法，接下来我们来看下源码是怎么样的吧： **怎么来看源码呢？不会的话我说下吧，针对idea,双击shift键，输入要查找的源码，进去查看就行了。**

```
public static Integer valueOf(int i) {
        if (i >= IntegerCache.low && i <= IntegerCache.high)
            return IntegerCache.cache[i + (-IntegerCache.low)];
        return new Integer(i);
    }
```
看到这里我们就很清楚了吧，超出这个缓存区的值，就要重新去开辟一块新的空间。用代码来检验一下吧  

```
Integer i = 120;
Integer j = 120;
System.out.print(i==j)   //显示true  

Integer i = 128;
Integer j = 128;
System.out.print(i==j)   //超过这个值，显示true  
```  
换一种话来说，这个过程就是将基本数据类型封装成对象引用的数据类型，也就是装箱。那么既然有装箱，肯定有拆箱，是怎么来拆的呢？什么情况下需要拆呢？既然是数据，那么肯定是会有如```+ - * / ```的之类的计算的，也就是说，在进行计算的时候，会隐式的转换成基本数据类型进行计算。

---

> 基本性质的东西讲完了，我们来看看==和equal之间的关系吧，

首先讲一下基本的，== 对于基本数据类型来讲，就是比较值是否相等，对于对象的引用，那就是比较地址是否相等。equal是比较内容，也就是存放在地址中的值是否相等。来看看Integer的equal方法的源码。  

```
   public boolean equals(Object obj) {
        if (obj instanceof Integer) {
            return value == ((Integer)obj).intValue();
        }
        return false;
    }
```  
很清晰，很明了吧。接下来看看一些例子帮助理解吧  

```
Integer i = new Integer(120);
Integer j = new Integer(120);
System.out.print(i==j)        //false
System.out.print(i.equal(j))  //true
```

> 除了上面的几种类型可以直接赋予字面值，还有一个我们常用的String类，也是可以直接赋予的，这里我们补充讲解下吧。上测试代码


```
 String s1 = "hello";
 String s2 = "hello";
 String s3 = new String("hello");
 System.out.println(s1==s2);  //true
 System.out.println(s1==s3);  //false
 System.out.println(s1.equals(s3));  //true
```
怎么来解释呢？我们要明确一个概念，叫做***字符串缓冲池***。什么意思呢，就是说我们在创建s1的时候，会将这个字符串放到缓冲池中，当我们用同样的形式去创建s2的时候，他会直接去缓冲池里面去找，并把地址的引用赋过来，那么s3呢？也就是我们说的，去new一块新的空间，地址当然不同。  

> 最后看看String的源码吧

```
public boolean equals(Object anObject) {
        if (this == anObject) {
            return true;
        }
        if (anObject instanceof String) {
            String anotherString = (String)anObject;
            int n = value.length;
            if (n == anotherString.value.length) {
                char v1[] = value;
                char v2[] = anotherString.value;
                int i = 0;
                while (n-- != 0) {
                    if (v1[i] != v2[i])
                        return false;
                    i++;
                }
                return true;
            }
        }
        return false;
    }
```



