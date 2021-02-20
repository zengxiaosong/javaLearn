#   redis学习记录

## 一、入门概述

### 1、redis是什么

> Redis :Remote  Dictionary Server (远程字典服务) 
>
> 是完全开源免费的，用C语言编写的，遵守BSD协议，是一个高性能的（Key/value） 分布式内存数据库，基于内存运行，并支持持久化的NOSql数据库，是当前最热门的NoSql数据库之一，也被人们称为数据结构服务器。
>
> redis与其他 Key -value 缓存产品相比具有以下三个特点：
>
> * Redis支持数据的持久化，可以将内存中的数据保持在磁盘中，重启的时候可以再加载进来。
> * Redis不仅仅支持简单的Key -Value 类型的数据，同时还提供list, set, zset, hash 等数据结构的存储。
> * Redis支持数据的备份，即master-slave模式的数据备份。

### 2、能干什么？

* 内存存储和持久化：redis支持异步将内存中的数据写到硬盘上，同时不影响继续服务。（比如：取最新N个数据的操作，比如可以将最新的10条评论的ID放在Redis的list集合中）
* 模拟类似于HttpSession这种需要设定过期时间的功能
* 发布，订阅消息系统
* 定时器，计数器

### 3、去哪里下载与学习

* http://redis.io/
* http://www.redis.cn/

> 上面的这个是官网文档，全英文的，下面是中文的官网文档。

![image-20201221153245014](img\image-20201221153245014.png)

### 4、下载安装

#### 1）window安装

> 我在做这个的时候基本是2020年末了，目前redis的官网已经没有去维护redis的windows版本了。因此如果想要下载的话，我这里推荐两个github的地址，供大家去下载使用。
>
> * https://github.com/tporadowski/redis/releases
> * https://github.com/microsoftarchive/redis/releases

下载压缩包进行解压后如下图所示:

![image-20201222134747980](img\image-20201222134747980.png)

首先运行` redis-server.exe` 打开redis的服务端，然后你就会得到下面的窗口：

![image-20201222134942970](img\image-20201222134942970.png)

这里比较重要的是端口号，这是redis的默认端口号，也是后面我们操作的时候需要记住的。然后打开` redis-cli` 你就会得到下面的窗口：（这里记住的是：前面的服务端窗口是不能关闭的）这个客户端是redis自带的客户端。

![image-20201222135220791](img\image-20201222135220791.png)

当执行ping命令时，得到返回的PONG时，就表示网络是能够连通的。接下来就能做操作了，比如` set key value`,以及获取值：` get value`。

![image-20201222135547160](img\image-20201222135547160.png)

> 当然，这是本机的操作，那如果说我们想远程操控下linux机器上的redis,甚至说不用他自己的Client，而是另外用客户端呢？具体使用哪种客户端，这里我在github上找了一个QuickRedis,把下载链接推荐出来：
>
> * https://gitee.com/quick123official/quick_redis_blog/releases

使用该客户端不仅能够连接本地的` redis-server` 还能连接远程的` redis-server`。

#### 2）linux安装

> 针对于linux的安装，由于本地电脑的差异，这里使用云服务器linux进行安装。这里我使用的腾讯云服务器。
>
> 然后使用xShell进行远程连接服务器。

首先到github上下载我们所需要的文件到本地。然后将该文件上传到我们的linux服务器上的` /opt`目录下。这里顺便说明下` /opt目录下是即用即得，当然，也可以随时删除，是不会对系统有影响的`。

当我们将文件下载到本地的时候，如何将文件传上到服务器呢？执行下列命令：

> rz:   执行后发现 commend not found 
>
> 说明 lrzsz（文件上传下载的工具）没有安装，执行 yum  -y install lrzsz命令进行安装
>
> 执行后执行  rpm -qa lrzsz 查看是否已经安装。
>
> 然后执行  rz -y 进行文件的上传。
>
> tar -zxvf redis-5.0.10.tar.gz  进行解压。
>
> 解压后需要注意的是，需要先执行 rpm -q  gcc查看linux是否安装了gcc编译器，如果没有进行安装，则是需要先安装gcc的。
>
> 安装完成后执行make命令，如果命令出现 gcc 命令未找到，就说明gcc未安装成功。
>
> 在执行make命令的时候，如果出现 jemallo.h文件没找到之类的错误，说明在上次安装的时候留下了残存的文件。先执行make distclean后再执行 make命令。出现下面的界面时，就说明redis已经安装成功了。

