# Java

## 1. 异常检测

### 异常检测的基础知识

* Java异常及错误的类图

  <center> <img src="Pics\java异常类图.jpg" width="70%" height="70%"/></center>

* error exception都继承了 throwable类

* `error`:正常情况下不会出现，大多数error会导致程序不可恢复，正常情况下不需要捕获

  * OutofMemoryError 内存溢出 

    * 内存泄露 ； 对象引用未清空 ； 存在死循环创建对象 ； 调整JVM启动参数增大内存值

  * NoClassDefFoundError 

    * Java在编译时能找到对应的类，但是在JVM运行时不能找到对应的类，在classpath找不到这个相应的类（编译期间没问题，运行时无法实例化一个类）

    * 常见原因： 静态成员初始化失败 （静态成员变量初始化在编译期能通过，但是运行时不能通过）

      如果JVM或者ClassLoader实例尝试加载（可以通过正常的方法调用，也可能是使用new来创建新的对象）类的时候却找不到类的定义。要查找的类在编译的时候是存在的，运行的时候却找不到了。这个时候就会导致NoClassDefFoundError。

    * 注意与 `ClassNotFoundException`的区别

      * 参考：[NoClassDefFoundError与ClassNotFoundException的区别](https://blog.csdn.net/suifeng3051/article/details/83382834)

* `exception`分类

  * checked exception 可检查异常，在代码中必须显示的捕获处理，是编译器检查的一部分
  * unchecked exception 不可检查异常，也叫 runtime exception运行时异常,通常可以通过逻辑检查来避免
    * NullPointerException  空指针异常
    * ArrayIndexOutOfBoundsException  数组越界
    * ClassCastException 运行时检测到的类型转换非法
    * ClassNotFoundException 运行时无法找到指定名称的类
      * Java支持使用`Class.forName`方法来动态地加载类，任意一个类的类名如果被作为参数传递给这个方法都将导致该类被加载到JVM内存中，如果这个类在类路径中没有被找到，那么此时就会在运行时抛出`ClassNotFoundException`异常。 
      * 常见原因： 类名写错了 ； 没有依赖相应的jar包 ； 类名不合法 ； jar包不在classpath中

### 异常处理方法及注意

#### 异常处理基础

* 常见的`try...catch...finally`异常处理块，finally用于资源回收，finally会在return之间执行
* 异常抛出： `throw` `throws`
  * throw 出现在函数体内，表示当前语句抛出异常
  * throws 声明一个函数有可能抛出某种类型的异常

#### 异常检测的注意事项

* `try...catch...finally`捕获异常时，尽量声明为对特定异常的捕获，而不是对通用异常的捕获，通用异常对于定位具体的问题没有多大的帮助。
* 不要生吞异常： 异常在生产环境中要输出到特定的日志当中
* 在异常无法处理的时候，可以选择向外抛出，在更高的层面，因为有了清晰地业务逻辑，可能找到更清晰的解决办法。
* `try...catch...finally`代码块会产生额外的性能开销，所以避免使用try包住整个代码块，而是具体到可能抛出异常的语句。
* java每实例化一个exception,都会对当时的栈进行快照，产生很大的开销。

