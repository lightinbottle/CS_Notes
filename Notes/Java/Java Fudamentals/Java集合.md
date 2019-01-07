# Java 集合

## 集合的接口框架

* java集合中定义的接口框架如下图所示：

  <center> <img src="Pics\collection接口结构.png"/> </center>

  

## 集合的类框架

* java集合中定义的类框架如下图所示：

  <center> <img src="Pics\collection类结构.png"/> </center>

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

* 双端队列接口`Deque`,并由ArrayDeque 和 LinkedList 两个类实现，支持在队头队尾操作

## 优先级队列

* 优先级队列Priority Queue，是用`堆（heap）`实现，堆是一种可以自我调节的二叉树，可以让最小的元素移动到根节点位置，但是不用花费时间排序
* 与TreeSet的迭代不同，优先级队列的迭代不是有序的，但是对优先级队列的`删除`操作是有序的（每次删除最小的元素）
* PQ的典型用例就是任务调度，优先级高的（数值低）任务先被执行

## 基本映射

* java提供了两个通用的映射Map的实现：`HashMap(hash table)`和`TreeMap(red black tree)`

* TreeMap用键(key)的整体排序对元素进行排序，并将其组织成一个搜索树

* 如果不需要有序访问键(key), 最好选择HashMap

* 迭代处理映射的（key-value）,最容易的方法就是使用`forEach()`方法：

  > mapA.forEach((k,v) -> System.out.println("key="+k+", value="+v));

* Map的视图(view, 从Map得到的实现了Collection接口或者某个子接口的对象)

  Map的三个视图：

  ```java
  Set<E> keySet();
  Collection<V> values();
  Set<Map.Entry<k,v>> entrySet();
  ```

  可以在视图返回的集合中删除元素，被删除的元素也将被从原始映射中删除，但是不能通过这个集合添加元素。

## 弱散列映射

* `WeakHashMap`解决的问题： 垃圾回收器（GC）跟踪活动的对象，对于普通的HashMap，只要映射对象是活动的，那么其中的所有桶都是活动的，它们不可以被回收。这样就可能继续存储一些无用的值

  WeakHashMap中，当对键的唯一引用来自散列条目时，这一数据结构将于GC协同工作一起删除无用的键/值对。

## 链接散列集与映射

* LinkedHashSet LinkedHashMap解决的是HashSet 和 HashMap的迭代无序问题。将HashSet 和 HashMap中存储的元素通过双向链表连接起来

* LinkedHashMap支持按照`插入顺序`迭代的同时还支持按照`访问顺序`的迭代

  ```java
  public LinkedHashMap(int initialCapacity,
                           float loadFactor,              //填充因子
                           boolean accessOrder)   //true表示访问顺序，false表示插入顺序   
  ```

  [Map 综述（二）：彻头彻尾理解 LinkedHashMap](https://blog.csdn.net/justloveyou_/article/details/71713781)

* LinkedHashMap的`访问顺序`实现原理

  当accessOrder标志位为true时，表示双向链表中的元素按照访问的先后顺序排列，可以看到，虽然Entry插入链表的顺序依然是按照其put到LinkedHashMap中的顺序，但put和get方法均有调用recordAccess方法（put方法在key相同时会调用）。recordAccess方法判断accessOrder是否为true，如果是，则将当前访问的Entry（put进来的Entry或get出来的Entry）移到双向链表的尾部（key不相同时，put新Entry时，会调用addEntry，它会调用createEntry，该方法同样将新插入的元素放入到双向链表的***尾部***，既符合插入的先后顺序，又符合访问的先后顺序，因为这时该Entry也被访问了）；当标志位accessOrder的值为false时，表示双向链表中的元素按照Entry插入LinkedHashMap到中的先后顺序排序，即每次put到LinkedHashMap中的Entry都放在双向链表的尾部，这样遍历双向链表时，Entry的输出顺序便和插入的顺序一致，这也是默认的双向链表的存储顺序。

* LRU（Least Recently Used）最少最近使用原则， 高速缓存算法

  当我们要用LinkedHashMap实现LRU算法时，就需要调用该构造方法并将accessOrder置为true，并且需要重写`removeEldestEntry()`方法

  ```java
  protected boolean removeEldestEntry(Map.Entry<K,V> eldest) {
       return false;            //默认返回false也就是不删除eldest元素
  }
  ```

  eldest是双向链表中header后的那个节点

  一个LRU的典型的例子：

  ```java
  protected boolean removeEldestEntry(java.util.Map.Entry<K, V> eldest) {
          // TODO Auto-generated method stub
          if(size() > 6){         //6 表示缓存的容量，当超过一定容量就删除least recently表示的元素
              return true; 
          }
          return false;
   }
  
  ```

  

## 枚举集与映射

* 枚举类型的元素集和

## 标志散列映射

* 在这个类中，键的hashcode不是用hashCode( )函数计算，而是用`System.identityHashCode()`方法计算，该方法根据对象的内存地址来计算散列码。同时，两个对象进行比较时，IdentityHashMap类使用`==`而不是`equals`,也就是说两个对象即使内容相同，也被视为不一样的对象
* 可以用来跟踪每个对象的遍历情况（即使内容相同，也被视为不一样的对象）