![image-20201223123744845](img\image-20201223123744845.png)

> 现在执行辅助命令 make install 

![image-20201223123859030](img\image-20201223123859030.png)

> 到这里，redis就基本装好了。

### 5、一个简单的helloWorld

> 按照上面的步骤，我们就已经在云服务器上成功的安装上了redis。那么现在就来简单的使用了，到哪里去用呢？
>
> **/usr: 这是一个非常重要的目录，用户很多的应用程序和文件都在这个目录下，类似于windows下的program file目录。**
>
> 执行命令 `cd   /usr/local/bin`   你就能看到下面的图：

![image-20201223124600011](img\image-20201223124600011.png)

现在，我们要做自己的配置，打开redis不使用默认的配置文件。

* 在根目录下创建文件夹  mkdir  myRdis
* 复制配置文件到myRedis下： cp redis.conf   /myRedis
* 执行命令 vim redis.conf 对文件进行编辑
* 将此处修改成后台进程。

![image-20201223125909464](img\image-20201223125909464.png)

* :wq  退出保存。
* ps  -ef|grep redis: 查看后台是否启动了该服务。
* redis-server /myRedis/redis.conf 启动我们的redis服务
* redis-cli -p 6379  启动客户端
* 执行ping  查看是否成功启动，如果返回pong，就说明成功了。
* 如果需要关闭的话，就执行shutdown  然后exit。

### 6、redis的一些其他知识

* redi是一个单进程，单进程模型来处理客户端的请求，对读写事件的响应是通过对epoll函数的包装来做到的，redis的实际处理速度完全依靠主进程的执行效率。Epoll是linux内核为处理大批量文件描述而做了改进的epoll。是linux下多路复用IO接口的select/poll的增强版本，它能显著提高程序在大量并发连接中只有少量活跃的情况下提高CPU 的利用率。
* 默认是16个数据库。类似数组从0开始，初始默认使用零号库
* Select命令切换数据库
* Dbsize查看当前数据库中的key的数量
* FlushDB:清楚当前库
* FlushAll:清楚所有库
* 16个库统一密码管理
* 索引都是从0开始
* 默认端口是6379

### 7、远程访问

> 这里我们以腾讯云的轻量应用服务器为例，进行下面解释：

* 本地cmd上 ping  服务器的ip地址，如果没问题，说明服务器是能够连接成功的

* 使用命令 telnet  ip port  查看redis的接口是否可用，如果可用，就说明能行，不可用进入下一步

* 查看redis.conf:  将bind 127.0.0.1  注释掉：这样就能让外网进行访问 并将protect-mode设置为no.

* 执行到这里的时候，很多网上说要把防火墙关闭，我认为不是很好的建议，具体来讲应该是将我们需要访问的端口号加入到防火墙中，以此保障安全性。

  ![image-20201223153856504](img\image-20201223153856504.png)

  添加安全组规则，或者是在腾讯云轻量服务器中是防火墙规则。

* 首先，查看下防火墙是否已经开启 firewall-cmd --state 

* 如果没有开启就执行命令： systemctl start firewalld     对应的关闭防火墙命令为：systemctl stop firewalld

* netstat -tunlp   :查看下我们的端口开放的状态

* firewall-cmd --permanent --zone=public --add-port=`port`/tcp  :永久的开启我们的端口号，让防火墙能够访问。firewall-cmd --zone=public --remove-port=8080/tcp --permanent：这是对应的关闭命令

* firewall-cmd --reload  ：最后重新加载配置，让上面我们添加的生效。

* firewall-cmd --permanent --zone=public --list-ports ：查看开启的端口列表

