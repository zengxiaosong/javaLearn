# 分页查询

## 一、数据库本身执行的分页

   ### 1、mysql对应的分页查询

> mysql执行分页的关键字   limit m(偏移量)，n(查询量)  ，注意的是，mysql中偏移量默认是0。

举例说明：

> select * from user limit 10,10      //偏移量为10，也就是从第11行数据开始查询，查询的数据总量为10条。

> select * from user limit 10  --->limit 0,10      //偏移量为零，从第一行开始查，查10条数据。

**对于分页查询，可以归结为下面的实例情况：**

> select * from user limit m, n  // m= (startPage-1) * pageSize   n=pageSize  // startPage表示当前正在查询的哪一页，值得注意的是，表达式是不能写在sql中的，只能写在java代码里面去。

**最后想说的是，在数据量大的时候，如果对偏移量有要求，尽量使用带偏移量的，在查询的时候，比较有优势。**

### 2、oracle对应的分页查询

> oracle 中对应的能实现分页查询的关键词是  **rownum**  表明的含义是行数。

举例说明：

> `select rownum rn,a.UPLOAD_STATE  from HIGHRISK a WHERE ROWNUM<=10`

![image-20201203170531356](img\image-20201203170531356.png)

实现分页查询：

> `SELECT * from (`
> `select rownum rn,a.UPLOAD_STATE  from HIGHRISK a WHERE ROWNUM<=m)  WHERE rn>=n`

对于这个分页，`m = startPage * pageSize     n = (startPage  - 1)* pageSize +1 。`

分页查询的另一个实例：

> `SELECT * from (`
> `select rownum rn,a.* from HIGHRISK a WHERE ROWNUM<=10)`
> `where rn BETWEEN m and n`

## 二、三方集成的分页操作