---
layout: post
title:  "SQLAlchemy"
date: 2017-04-01 22:29:38 +0800
categories: flask
tags:  python flask sql
author: csurong
---

* content
{:toc}

学习 flask-SQLAlchemy 中的记录和拓展




## 常用的SQLAlchemy列类型

```
类型名			Python类型				说　　明
Integer			int						普通整数，一般是32 位
SmallInteger	int						取值范围小的整数，一般是16 位
BigInteger		int	或long 				不限制精度的整数
Float			float					浮点数
Numeric			decimal.Decimal			定点数
String			str						变长字符串
Text			str						变长字符串，对较长或不限长度的字符串做了优化
Unicode			unicode					变长Unicode 字符串
UnicodeText		unicode					变长Unicode 字符串，对较长或不限长度的字符串做了优化
Boolean			bool					布尔值
Date			datetime.date			日期
Time			datetime.time			时间
DateTime		datetime.datetime		日期和时间
Interval		datetime.timedelta		时间间隔
Enum			str						一组字符串
PickleType		任何Python				对象自动使用Pickle 序列化
LargeBinary		str						二进制文件
```

## 常用的选项设置

```
选项名				说　　明
primary_key			如果设为True，这列就是表的主键
unique				如果设为True，这列不允许出现重复的值
index				如果设为True，为这列创建索引，提升查询效率
nullable			如果设为True，这列允许使用空值；如果设为False，这列不允许使用空值
default				定义默认值
```

## 表关系

```
class Role(db.Model):   
    # ...   
    users = db.relationship('User', backref='role') # 1 定义 relationship   

class User(db.Model):   
    # ...   
    role_id = db.Column(db.Integer, db.ForeignKey('roles.id')) # n 定义 ForeignKey
```
添加到Role 模型中的users 属性代表这个关系的面向对象视角。对于一个Role 类的实例，其users 属性将返回与角色相关联的用户组成的列表。
db.relationship() 的第一个参数表明这个关系的另一端是哪个模型。如果模型类尚未定义，可使用字符串形式指定。db.relationship() 中的backref 参数向User 模型中添加一个role 属性，从而定义反向关系。这一属性可替代role_id 访问Role 模型，此时获取的是模型对象，而不是外键的值。

大多数情况下，db.relationship() 都能自行找到关系中的外键，但有时却无法决定把哪一列作为外键。例如，如果User 模型中有两个或以上的列定义为 Role 模型的外键，SQLAlchemy 就不知道该使用哪列。如果无法决定外键，你就要为db.relationship() 提供额外参数，从而确定所用外键


## 关系选项

```
选项名			说　　明
backref			在关系的另一个模型中添加反向引用
primaryjoin		明确指定两个模型之间使用的联结条件。只在模棱两可的关系中需要指定
lazy			指定如何加载相关记录。可选值有select（首次访问时按需加载）、immediate（源对象加载后就加载）、joined（加载记录，但使用联结）、subquery（立即加载，但使用子查询），noload（永不加载）和dynamic（不加载记录，但提供加载记录的查询）
uselist			如果设为Fales，不使用列表。用于定义一对一关系
order_by		指定关系中记录的排序方式
secondary		指定多对多关系中关系表的名字。多对多的情况下建议使用它来指定中间表
secondaryjoin	SQLAlchemy 无法自行决定时，指定多对多关系中的二级联结条件
```

## 查询过滤

```
过滤器			说　　明
filter()		把过滤器添加到原查询上，返回一个新查询
filter_by()		把等值过滤器添加到原查询上，返回一个新查询
limit()			使用指定的值限制原查询返回的结果数量，返回一个新查询
offset()		偏移原查询返回的结果，返回一个新查询
order_by()		根据指定条件对原查询结果进行排序，返回一个新查询
group_by()		根据指定条件对原查询结果进行分组，返回一个新查询
```

## 查询执行函数

```
方　法			说　　明
all()			以列表形式返回查询的所有结果
first()			返回查询的第一个结果，如果没有结果，则返回None
first_or_404()	返回查询的第一个结果，如果没有结果，则终止请求，返回404 错误响应
get()			返回指定主键对应的行，如果没有对应的行，则返回None
get_or_404()	返回指定主键对应的行，如果没找到指定的主键，则终止请求，返回404 错误响应
count()			返回查询结果的数量
paginate()		返回一个Paginate 对象，它包含指定范围内的结果
```

## 参考

* [flask-SQLAlchemy 文档](http://www.pythondoc.com/flask-sqlalchemy/quickstart.html)

* 其他