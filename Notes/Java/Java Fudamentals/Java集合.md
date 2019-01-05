# Java 集合

## 集合的接口框架

* java集合中定义的接口框架如下图所示：

  ![collection接口结构](..\..\..\Pic\collection接口结构.png)

##集合的类框架

* java集合中定义的类框架如下图所示：

  ![collection类结构](..\..\..\Pic\collection类结构.png)

## 链表

* 链表在java中通过`linkedlist`类实现，双向链表，该类实现了`AbstractSequentialList`接口和`Deque`双向队列的接口

* 链表的迭代器

  链表是一个有序集合，集合类库对其提供了迭代器的子接口`ListIterator`,有以下几个常用的API：

  ```java
  ListIterator<> iter=list.listIterator();
  iter.add();        //添加到迭代器当前的位置之前，只跟迭代器的位置有关，跟迭代器的状态无关
  iter.remove();     //迭代器调用next()之后删除迭代器左边的元素；调用previous()之后，删除迭代器右边的元素，并且不能连续调用两次remove();即remove()不仅跟迭代器的位置有关，还跟迭代器的状态有关
  iter.hasNext();
  iter.next();       //返回迭代器越过的元素
  iter.hasPrevious();//反向遍历
  iter.previous();  //返回迭代器越过的元素
  iter.set();       //用一个新的元素取代调用next或者previous返回的上一个元素
  ```

* 链表不支持快速的随机访问，可以用`C instanceof RandomAccess`判断

## 数组列表

* 数组列表`ArrayList`底层通过`Array`实现，动态再分配

* Vector 和 ArrayList的对比

  Vector也继承了AbstractList类，主要有以下两点区别：

  * Vector的方法都是同步的(Synchronized),是线程安全的(thread-safe)，而ArrayList的方法不是，线程的同步必然要影响性能 
  * ArrayList在底层数组不够用时在原来的基础上扩展0.5倍，Vector是扩展1倍

* 建议在不需要同步的时候使用ArrayList, 否则必须使用Vector

## 散列集 HashSet

* java中的散列表通过`链表数组`实现，每个列表成为一个`桶（bucket）`。散列表给每个对象生成一个散列码，因此对象需要实现`hashCode`方法。hashCode应该与equals方法兼容，即a.equals(b)为true，a与b必须具有同样的散列码
* 散列冲突：对象散列码对应的桶已经有其他对象占用，Java8中，当桶都被占满时，就会从链表变成平衡二叉树。
* 影响散列表性能的两个重要因素：散列函数、初始的桶的个数（避免散列冲突）
* 当散列表的太满时，超过填充因子（默认是0.75），会产生一个2倍桶数的新的散列表，将原先的表再散列。
* `HashSet`是基于散列表实现的无序集合类。

##树集 TreeSet

* TreeSet是一个有序集合，基于红黑树（RedBlackTree）实现。每当一个元素被添加到树中时，就被放到正确的排序位置上。迭代器总是以排好序的顺序访问每个元素。

* 添加新元素到树中要比散列表中慢，时间复杂度是log2n(假设树中有n个元素)。

* 如果不需要对数据进行排序，就不需要使用树集。

* TreeSet 还实现了 `NavigableSet`接口，增加了几个便于定位元素以及反向遍历的方法，如：

  > E higher(E value)
  >
  > E lower(E value)
  >
  > E ceiling(E value)
  >
  > E floor(E value)
  >
  > E pollFirst()
  >
  > E pollFirst()
  >
  > Iterator<E> descendingIterator()

## 队列与双端队列

