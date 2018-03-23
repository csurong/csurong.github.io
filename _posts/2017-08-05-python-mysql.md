---
layout: post
title:  "python直接操作MySQL"
date: 2017-08-05 22:10:03 +0800
categories: python
tags:  MySQL python
author: csurong
---

* content
{:toc}

今天封装了一下pymysql模块对MySQL的一些操作，顺便回顾一下SQL




## 语法

### 字段类型

+ 数字：int, decimal(浮点数) 使用：decimal(总共位数，小数位数)
+ 字符串：char（自动加空格到指定长度）,varchar（只有最大值，长度可变）,text（大文本）     
+ 日期：datetime（年月日 时分秒） date（年月日）
+ 布尔：bit

### 字段约束：

+ primary key   主键（不能重复，唯一标识，查找非常快）
+ not null      非空
+ unique        唯一（不能重复）
+ default       默认（建的时候就设置了，不给就是 x）
+ foreign key   外键 

### 数据库操作：

+ 数据库名随便起，字符集选择 utf-8，排序规则一般选通用
+ 表对应的既是 E-R 模型中的 E，即实体
+ 对表的增删改操作 可以见参考文档 图形界面操作 id不允许空值，且让它自动递增
+ 不要做物理删除，只做逻辑删除。给个 isDelete 栏，默认为 0 没有删除
+ 数据库操作：
        + 创建数据库: `create database 数据库名 charset=utf8;`
        + 删除数据库：d`rop database 数据库名`
        + 切换数据库：`use 数据库名`
        + 查看当前数据库：`select database()`
        + 显示所有的数据库：`show databases；`

### 表操作：

+ 查看所有的表：show tables;
+ 创建表
```sql
create table 表名(列及类型)；
如：
create table students(
id int auto_increment primary key not null,
name varchar(10) not null,
gender bit default 1,
birthday datetime  //最后不要写逗号，报错
);
```
+ 查看表：`desc 表名`;
    + 修改表 `alter table 表名 add|change|drop 列名 类型`;  此操作没必要，一开始就该做好表设计
    + 删除表
    + 表名重命名

### 数据操作

增删改查
+ 查询：`select * from 表名`
+ 增加数据：`insert into 表名 values(有多少字段就要插多少值，id也要随便写一个占位)` 
+ 修改：`update students(表名) set birthday='1999-1-1'（数据，可用逗号隔开） where id=2（条件）; ` 
+ 删除数据：`delete from 表名 where id=5;` 物理删除

### 总结

+ 创建数据库：`create database name1 charset=utf-8`
+ 创建表：`create table name2 (字段 类型 约数) id int auto_increment primary key not null`
+ 增加数据：`insert into name2 values()`
+ 删除数据：`delete from name2 where id=2`
+ 修改数据：`update name2 set 字段 where id=2`

## 查询

### 基本查询

+ `select * from 表名;`
+ `select name,gender from students;` 查询多个列

消除重复行；
    + `select distinct gender from students;`
    + `select distinct id,gender from students;` id和gender都不重复，只要有一个不重复就都返回

### where查询

`select * from 表名 where 条件;`

+ 比较运算符：= > >= !=
+ 逻辑运算符：and or not
+ 模糊查询(字符匹配)：
    + like：select * from students where sname like '黄%';
    + %：%表示任意多个字符
    + \_: 下划线表示任意一个字符

+ 范围查询；
    + `in: select * from students where id in(1,3,8); `查询编号是1或3或8的学生
    + `between...and...:select * from students where id between 3 and 8;` 在一个连续的范围内

+ 空判断：select * from students where birthday is null 或者 is not null; 注意null和‘’是不同的

+ 聚合：为了快速得到统计数据，提供了5个聚合函数
    + count(\*) `select count(*) from students;` 统计行数
    + max(age)  `select max(id) from students where gender=0;` 最大值
    + min(age)  `select min(id) from students where isdelete=0;` 最小值
    + sum(num)  `select sum(id) from students where gender=1;` 和
    + avg(num)  `select avg(id) from students where isdelete=0 and gender=0;` 平均值

+ 分组：按照字段分组（男生一队，女生一队）
    + 先按字段分组，再按条件 select
    + 一般分组都是为了做聚合
```sql
select gender,count(*)
from students
group by gender;
```
+ 分组后的数据删选：
```sql
select gender,count(*)
from students
group by gender
having gender=1;  // having后面的条件运算符与where的相同
```

+ 对比where与having
    + where是对from后面指定的表进行数据筛选，属于对原始数据的筛选
    + having是对group by的结果进行筛选
+ 排序：order by `select * from subject where isdelete=0 order by stitle;`
+ 分页：获取部分行 `select * from 表名 limit start,count` 从 start 开始，获取count条数据
+ 顺序：
```sql
select distinct *
from 表名
where ....
group by ... having ...
order by ...
limit star,count
```

## 视图、事务、索引

### 建立关系表：

+ 约束完成检查（建立了关系字段之后，为了保证数据的有效性而添加的一种约束）
+ 外键的级联操作：在删除students表的数据时，如果这个id值在scores中已经存在，则会抛异常。鸡肋功能，用逻辑删除可以避免这种情况
```sql
create table scores(
id int primary key auto_increment,
stuid int, // 这里已经创建好了
subid int,
score decimal(5,2),
foreign key(stuid) references students(id), // 外键是 stuid，引用的是 students 表中的 id 。此时插入或者修改数据时，如果stuid的值在students表中不存在则会报错
foreign key(subid) references subjects(id)
);
```

### 连接查询：

```sql
select students.name, subjects.title, scores.score
from scores
inner join students on scores.stuid=student.id
inner join subjects on scores.subid=subjects.id
```

