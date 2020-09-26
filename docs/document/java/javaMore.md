# java初级部分

## 一、 数组

### 1、数组的定义

数组就是用来存储同一类型若干数据的`容器`。

数组定义语法：（动态初始化）

​         													 数据类型[]  数组名 = new 数据类型[length]  

​														或者：

​         													 数据类型 数组名[] = new 数据类型[length]  

定义后，根据每个元素的需要来进行赋值当然，也可以使用静态初始化：

如： int []  num = new int[]{1,2,3}    //这里就可以不定义数组的长度了，因为长度就随着数组中元素的定义而被定义了。

也可以使用如下： int []  num = {1,2,3}    对于数组的访问，我们可以使用`for/foreach`循环的方式或者是通过`Arrays.toString()`的静态方法来访问数组。

> 说明：new运算符在**堆内存**中分配一块连续的存储空间，我们把这块存储空间的引用赋值给变量。并通过数组的索引值来访问数组，数组的最大下标为length-1.

数组具有length属性，访问时通过:  `数组名.length`

### 2、数组的深入理解

```java
public static void main(String[] args) {
    int [] num = new int[5];
}
```

![image-20200923090734284](img\image-20200923090734284.png)

当我们在main方法中新建一个数组时，通过new 关键字就会在我们jvm中的堆内存中开辟一块连续的地址来存放数组中的每一个元素，然后将这块连续的内存的首地址赋值给我们的num变量，然后将num变量存储到临时栈中。

那么，我们为啥可以通过下标索引来访问呢？是因为num[1] 访问的原理是通过  首地址+4*1的方式，这里4是指int占4个字节。

> 所以说为什么数组的访问速度要快于链表，就是因为数组是直接访问，时间复杂度为1，而链表要遍历找到其地址，速度上就慢是哪个很多了。

### 3、相关应用

数组可以用在变长参数中:  

​		如 `public void  getArray(int ... array){}`    这里面，我们在访问参数的时候，就可以使用array[i]的形式，或者传递的参数也可以是数组类型的。如`getArray(array);`

数组的扩容问题：

​		因为数组在定义时，长度是定长的，因此就会出现可能长度不够的情况，这个时候，就要考虑到数组扩容的问题了，那扩容的步骤是怎么样的呢？

				*  定义一个新的数组，长度只要比原数组长就可以了
				*  将原数组地址中的内容拷贝到新数组中的地址位置
				*  将新数组的地址位置赋值给原数组的变量

具体也可以使用：

```java
int [] ints = new  int[]{1,2};
int [] anInts = new int[ints.length+ints.length/2];
System.arraycopy(ints,0,anInts,0,ints.length);   //系统的方法
ints = anInts;
System.out.println(Arrays.toString(ints));
```

查看该方法的源码：

```java
 * @param      src      the source array.
 * @param      srcPos   starting position in the source array.
 * @param      dest     the destination array.
 * @param      destPos  starting position in the destination data.
 * @param      length   the number of array elements to be copied.
 * @exception  IndexOutOfBoundsException  if copying would cause
 *               access of data outside array bounds.
 * @exception  ArrayStoreException  if an element in the <code>src</code>
 *               array could not be stored into the <code>dest</code> array
 *               because of a type mismatch.
 * @exception  NullPointerException if either <code>src</code> or
 *               <code>dest</code> is <code>null</code>.
 */
public static native void arraycopy(Object src,  int  srcPos,
                                    Object dest, int destPos,
                                    int length);
```

查看该方法的注释说明就能明白该方法是如何使用的了，这里值得注意的是该方法中并没有方法体，要知道没有方法体是需要abstract关键词标注的，但这里是有native关键字，也就是说，通过这个关键字，我们这个方法是通过其他语言来实现的。

或者是使用Arrays中的静态方法：

```java
ints = Arrays.copyOf(ints, ints.length * 2);
System.out.println(Arrays.toString(ints));
```

```java
* @param original the array to be copied
 * @param newLength the length of the copy to be returned
 * @return a copy of the original array, truncated or padded with zeros
 *     to obtain the specified length
 * @throws NegativeArraySizeException if <tt>newLength</tt> is negative
 * @throws NullPointerException if <tt>original</tt> is null
 * @since 1.6
 */
public static int[] copyOf(int[] original, int newLength) {
    int[] copy = new int[newLength];
    System.arraycopy(original, 0, copy, 0,
                     Math.min(original.length, newLength));
    return copy;
}
```

