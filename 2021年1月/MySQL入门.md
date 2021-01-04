# MySQL 入门

本文写于 2020 年 1 月 4 日

## 数据库的作用与意义

**数据是程序的核心之一。**

数据库（Database / DB）的作用就是「储存」「读取」与「管理」数据。

有了数据库后，我们就可以直接查找数据。但并不是只有使用 MySQL、Redis……等数据库管理系统（Database Manage System / DBMS）才能使用数据库。

我们完全可以使用 txt 文件来记录数据：

```txt
学号,姓名,性别,专业
1,王二,女,计算机
2,张三,男,金融
```

当然，除了 txt，我们也可以用 excel，用 word 等等文件去存放数据。每当我们需要操作数据的时候，只需要**用程序对这些文件进行读写**就行了。

```js
// 伪代码
const context = readFile();
const newContext = deal(context);
writeFile(newContext);
```

但是这样太不方便了！

所以程序员创造出了各类数据库管理系统，例如本文所介绍的 MySQL。

```bash
# 例子来源：菜鸟教程
mysql> use RUNOOB;
Database changed

mysql> set names utf8;
Query OK, 0 rows affected (0.00 sec)

mysql> SELECT * FROM Websites;
+----+--------------+---------------------------+-------+---------+
| id | name         | url                       | alexa | country |
+----+--------------+---------------------------+-------+---------+
| 1  | Google       | https://www.google.cm/    | 1     | USA     |
| 2  | 淘宝          | https://www.taobao.com/   | 13    | CN      |
| 3  | 菜鸟教程      | http://www.runoob.com/    | 4689  | CN      |
| 4  | 微博          | http://weibo.com/         | 20    | CN      |
| 5  | Facebook     | https://www.facebook.com/ | 3     | USA     |
+----+--------------+---------------------------+-------+---------+
5 rows in set (0.01 sec)
```

可以看到，我们通过 SQL 的介入，免去了手动处理数据的读写，极大的方便了我们的编程。

## 数据库的分类

目前我们有两种数据库类型，分别是**关系型数据库**与**非关系型数据库**。

_目前可以暂且认为数据库就是一张张的表，类似于 Excel。_

### 关系型数据库（SQL）

MySQL、Oracle、Sql Server、SQL Lite 都是关系型数据库。

关系型数据库通过表与表之间 **「关系」**、行与列之间的 **「关系」** 来进行数据的储存。

### 非关系型数据库（NoSQL / Not Only SQL）

Redis、MongoDB 都属于非关系型数据库。

非关系型数据库是以 **「对象」** 进行存储的，根据对象的自身属性来决定数据。

## MySQL 的基本操作

略。

（完）
