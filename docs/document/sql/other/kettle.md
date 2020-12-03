# kettle工具学习教程

## 一、前言

> 对于学习kettle工具而言，我们首先应该了解下为什么要使用这个工具，在哪些场景或者是应用上要使用这个工具，比如说对业务数据进行抽取，分析，整理后的决策，以及建立数据仓库等方面，就需要用到kettle工具，当然，这个已经属于大数据的方面了。在这之前，应先了解数据仓库的概念，再来学习就方便得多了。

### 1、企业数据仓库模型

![image-20201129202238650](img\image-20201129202238650.png)

### 2、ETL简介

ETL(Extract-Transfrom-load 的缩写，即数据抽取，转换，装载的过程)，对于企业或者行业应用来说，我们会经常遇到各种数据的处理，转换，迁移，所以了解并掌握一种ETL工具的使用，必不可少，这里，我们要使用的工具就是kettle。

![image-20201129202907538](img\image-20201129202907538.png)

## 二、kettle的简单介绍

### 2、kettle的组成

* 勺子（Spoon.bat/ spoon,sh）:是一个图形化的界面，可以让我们用图形化的方式开发转换和作业。
* 煎锅（Pan.bat / pan.sh）: 利用Pan命令的形式调用Trans(转换)
* 厨房（kitchen.bat/kitchen.sh）：利用kitchen命令行调用job(作业)。
* 菜单（Carte.bat/Carte.sh）Carte是一个轻量级的Web容器，用于建立专用，远程的ETL Server.

> 现在主要先了解，等后面用到关于集群的时候，再深入学习使用Cart.bat这样的东西。

### 3、kettle的一些特点

![image-20201129184648687](img\image-20201129184648687.png)

### 4、下载链接

* https://sourceforge.net/projects/pentaho/files/?css-reload=1
* https://community.hitachivantara.com/docs/DOC-100985

> 可根据需要下载不同版本的工具

### 5、相关学习网址

* http://www.kettle.net.cn/
* https://community.hitachivantara.com/docs/DOC-100985

### 6、kettle的概念模型

![image-20201129203124462](img\image-20201129203124462.png)