* firewall-cmd --permanent --zone=public --query-port=8080/tcp ：查看指定的端口列表

* systemctl start firewalld.service：重新打开防火墙。

*  telnet  ip port  ：再次执行此命令，发现能够访问，就没有问题了。

## 二、redis的基本数据类型

### 1、基本概述

> 首先可以看到官方给的一个关于基本数据类型的一个基本说明：

![image-20201224102626359](img\image-20201224102626359.png)

对于我们常见的，也就是最基本的五种基本数据类型：

* String
* list
* set
* hash
* Zset(Sorted set)

大概分别说明下这几种：

* String（字符串）

  > string是redis最基本的类型，可以理解为和Memcached一模一样的类型，一个key对应一个value。String类型是二进制安全的，意思是redis的String可以包含任何数据。比如jpg图片或者序列化的对象，String类型是Redis最基本的数据类型，一个redis中字符串value最多可以是512M。

* Hash（哈希，类似于java中的Map）

  > Redis hash是一个键值对集合，Redis hash 是一个String类型的field和value的映射表，hash特别适合用于存储对象，类似JAVA里面的Map<String,Object>。

* List（列表）

  >实际上底层是一个链表。Redis列表是简单的字符串列表，按照插入顺序排序，你可以添加一个元素到列表的头部或者尾部。

* set(集合)

  > redis的set是String类型的无序集合，他是通过HashTable实现的。

* Zset( sorted set: 有序集合)

  > Redis  zset 和 set 一样也是string类型元素的集合，且不允许重复的成员。
  >
  > 不同的是每个元素都会关联一个double类型的分数。
  >
  > redis正是通过分数来为集合中的成员进行从小到大的排序。zset的成员是唯一的，但是分数（score）却可以重复。类似于hash得到的hash值是可以重复的。

### 2、redis命令大全

> http://redisdoc.com/

### 3、Key相关的常见命令

* `EXIST key`:   查找某个键是否存在，如果存在，返回（integer 1）不存在，返回（integer 0）

  ![image-20201224163754597](img\image-20201224163754597.png)

* `keys   *`   :  查找所有的key。返回的是一个string数组。

![image-20201224164026593](img\image-20201224164026593.png)

* `move  key  db` ：将某个键值移动到对应的库当中，成功后返回（integer 1）否则返回0。

![image-20201224164336055](img\image-20201224164336055.png)

* ` expire  key  second` ：为给定的key设置过期时间。
* ` ttl key`   :  查看还有多少时间过期， -1表示永远不会过期，-2表示已经过期。

![image-20201225153431588](E:\myStudy\docisfy\javaLearn\docs\document\framework\NoSql\img\image-20201225153431588.png)

> 值得注意的是，一旦设置了过期时间，那么当该键过期后，就不会在内存中存在该值，也不会在持久化文件中存在。

* ` del key`  : 当key存在的时候，删除该key值，并且饭后值integer 1,否则的话，返回值 integer 0。

* ` type key` : 查看key是属于什么类型的？也就是上面说的String，list，set等等。

### 4、String字符串

> String 类型单值的，下面来看下相关操作的案例。

* set /  get   /  del    / `append(返回的是value的长度)`   /   `strlen（返回的是value的长度）`

![image-20201225162641939](img\image-20201225162641939.png)

* `Incr  /  decr   /  incrby   /  decrby`   注意的是，一定要是数字才能进行加减。

![image-20201228135505840](img\image-20201228135505840.png)

* setrange   /    getrange   :   setrange:设置指定区间范围的值，如 setrange key sart XXX。getrange:获取指定区间范围的值，如 getrange key start end。

![image-20201228140811752](img\image-20201228140811752.png)

* setex (set with expire) 设置键的时候就带期限，  / setnx (set if not exist)  当不存在的时候才进行设置。

![image-20201228141958185](img\image-20201228141958185.png)

* mset （多值设置）  /   mget  （多值获取）  /  msetnx(当多个都不存在时进行执行，具有原子操作，一个不成功都不成功)

![image-20201228143036003](img\image-20201228143036003.png)

### 5、list单键多值

