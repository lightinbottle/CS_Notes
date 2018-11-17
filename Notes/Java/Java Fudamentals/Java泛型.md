# Java 泛型



---

 ## 1. 泛型定义  
 * 泛型： Generic programming, 即参数化类型，意味着编写的代码可以被不同类型的对象所重用。

 ## 2. 泛型对象、方法  
 * 泛型类就是具有一个或者多个泛型变量的类。  
 * 泛型方法格式：(T 是类型变量) 
 ```java
 public static <T> T getMiddle(T... a)  //大多数情况下调动泛型方法可以省略类型参数，如：ClassName.getMiddle(1,2,3)
 ```
 * 类型变量可以添加一些限定  
 ```java
 public static <T extends Comparable> T min(T[] a)  
 ```

 ## 3. 泛型和虚拟机  
 * Java虚拟机没有泛型类型对象——所有的对象都是属于普通类。Java的泛型是在编译器这个层次实现的，编译过后生成的字节码文件（Class文件）没有泛型的相关信息，在运行时没有泛型的概念，在编译时擦除类型变量。
 * Java类型擦除： 在定义一个泛型类型时，会生成一个相应的原始类型。原始类型就是泛型类型经过编译器删去类型参数（类型擦除）后生成的。擦除类型变量，并替换为限定类型（无限定类型的变量就用Object）。
    下面这段代码可以表明，泛型类型都对应于同一个原始类型：
    ```java
    List<String> ls = new ArrayList<String>();  
    List<Integer> li = new ArrayList<Integer>();  
    System.out.println(ls.getClass() == li.getClass());  
    ```    
 * 类型擦除为了代码版本的兼容，使得早期版本的非泛型java程序还能在后续高版本的JVM上运行  
 
 ## 4.参考
 * [Java 泛型详解](http://www.importnew.com/24029.html)
 * [10 道 Java 泛型面试题](https://cloud.tencent.com/developer/article/1033693)