通过上面的扩容，我们就可以对数组进行插入删除操作了。

> 数组的优点：查询快，直接计算地址值获取元素
>
> 数组的缺点：删除和插入慢，要移动大量的元素节点。

**同时，对数组的操作很多部分都放在了工具类Arrays中了，要常使用此类对数组进行操作**

## 二、String

### 1、基本

对于String，相关的操作可以直接去查相关的接口文档就可以了，这里主要说明下关于字符串拼接的问题：

例如： 

String  s1 = "abc" + "df";     //这里在javac编译阶段会进行常量优化,因此编译之后就直接是s1="abcdf"，并且只生成了一个对象。

String s2 = "abcdf";

s1==s2      //true     //经过上面的过程，这里就显示为true

### 2、经典面试题

关于String 的经典面试案例：

```java
String s1 = "abc";
String s2 = s1 + "df";
```

> 这里同样是两个字符串进行相加，为什么不是上面的那种呢？那是因为，在上面是两个字面量进行相加，会被编译器优化，在这里是通过变量s1, 这里的实现步骤是：通过`Stringbuilder`的append()方法，连接了两个字符串，因此并没有创建实例化对象，而是经过append()方法后通过`StringBuilder`的toString()方法，返回了一个新的对象。

```java
String s1 = new String("abc");
String s2 = new String("abc");
```

> 对这个例子来看，创建了三个对象，具体解释为：先创建字面量实例化对象“abc”,然后通过这个常量new了两个实例化对象。

### 3、线程安全问题

浅显的说下关于String，`StringBuffer`，`StringBuilder`的线程安全问题，查看源码，我们发现

在String 源码中有这样的数据：

```java
/** The value is used for character storage. */
private final char value[];
```

> 也就是说，String字符串内部的存储是按照字符数组的格式进行存储的，并定义为常量，引用的地址位置不能改变，并且，最终存储的位置是字面量的地址，并且在该类中，也不存在对该类中值的修改。因此线程安全。

在`StringBuffer`中存在这样的源码：

```java
@Override
public synchronized int length() {
    return count;
}
@Override
public synchronized int capacity() {
    return value.length;
}
```

> 在方法前面添加了关键字synchronized，也就是为方法加上了线程锁，因此在访问的时候，是不允许其他线程进行访问的。所以说对于临界资源的访问，是线程安全的。

在`StringBuilder`中，对字符串的相关操作并没有加上锁，因此来讲是非线程安全的，，但是总的来讲，他的效率也是最高的，因为线程安全会阻碍线程的同步化，并且是以牺牲效率为代价的。

## 三、数据库事务的隔离级别

数据库的事务有四个特性：

* 持久性：指数据永远的保存在数据库的磁盘中。
* 原子性：指在事务中的操作要具有原子性，也就是要么都执行，要么都不执行。比如转账的操作，一方增加钱，一方减少钱
* 一致性：也就是指事务在开始前和开始后数据库中的数据是一致的，比如转账操作，最终钱的数目是一致的。
* 隔离性：隔离性也分为四种，具体是指多线程并发的情况下，事务之间的干扰问题

这里重点描述下事务的隔离级别：

* `(read uncommited)读未提交`，会产生脏读，不可重复读，幻读等现象，具体表现为：**当A事务正在对数据库中的表进行增删改操作时，但此时还未提交事务，此时B事务对该数据库进行读取数据，那么就会读取到b中未提交的数据，产生了脏读。**（也就是写允许读，不允许写，读允许读写）
* (read commited) 读已提交，避免了脏读，会产生不可重复读的，幻读的现象，具体表现为：**当事务A读取表中的数据时，事务B对该表中的数据进行了修改，并且已经进行了提交，此时A再次读取的数据就与前面读取的数据不一致了，因此产生了不可重复读。**（读允许读写，写不允许读写）
* (repeatable read)可重复读，避免了脏读，避免了不可重复读。具体表现为：当A事务进行读取数据的时候，是不允许其他事务对该数据进行写的（update/delete操作），（读不允许写，允许读，写不允许读写）
* `(Serializable)可序化`,前面的可重复读虽然是读的时候不允许进行（update/delete）操作，但是允许进行insert操作，因此，如果先读取了数据，再进行插入操作，再次读取数据的时候，就会造成幻读，多了一些其他的数据。可序化就是指在读的时候是不允许进行insert操作的，（读不允许写，insert操作，但是允许读操作，写不允许任何操作）