> list底层是属于链表的，因此

*  lpush  /   rpush   /   lrange  :    lpush 是指头插法，逐渐向左边做插入操作。rpush是尾插法，向右边做插入操作。lrange和前面介绍的用法是相同的。

![image-20201228174557400](img\image-20201228174557400.png)

* lpop   /    rpop   :  lpop是左边出栈，rpop是右边出栈，注意的是，每次只能一个元素进行出栈，并且出栈后，栈里面是没有该元素的。

![image-20201228174801385](img\image-20201228174801385.png)

* lindex  ：按照索引下标获得元素（从上往下）

![image-20201228174939203](img\image-20201228174939203.png)

* llen  ：求该list键的的元素长度。

![image-20201228175109921](img\image-20201228175109921.png)

*  lrem  key 删掉n个value：  对于列表中，可以具备重复的值，因此，可以进行多次的删除。

![image-20201228175415604](img\image-20201228175415604.png)

> 从上面的例子中我们可以看到，在list1中并没有9个5，但是该命令依然能够成功，并且将原本已经存在的4个5删完全了。

* ltrim key index  end  :  截取指定范围的值后再赋值给key。

![image-20201228180057636](img\image-20201228180057636.png)

> 值得注意的是，在上面描述的过程中，截取的value是包含了end部分的。

* rpoplpush 源列表  目的列表 ，指的是将源列表中的元素弹出来放到目的列表中。

![image-20201228180420073](img\image-20201228180420073.png)

* lset key index value : 对列表中对应位置的数进行修改。

![image-20201228180714306](img\image-20201228180714306.png)

* linsert key before/after  value1   value2   :在列表中某个部分元素的前面或者是后面进行值的插入。需要说明的是：因为值可以重复，所以在进行插入的时候，只会在找到第一个该值的时候进行插入，然后就返回了，并不会多次插入。

![image-20201228180926580](img\image-20201228180926580.png)

> 总结：对于list，如果键不存在，就会创建，如果键存在，就会新增。如果键中所有的值都被移除了，键也就消失了。链表的操作无论是头和尾，效率都是十分高的，但是如果对中间值进行操作，效率就比较低下。

### 6、set集合

> set集合作为不可重复的键值对。

* sadd   set   value      # 向集合中添加值，并且，值是不会重复的。

![image-20210105141318886](img\image-20210105141318886.png)

* smember  set    #  查看指定set的所有的值。

![image-20210105143841707](img\image-20210105143841707.png)

* sismember set value     # 判断某个值是否在该集合内。

![image-20210105144639147](img\image-20210105144639147.png)

* scard  set   #  查看集合里面的数目

![image-20210105151319735](img\image-20210105151319735.png)

* srem  set  value   #  指定移除set集合中的元素。

![image-20210105151832332](img\image-20210105151832332.png)

* SRANDMEMBER myset1  count    # 随机获取指定集合中的count个数的值。获取的值不具备顺序。

![image-20210105153047599](img\image-20210105153047599.png)

* SPOP  set     # 随机删除集合中的某个元素，并且返回该元素。如果集合为空，没有元素了，就应返回null。

![image-20210105153923259](img\image-20210105153923259.png)

* smove  source  destin  value     #   将source集合中的某个value移动到destin集合中。

![image-20210105154744413](img\image-20210105154744413.png)

* sdiff  set1  set2      #   求set1集合与set2集合之间的差集，也就是说set1-set2（set1中独有的）。

![image-20210105160306376](E:\myStudy\docisfy\javaLearn\docs\document\framework\NoSql\img\image-20210105160306376.png)

* sinter   set1   set2   #    求两个集合之间的交集操作。

![image-20210105160456160](E:\myStudy\docisfy\javaLearn\docs\document\framework\NoSql\img\image-20210105160456160.png)

* SUNION set1 set2    #  求两个集合的并集操作。

![image-20210105160637943](img\image-20210105160637943.png)

### 7、hash集合

> map集合，具体来讲是key-map，对应设置键key。对应的值是一个map集合，本质上和 String没有太大的区别，但是value本质上还是属于 key-value类型的。