+ 注意 join 和 on 关键字
+ 谁 join 谁没关系，关键是要建立他们的关系

```sql
select students.name, subjects.title, scores.score
from students
inner join scores on scores.stuid=students.id
inner join subjects on scores.subid=subjects.id; //和上面的结果一样的
```
+ inner left right
    + inner 是两个表都能匹配上的出现
    + left 是两个表都能匹配上的出现，左表特有的也出现，未对应的数据使用null填充
    + right 是两个表都能匹配上的出现，右表特有的也出现

+ 例子；
```sql
no.1
需求：查询科目的名称、平均分
分析：
    + 需要 subjects.title 和 scores.score 信息
    + 数据来源于两个表，那就要考虑连接问题，那就是 subjects.id=scores.subid
    + 平均分，那就是聚合函数 avg
    + 设计到聚合函数，那就要考虑要不要用 group by
    + 本题需要对科目分组之后求平均分，所以要用 group by
    + 据题意，根据科目的名字进行分组 group by subjects.title

select subjects.title,avg(scores.score)
from scores
inner join subjects on scores.subid=subjects.id
group by subjects.title;


no.2
需求：查询未删除科目的名称、最高分、平均分
分析：
    + 条件：where subjects.isdelete=0
    + 要筛选的 subjects.title max(scores.score) avg(scores.score)
    + 需要连接 subjects.id=scores.subid
    + 也需要分组 group by subjects.title

select subjects.title,max(scores.score),avg(scores.score)
from scores
inner join subjects on subjects.id=scores.subid
where subjects.isdelete=0 //where 要在 group by 的前面
group by subjects.title;
```

### 视图

为了实现select的多次调用，实现对查询的封装

### 事务

+ 当一个业务逻辑需要多个sql完成时，如果其中某条sql语句出错，则希望整个操作都退回
+ 使用事务可以完成退回的功能，保证业务逻辑的正确性
+ 一般用不到

## mysql与python交互

+ 我用的模块是 pymysql [文档](https://github.com/PyMySQL/PyMySQL#documentation)
+ connection对象：
    + 建立连接 conn=connect(参数列表)
    + 对象的方法：
        + commit()事务，修改需要提交才会生效
        + rollback()事务，放弃之前的操作
        + close() 关闭连接
        + cursor() 返回Cursor()对象，用于执行sql语句
+ Cursor对象：
    + 执行 sql 语句，增删改查等等
    + 执行完要 close()
    + 创建对象：cursor1=con.cursor()
    + 方法：
        + execute(operation [, parameters ])执行 sql 语句，返回受影响的行数
        + fetchone()执行查询语句时，获取查询结果集的第一个行数据，返回一个元组
        + fetchall()执行查询时，获取结果集的所有行，一行构成一个元组，再将这些元组装入一个元组返回
```python
# 插入一条数据（增删改，就是把下面的 sql 语句改掉就行了）
import pymysql
def main():
    try:
        conn = pymysql.connect(host='localhost', port=3306, db='csurong', user='root', password='rong', charset='utf8') # 建立连接
        cs1 = conn.cursor() #创建 cursor 对象
        count = cs1.execute("insert into students(name) values('rl')") # 执行 sql 语句
        print(count)
        conn.commit() # 提交数据库修改
        cs1.close() # 关闭 cursor 对象
        conn.close() # 关闭连接

    except Exception:
        print('sth wrong')

```

### sql注入

用户添加信息，不按套路出牌。解决方案是 sql 语句参数化
```python
name = input('请输入学生姓名： ')
conn = pymysql.connect(host='localhost', port=3306, db='csurong', user='root', passwd='rong', charset='utf8')
cs1 = conn.cursor() //下面这个也是添加数据的一种方式，不过我没记，文档里有
sql = "insert into students(name) values(%s)" #If args is a list or tuple, %s can be used as a placeholder in the query. If args is a dict, %(name)s can be used as a placeholder in the query
count = cs1.execute(sql, [name]) # 第二个参数是一个列表。sql中插入多个列的话就要将多次输入构造列表
print(count)
conn.commit()
cs1.close()
conn.close()
```

### 查询

```python
import MySQLdb
try:
    conn=MySQLdb.connect(host='localhost',port=3306,db='test1',user='root',passwd='mysql',charset='utf8')
    cur=conn.cursor()
    cur.execute('select * from students where id=7') //先 execute 再执行其它方法
    result=cur.fetchone()
    print result
    cur.close()
    conn.close()
except Exception,e:
    print e.message
```

### 封装

```python
# 封装成一个模块
class PymysqlHelper:
    def __init__(self, host, port, db, user, password, charset='utf-8'):
        self.host = host
        self.port = port
        self.db = db
        self.user = user
        self.password = password
        self.charset = charset

    def open(self):
        self.conn = pymysql.connect(host=self.host, port=self.port, db=self.db, user=self.user, password=self.password, charset=self.charset)
        self.cursor = self.conn.cursor()

    def close(self):
        self.cursor.close()
        self.conn.close()

    def add_update_delete(self, sql, params=[]):
        try:
            self.open()

            self.cursor.execute(sql, params=[])
            self.conn.commit()

            self.close()
            print('success')
        except Exception:
            print('something wrong')

    def all(self, sql, params):
        result = None
        try:
            self.open()

            self.cursor.execute(sql, params=[])
            result = self.cursor.fetchall()

            self.close()

        except Exception:
            print('something wrong')

        return result

# 在需要时调用
from PymysqlHelper import PymysqlHelper

name = input('input your name: ')
id1 = input('input your stuid: ')

sql = 'update students set name=%s where id=%s'
params = [name, id1]

sqlhelper = PymysqlHelper('localhost', '3306', 'students', 'root', 'rong')
sqlhelper.add_update_delete(sql, params)
```