值得注意的是，对于这样的隔离级别，隔离级别越高，允许的并发性能就越低，所以最后的串行化操作，基本上是没有进行并行操作的。

## 四、线程安全与非线程安全



## 五、Date相关日期类

### 1、Date的相关

```java
//日期类的相关操作,获取当前系统的时间
Date date = new Date();
System.out.println(date);

//获取系统时间的毫秒级
long currentTime = System.currentTimeMillis();
System.out.println(currentTime);

//将这个毫秒数转为日期的格式进行输出
Date date1 = new Date(currentTime);
System.out.println(date1);

//获取日期的天，年，月，等相关信息，可以使用下面的相关方式，不过这些方法是已经快过时的
System.out.println(date1.getDate());

//将日期格式转换为字符串的格式
SimpleDateFormat sd = new SimpleDateFormat("yyyy-MM-dd hh:mm:ss");
String format = sd.format(date1);
System.out.println(format);

//将字符串格式的日期转换为Date类型的
String nowTime = "2020年05月22日 22:13:45";
SimpleDateFormat sd1 =  new SimpleDateFormat("yyyy年MM月dd日 hh:mm:ss");
Date newDate = sd1.parse(nowTime);
System.out.println(newDate);
```

### 2、LocalDateTime的相关操作

```java
/**
*  值得注意的是：该  LocalDateTime是没有公共的构造方法的，
 *  因此要获得该类实例化的对象，需要通过该类的静态对象,
 *  该类相对于Data类来讲，是线程安全的，其值是final类型的
*/
LocalDateTime localDateTime = LocalDateTime.now();
System.out.println(localDateTime);  //2020-09-26T16:17:47.959  产生了一个毫秒级别的时间日期
//获取相关的属性
System.out.println(localDateTime.getDayOfMonth());

//将该格式转换成字符串格式
DateTimeFormatter dateTimeFormatter = DateTimeFormatter.ofPattern("yyyy-MM-dd HH:mm:ss");
String format = localDateTime.format(dateTimeFormatter);
System.out.println(format);

//将字符串格式的数据转换为LocalDateTime类型的
String dateTime = "2020年05月07 19:28:36";
DateTimeFormatter dateTimeFormatter1 = DateTimeFormatter.ofPattern("yyyy年MM月dd HH:mm:ss");
LocalDateTime localDateTime1 = LocalDateTime.parse(dateTime, dateTimeFormatter1);
System.out.println(localDateTime1);
```

## 6、Math相关类的使用

### Math类和Random类

```java
//Math
//相关的数学类
double num = Math.log(2);  //比如求对数相关的操作
System.out.println(num);

int abs = Math.abs(-422);  //求其绝对值
System.out.println(abs);

double random = Math.random();  //生成一个[0,1)之间的随机数
System.out.println(random);     //下面会讲到有一个专门生成随机数的类

//Random
//生成随机数的类
Random random1 = new Random();
int i = random1.nextInt(2);//生成随机的整数.如果定义了范围，那就是[0,2)之间的数字
System.out.println(i);

//与之对应的还有等相关的
double v = random1.nextDouble(); //生成的是[0,1)之间的随机
System.out.println(v);
```

Integer类作为int的包装类，能存储的最大整型值为2^31-1 ，Long类最大为2^63-1，虽然比Integer类大了很多，但是也是有限的。如果想要表示更大的整数，不管是基本数据类型还是它们对应的包装类都无法实现。Java中提供了两个用于高精度计算的类：BigInteger和BigDecimal，这两个类包含的方法、提供的操作与基本类型及其对应的包装类相同，并提供了java.lang.Math的所有相关方法。BigInteger类实现了任意精度的整数运算，BigDecimal实现了任意精度的浮点数运算。