对应相关的命令如下：

* hset key field value      #    向该hash中添加键值对。

![image-20210105162900434](img\image-20210105162900434.png)

* hget  key  field      #    获取该hash中的某个键值对。

![image-20210105163334357](img\image-20210105163334357.png)

* hgetall   key    #  获取hash集合中的所有字段。

![image-20210105164326407](img\image-20210105164326407.png)

* hmset    key      field   value   field   value    #   向该hash中添加多个键值对。

![image-20210105164744530](img\image-20210105164744530.png)

* hmget    key    field     field     #  获取hash表中的多个字段。

![image-20210105165134321](img\image-20210105165134321.png)

* hdel    key   field    field   #   删除hash中指定的key的field ,对应删除相关的键值对。

![image-20210105165823722](img\image-20210105165823722.png)

* hlen  key    #  获取指定hash表的长度。

![image-20210105171050565](img\image-20210105171050565.png)

* HEXISTS key  field     #  判断hash表中某个字段是否存在，如果存在返回1, 不存在则返回0。

![image-20210105171621719](img\image-20210105171621719.png)

* hkeys    key     #   获取hash表中的所有键，（仅仅只是获取键，而不是获取值）

![image-20210105171935848](img\image-20210105171935848.png)

* HVALS   key      #  获取hash表中所有的值

![image-20210105172307687](img\image-20210105172307687.png)

* HINCRBY  key   field  increment : 为指定的key的field加上增量 increment   ( 值得注意的是：增量只能针对于integer类型的 ) 并返回对应增量之后的值。

![image-20210203152719855](img\image-20210203152719855.png)

### 8、zset有序集合

> zset 是一种特殊的 set（sorted set），在保存 value 的时候，**为每个 value 多保存了一个 score 信息**。根据 score 
>
> 信息，可以进行排序。 这个评分（score）被用来按照从**最低分到最高分的方式**排序集合中的成员。集合的成员是唯一的，但是评分可 以是重复了 

下面看基本的命令：

![image-20210203165322941](img\image-20210203165322941.png)

* ZADD  KEY  [score  value]    : 向有序集合key中添加带有分数的值，可以多次添加。

![image-20210203154612902](img\image-20210203154612902.png)

* ZSCORE    key   member    :  返回指定值的分数

![image-20210203155223871](img\image-20210203155223871.png)

* ZRANGE   KEY   STRAT   END  [WITHSCORES] :   返回一定区间的值，可以选择是否和score一起返回。

![image-20210203160451462](img\image-20210203160451462.png)

* ZRANGEBYSCORE  key  min   max  [withscores]  :   取分数指定区间的数据，从小到大返回

![image-20210203161706175](img\image-20210203161706175.png)

* ZREVRANGESCORE  key   max   min  [withscores]  :   取分数指定区间的数据，从大到小返回

![image-20210203163212977](img\image-20210203163212977.png)

* ZCARD   KEY    :    返回有序集合key中具有值的数量

![image-20210203164217363](img\image-20210203164217363.png)

* ZCOUNT KEY  MIN   MAX   :   统计分数区间元素的个数

![image-20210203164401022](img\image-20210203164401022.png)

* ZREM  KEY  MEMBER  : 删除某个有序集合中的值   （ 不过的是，需要获取其值）

![image-20210203164610835](img\image-20210203164610835.png)

* ZRANK  KEY MEMBER  :   返回该元素在集合中的排名，排名从0开始。

![image-20210203164910712](img\image-20210203164910712.png)

* ZINCRBY  KEY  INCRMENT  MEMBER  : 为元素的score加上增量

![image-20210203165206705](img\image-20210203165206705.png)

## 三、配置文件详解

### 1、include

> `include   path/to/local.conf`

> `include  path/to/other.conf`

可以将公共的配置放入到一个公共的配置文件中，然后通过子配置文件引入父配置文件中的内容！ 将配置按照模块分开

### 2、network

