---
title: "Java集合总结一之Collection体系"
date: 2019-10-15T21:33:37+08:00
draft: false
---
# Java集合框架
* Java集合框架最重要的两个接口为Collection和Map，将集合的接口与实现分离
    1. Collection是单列集合；Map是双列集合
    2. Collection中只有Set系列要求元素唯一；Map中键需要唯一，值可以重复
    3. Collection的数据结构是针对元素的；Map的数据结构是针对键的。

![](/images/Java Collection/1.png)

## Collection接口
* 集合类的基本接口

### 迭代器

* 迭代器是按次序一个一个地获取集合中所有的对象，是访问集合中每个元素的标准机制。
迭代器的创建：Collection接口的iterator()方法返回一个Iterator
Iterator it=test.iterator(); //将test集合对象转为迭代器
迭代器的常用方法：

```
public interface Iterator {
	boolean hasNext();

	Object next();

	void remove(); // Optional
}
```
**在调用remove()方法的时候, 必须调用一次next()方法. remove()方法实际上是删除上一个返回的元素.（可以理解next（）越过了下一个元素并返回刚刚越过的那个元素的引用）**

* 可以用迭代器和lambda表达式处理集合中剩余的每个元素

```
iterator.forEachRemaining(element -> doSomethingWith(element));
```
### Collection常用方法

* AbstractCollection类提供了很多常用方法的实现:

![](/images/Java Collection/2.png)

一个具体的集合类可以直接扩展他，其实在Java8中最好还是在Collection接口中直接已默认方法的形式直接实现。不过Collection接口中增加了很多支持流的默认方法，例如
```
default boolean removeIf(Predicate<? super E> filter)
```
用于删除某个满足条件的元素

## List & Set
### List
* List是一个有序集合，支持随机访问或者迭代器访问
常用方法：

![](/images/Java Collection/3.png)
  
* 最常⽤的ArrayList
* 本质上就是⼀个数组
* ⾯试题：动态扩容的实现
* RandomAccess标记接口用来标记特定集合是否支持随机访问（因为基于链表实现的list不支持随机访问，时间复杂度为O(n)）

### Set
* Set集合的特点：元素不重复，无下标， Set集合下面有`HashSet(存取无序)，LinkedHashSet，TreeSet`
* 与Collection接口有相同的方法签名，主要用于区分其他集合

## hashcode与equals()方法
### equals()方法
* equals()方法是用来判断其他的对象是否和该对象相等
* 它的性质有：

    * 自反性（reflexive）。对于任意不为null的引用值x，x.equals(x)一定是true。

    * 对称性（symmetric）。对于任意不为null的引用值x和y，当且仅当x.equals(y)是true时，y.equals(x)也是true。

    * 传递性（transitive）。对于任意不为null的引用值x、y和z，如果x.equals(y)是true，同时y.equals(z)是true，那么x.equals(z)一定是true。

    * 一致性（consistent）。对于任意不为null的引用值x和y，如果用于equals比较的对象信息没有被修改的话，多次调用时x.equals(y)要么一致地返回true要么一致地返回false。

    * 对于任意不为null的引用值x，x.equals(null)返回false。
    
对于Object类来说，equals()方法在对象上实现的是差别可能性最大的等价关系，即，对于任意非null的引用值x和y，当且仅当x和y引用的是同一个对象，该方法才会返回true

* 当equals()方法被override时，hashCode()也要被override。按照一般hashCode()方法的实现来说，相等的对象，它们的hash code一定相等
### hashCode()方法
* Java世界⾥第⼆重要的约定：hashCode
    *  同⼀个对象必须始终返回相同的hashCode
    * 两个对象的equals返回true，必须返回相同的hashCode
    * 两个对象不等，也可能返回相同的hashCode

* 哈希算法
    * 哈希就是⼀个单向的映射
    * 例⼦：从姓名到姓到哈希运算
    * 从任意对象到⼀个整数的hashCode
* hashCode()方法给对象返回一个hash code值。这个方法被用于hash tables，例如HashMap。

* 它的性质是：

    * 在一个Java应用的执行期间，如果一个对象提供给equals做比较的信息没有被修改的话，该对象多次调用hashCode()方法，该方法必须始终如一返回同一个integer。

    * 如果两个对象根据equals(Object)方法是相等的，那么调用二者各自的hashCode()方法必须产生同一个integer结果。