### BigInteger类

java.math包下的BigInteger可以表示不可变的任意精度的整数，主要是针对整型大数字的处理类，在运算中可以准确地表示任何大小的整数值，而不会丢失任何信息。
常用构造器：

```java
BigInteger(String val)：根据字符串构建BigInteger对象
//如下所示：
BigInteger bigInteger=new BigInteger("1234567891011121314151617181920");
System.out.println(bigInteger);//1234567891011121314151617181920
1234
```

常用方法：

```java
public BigInteger abs()：返回对象的绝对值
public BigInteger pow(int exponent) ：返回对象的平方数
public BigInteger add(BigInteger val) ：返回对象与另一个对象的和
public BigInteger subtract(BigInteger val) ：返回对象与另一个对象的差
public BigInteger multiply(BigInteger val)：返回对象与另一个对象的积
public BigInteger divide(BigInteger val) ：返回对象与另一个对象的商，整数相除只保留整数部分
public BigInteger remainder(BigInteger val) ：返回对象与另一个对象的余数
public int compareTo(BigInteger val)：比较对象与另一个对象的大小
12345678
```

### BigDecimal类

由于float类型和double在计算时很容易丢失精度。但在商业计算中，尤其是与计算货币值相关时，要求数字的精度很高，Java提供了java.math.BigDecimal类用来实现任意精度的浮点数运算，可以用它进行精确的货币计算。
常用构造器：

```java
BigDecimal(int val)：根据int类型的数字构建BigDecimal对象
BigDecimal(long val)：根据long类型的数字构建BigDecimal对象
BigDecimal(double val)：根据double类型的数字构建BigDecimal对象
BigDecimal(String val)：根据字符串构建BigDecimal对象
BigDecimal(BigInteger val)：根据BigInteger类型的数字构建BigDecimal对象
12345
```

常用方法：

```java
public BigDecimal abs()：返回对象的绝对值
public BigDecimal pow(int n) ：返回对象的平方数
public BigDecimal add(BigDecimal augend)  ：返回对象与另一个对象的和
public BigDecimal subtract(BigDecimal subtrahend)：返回对象与另一个对象的差
public BigDecimal multiply(BigDecimal multiplicand)：返回对象与另一个对象的积
public BigDecimal remainder(BigDecimal divisor)：返回对象与另一个对象的余数
public int compareTo(BigDecimal val)：比较对象与另一个对象的大小
1234567
```

BigDecimal可以用来做超大的浮点数的运算，比如+ - * /的运算，其中除法运算是最复杂的，因为商的位数可能有除不进的情况，此时需要考虑末位小数点的处理。

```java
//参数1：被除数   参数2：表示小数近似处理的模式(舍入模式)
public BigDecimal divide(BigDecimal divisor, int roundingMode) 
12
```

常用的舍入模式：

- BigDecimal.ROUND_UP：最后一位如果大于0，则向前进一位，正负数都如此；
- BigDecimal.ROUND_DOWN：最后一位不管是什么都会被舍弃；
- BigDecimal.ROUND_CEILING：如果是正数，按ROUND_UP处理，如果是负数，按照ROUND_DOWN处理；
  例如8.1->9； -8.1->-8；所以这种近似的结果都会>=实际值。
- BigDecimal.ROUND_FLOOR：跟BigDecimal_ROUND_CEILING相反，如果是正数，按ROUND_DOWN处理，如果是负数，按照ROUND_UP处理；
  例如8.1->8；-8.1->-9，这种处理的结果<=实际值
- BigDecimal.ROUND_HALF_UP：如果最后一位<5则舍弃，如果>=5， 向前进一位，即为四舍五入模式；
  例如：7.5->8；7.4->7；-7.5->-8
- BigDecimal.ROUND_HALF_DOWN：如果最后一位<=5则舍弃，如果>5，则向前进一位；
  例如：7.5->7；7.6->8；-7.5->-7
- BigDecimal.ROUND_HALF_EVEN ：如果倒数第二位是奇数，按照BigDecimal.ROUND_HALF_UP处理，如果是偶数，按照BigDecimal.ROUND_HALF_DOWN来处理。
  例如：7.5->8；8.5->8；7.4->7；-7.5->-8