| 属性           | 含义                               | 备注                                                         |
| :------------- | ---------------------------------- | ------------------------------------------------------------ |
| bind           | 限定访问的主机地址                 | 如果没有bind,就表示任意的ip地址都可以进行访问，当然， 在生产环境下，是需要限定到自己的应用服务器的ip地址 |
| protected-mode | 安全防护模式（yes / no）           | 如果没有指定bind指令，也没有设置密码，就开启安全模式，此时就只能允许本机访问。 |
| port           | 端口号                             | 默认是637                                                    |
| tcp-backlog    | 网络连接过程中，某种状态的队列长度 | redis是单线程的，指定高并发访问时排队队列的长度，超过后，就呈现阻塞状态，可以理解是一个请求到达后至到接受进程处理前的队 列长度。（一般情况下是运维根据集群性能调控）高并发情况下，此值可以适当调高。 |
| timeout        | 超时时间                           | 默认永不超时，也就是默认下是 0  （也就是说当客户端闲置后这个连接什么时候关闭，一般情况下是不会关闭的） |
| tcp-keepalive  | 对客户端的心跳检测间隔时间         | 为0表示不检测，也就是短连接，也就是说，服务端会在间隔时间发送”侦探”信号观察客户端是否还连接，以此保持连接状态。 |
|                |                                    |                                                              |

### 3、general

| 属性            | 含义                   | 备注                                                         |
| --------------- | ---------------------- | ------------------------------------------------------------ |
| daemonize       | 是否为守护进程模式运行 | 设置为yes后，该进程就能在后台进行运行                        |
| pidfile         | 进程id文件保存的路径   | 配置 PID 文件路径，当 redis 作为守护进程运行的时候，它会把 pid 默认写到 /var/redis/run/redis_6379.pid 文件里面。 |
| loglevel        | 定义日志的级别         | debug（记录大量日志信息，适用于开发、测试阶段）                            verbose（较多日志信息）                                                                                   notice（适量日志信息，使用于生产环境）                                                      warning（仅有部分重要、关键信息才会被记录） |
| logfile         | 日志文件的位置         | 当指定为空字符串时，为标准输出，如果 redis 以守护进程模式运行， 那么日志将会输出到/dev/null，当然，指定了就会记录到指定的位置上去。 |
| syslog-enabled  | 是否记录到系统日志     | 要想把日志记录到系统日志服务中，就把它改成 yes               |
| syslog-ident    | 设置系统日志的 ID      |                                                              |
| syslog-facility | 指定系统日志设置       | 必须是 USER 或者是 LOCAL0-LOCAL7 之间的值                    |
| databases       | 设置数据库数量         | 一般情况下默认是16                                           |



### 4、其他

| 属性                        | 含义             | 备注                                                         |
| --------------------------- | ---------------- | ------------------------------------------------------------ |
| requirepass                 | 设置密码         | 设置密码后，客户端打开的时候是需要先输入密码的               |
| maxclients                  | 最大连接数       | 对于一个服务端，不同的客户端产生的线程数                     |
| maxmemory                   | 最大占用多大内存 | 一旦占用内存超限，就开始根据**缓存清理策略**移除数据如果 Redis 无法根据移除规则来移除内存中的数据，或者设置了“不允许移除”，那么 Redis 则会针对那些需要申请内存的指令返回错误信息，比如SET、LPUSH 等 |
| maxmemory-policy noeviction | 缓存清理策略     | （1）volatile-lru：使用 LRU 算法移除 key，只对设置了过期时间的键，   （2）allkeys-lru：使用 LRU 算法移除 key，                                                                   （3）volatile-random：在过期集合中移除随机的 key，只对设置了过期时间的键 ，                                                                                                                            （4）allkeys-random：移除随机的 key                                                                    （5）volatile-ttl：移除那些 TTL 值最小的 key，即那些最近要过期的 key （6）noeviction：不进行移除。针对写操作，只是返回错误信息。 |
| maxmemory-samples           | 样本数           | 样本数越小，准确率越低，但是性能越好。LRU 算法和最小 TTL 算 法都并非是精确的算法，而是估算值，所以你可以设置样本的大小。一般设置 3 到 7 的数字。 |