**并不要求根据equals(java.lang.Object)方法不相等的两个对象，调用二者各自的hashCode()方法必须产生不同的integer结果。然而，程序员应该意识到对于不同的对象产生不同的integer结果，有可能会提高hash table的性能。**

* 大量的实践表明，由Object类定义的hashCode()方法对于不同的对象返回不同的integer。

    * 在object类中，hashCode定义如下：
    ```
    public native int hashCode();
    ```
     说明是一个本地方法，它的实现是根据本地机器相关的。当然我们可以在自己写的类中覆盖hashcode()方法，比如String、Integer、Double等这些类都是覆盖了hashcode()方法的。例如在String类中定义的hashcode()方法如下：

    ```    
    public int hashCode() {  
        int h = hash;  
        if (h == 0) {  
            int off = offset;  
            char val[] = value;  
            int len = count;  
  
            for (int i = 0; i < len; i++) {  
                h = 31 * h + val[off++];  
            }  
            hash = h;  
        }  
        return h;  
    }  
    ```
* 重写equals()和hashcode()小结：
    1. 重点是equals，重写hashCode只是技术要求（为了提高效率）
    2. 为什么要重写equals呢？因为在java的集合框架中，是通过equals来判断两个对象是否相等的
    3.事在向HashSet集合中添加元素时，其实只要重写equals()这一条也可以。但当hashset中元素比较多时，或者是重写的equals()方法比较复杂时，我们只用equals()方法进行比较判断，效率也会非常低，所以引入了hashCode()这个方法，只是为了提高效率，且这是非常有必要的
## Map 映射
* Map 集合类用于存储元素对（称作“键”和“值”），其中每个键映射到一个值。

### Map常用操作

![](/images/Java Collection/4.png)

* 要迭代处理Map中的值可以用forEach()方法：
```
Map m = new HashMap();
        m.forEach((k,v)-> doSomethingWith(k,v));
```
* Map的访问也和HashSet的特点一样是无需的，Map体系与Set体系不同并不实现Iterator接口，当然也不会具有迭代器，Map集合间接通过Set的迭代器遍历集合，Map集合访问方式主要有两种：keySet和entrySet
    * keySet：将Map中所有的键存入到Set集合，再借助Set的迭代器Iterator取出所有的键，最后再根据get方法获取到键对应的值。**keySet所取出的键值与原map保持关联，互相有影响**
    * entrySet:将Map集合的映射关系Map.Entry存入到Set集合中，映射关系的数据类型就是Map.Entry. eg:
    ```
    /*
    Map.Entry：其实Entry是Map接口中的一个内部接口（因为能加static修饰符只能是成员变量，所以只能是成员接口），因为只能先有Map集合才有映射关系，而映射关系是直接处理成员数据的，所以设计为内部接口。
    interface Map{
        public static interface Entry{
            public abstract Object getKey();
            ...
        }
    }
    //嵌套接口的实现
    class HashMaps implements Map{
        class Maps implements Map.Entry{
            public Object getKey();
            ...
        }
    }*/
    

    Set<Map.Entry<T,T>> entrySet=map.entrySet();//得到Set集合
    Iterator<Map.Entry<T,T>> it=entrySet.iterator();//得到Set集合的迭代器
    while(it.hasNext()){
        Map.Entry<T,T> m=it.next();//通过迭代器获取映射关系
        String key=m.getKey();//通过映射关系获取key
        String value=map.getValue(key);
    }
    ```
### Map体系HashTable和HashMap
* 哈希表HashTable实现了Map接口，该哈希表将键映射到对应的值，该集合是线程同步的。任何非null对象都可以作为键Key或者值Value，换言之不允许存入null键null值。为了能成功在哈希表中存储和获取对象，用作键的对象必须实现hashCode和equals方法。
* HashMap底层数据结构也是哈希表，除了非同步和允许使用null键和null值、效率比较高以外，其他功能和HashTable类相同。
要保存到Map集合的对象完成三步工作：实现Comparable<对象>、覆写compareTo、hashCode、equals方法
* HashMap是最常⽤，最⾼效的Map实现：
    * HashMap的扩容
    * HashMap的线程不安全性
    * HashMap在Java 7+后的改变：链表->红⿊树
    * HashMap和HashSet本质上是⼀种东⻄

### Map体系TreeMap
* TreeMap是可以排序的Map集合，TreeMap底层数据结构是二叉树，线程不同步，可以用于给Map中的键Key排序，可以通过compareTo方法来排序，也可以通过传入比较器构造Map集合来覆盖掉comareTo方法来实现排序。



