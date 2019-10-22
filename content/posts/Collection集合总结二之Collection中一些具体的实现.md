---
title: "Collection集合总结二之Collection中一些具体的实现"
date: 2019-10-17T14:45:55+08:00
draft: true
---
# 常见集合的实现
*******
## 链表实现的List LinkedList
* inkedList:是基于链表结构实现的，所以查询速度慢，增删速度快，提供了特殊的方法，对头尾的元素操作（进行增删查）。

* 可以使用LinkedList来实现栈和队列；栈是先进后出，而队列是先进先出

### ListIterator接口
* `add()` 方法：与Collection中的add方法不同，这个方法不返回boolean。
* 有两个方法用来反向遍历链表
```
E previous()
boolean hasPrevious()
```
* add方法只依赖迭代器的位置，remove方法依赖迭代器的状态
* 当用多个iterator遍历同一个集合，只能附加一个可以同时读写的迭代器，否则有可能会抛出Concurrent ModificationException
## ArrayList
* 底层是使用动态数组实现，所以查询速度快，增删速度慢，最常用的List实现
* 要注意的是ArrayList是不同步的，但是速度优于Vector，如果没有多线程访问，使用前者