## 四、持久化策略

### 1、概述

> redis主要是在工作内存当中，内存本身就不是一个持久化设备，断电后数据会清空，所以redis在工作过程中，如果发生了意外断电事故，如何尽可能减少数据的丢失。

> redis提供不同级别的持久化方式：
>
> * RDB  (redis database) 持久化方式能够在指定的时间间隔对你的数据进行快照存储。
> * AOF(append only file) 持久化方式记录每次对服务器的写操作，当服务器重启的时候会重新执行这些命令来恢复原始的数据，AOF命令以redis协议追加保存每次写的操作到文件末尾，redis还能对AOF文件进行后台重写，使得AOF文件的体积不至于过大。
> * 如果你只希望你的数据在服务器运行的时候存在，你也可以不使用任何持久化方式。
> * 你也可以同时开启两种持久化方式，在这种情况下，当redis重启的时候会优先载入AOF文件来恢复原始的数据，因为在通常情况下AOF文件保存的数据集要比RDB文件保存的数据集要完整。
> * 最重要的事情是要了解RDB和AOF持久化方式的不同。

### 2、RDB持久化



1）简述

RDB:  在指定的时间间隔内将内存中的数据集快照写入到磁盘，也就是行话讲的snapshot快照，它恢复时是将快照文件直接读到内存里。

工作机制：每隔一段时间，就把内存中的数据保存到硬盘上的指定文件中。默认情况下是开启的。

redis会单独创建（fork）一个子进程进行持久化，会先将数据写入到一个临时的文件中，待持久化过程都结束了，再用这个临时文件替换上次持久化好的文件。整个过程中，主进程是不会进行任何的IO操作的，这就确保了极高的性能，**如果需要进行大规模数据的恢复，且对于数据恢复的完整性不是非常敏感，那RDB方式要比AOF方式更加高效。但是RDB的缺点是最后一次持久化后的数据可能丢失。**



2）RDB保存策略

save    900    1    900秒内如果至少有一个key值发生变化，则保存。

save      300    10   300秒内如果至少有10个key值发生变化，则保存。

save  60   10000  60秒内如果至少有10000个key值发生变化，则保存。

save  ""  就表示禁用RDB模式。



3）RDB常用的属性配置

| 属性                        | 含义                                                | 备注                                                         |
| --------------------------- | --------------------------------------------------- | ------------------------------------------------------------ |
| save                        | 保存策略                                            |                                                              |
| dbfilename                  | RDB快照文件名                                       |                                                              |
| dir                         | RDB快照保存的目录                                   | 必须是一个目录，不能是文件名。最好改为固定目录。默认为./  代表执行redis-server命令的当前目录。 |
| stop-writes-on-bgsave-error | 是否在备份出错时，继续接受写 操作                   | 如果用户开启了 RDB 快照功能，那么在 redis 持久化数据 到磁盘时如果出现失败，默认情况下，redis 会停止接受所有的写请求 |
| rdbcompression              | 对于存储到磁盘中的快照，可以 设置是否进行压缩存储。 | 如果是的话，redis 会采用 LZF 算法进行压缩。如果你 不想消耗 CPU 来进行压缩的话，可以设置为关闭此功能，但是存储在磁盘上的快照会 比较大。 |
| rdbchecksum                 | 是否进行数据校验                                    | 在存储快照后，我们还可以让 redis 使用 CRC64 算法来进行数据校验，但是这样做会增加大约 10%的性能消耗，如果希望获取到最大的性能提升，可以关闭此功能。 |



4）RDB数据丢失的情况

两次保存的时间间隔内，服务器宕机，或者发生断电问题。

![image-20210205161211595](img\image-20210205161211595.png)



5）RDB的触发

* 基于自动保存的策略
* 执行save, 或者bgsave命令！ 执行时，是阻塞状态。
* 执行 flushdb 命令，也会产生 dump.rdb，但里面是空的，没有意义。
* 当执行 shutdown 命令时，也会主动地备份数据。

6）RDB的优缺点

* 优点：RDB是一个非常紧凑的文件，