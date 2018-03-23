---
layout: post
title:  "python中三个特殊的内置函数"
date: 2017-02-22 10:22:43 +0800
categories: python
tags:  python
author: csurong
---

* content
{:toc}

不写下来容易忘 




## iterable 对象

可迭代对象（iterable），JS 中也有这个名词，但还是有明显区别的。在python中，大白话解释就是 可以直接作用于for循环的对象。可以使用`isinstance()`判断一个对象是否是Iterable对象.常用的像 list、dict、tuple、set、str 类型以及生成器（generator）都是可迭代对象。

这三个内置函数就是和可迭代对象打交道的

## map

文档中是这么写的
```
Apply function to every item of iterable and return a list of the results. If additional iterable arguments are passed, function must take that many arguments and is applied to the items from all iterables in parallel. If one iterable is shorter than another it is assumed to be extended with None items. If function is None, the identity function is assumed; if there are multiple arguments, map() returns a list consisting of tuples containing the corresponding items from all iterables (a kind of transpose operation). The iterable arguments may be a sequence or any iterable object; the result is always a list.
```
map函数使用自定义的function处理iterable中的每一个元素，将所有的处理结果以list的形式返回

```python
def func(x):
    ''' '''
    return x*x

print map(func, [1,2,4,8]) # [1,2,16,64]
```

## reduce

```python
Apply function of two arguments cumulatively to the items of iterable, from left to right, so as to reduce the iterable to a single value. For example, reduce(lambda x, y: x+y, [1, 2, 3, 4, 5]) calculates ((((1+2)+3)+4)+5). The left argument, x, is the accumulated value and the right argument, y, is the update value from the iterable. If the optional initializer is present, it is placed before the items of the iterable in the calculation, and serves as a default when the iterable is empty. If initializer is not given and iterable contains only one item, the first item is returned
```
en，就是一个接着一个来的意思。[a,b,b,b,b,b] 大佬a把b吃掉，a和b执行后还叫a，a在接着吃掉后面的

## filter

```python
Construct a list from those elements of iterable for which function returns true. iterable may be either a sequence, a container which supports iteration, or an iterator. If iterable is a string or a tuple, the result also has that type; otherwise it is always a list. If function is None, the identity function is assumed, that is, all elements of iterable that are false are removed.
```
这个还是很简单的，filter 本身就是过滤的意思。元素经过一波筛选，符合条件的组成新的list

## 总结

map、reduce、filter 用的还是比较多的，经常和匿名函数一起用，比较方便。官方文档中还是有很多尚不熟悉的内置函数，常用的记住了，其他的等用的时候再去查吧。

## 参考

* [文档](https://docs.python.org/2/library/functions.html#filter%E4%B8%AD%E5%A6%82%E4%B8%8B%E4%BB%8B%E7%BB%8Dfilter%E5%87%BD%E6%95%B0%EF%BC%9A)


