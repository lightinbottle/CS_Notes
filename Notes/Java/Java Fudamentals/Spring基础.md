## Spring基础

## Spring IOC容器

* **BeanFactory容器**：最简单的容器，主要功能是为依赖注入提供功能。

* **Application Context容器**

  是beanfactory的子接口，也被称为spring上下文

  包括三个接口实现：

  * **FileSystemXmlApplicationContext**  从xml文件中加载已经定义的bean,需要提供xml的完整路径
  * **ClassPathXmlApplicationContext** 从xml文件中加载已经定义的bean,不需要提供xml的完整路径，容器会在ClassPath路径下搜索bean配置文件
  * **WebXmlApplicationContext** 会在一个web应用程序范围内加载在xml中已经定义的bean

* **Bean**

  bean是一个被实例化、组装，并通过Spring IOC容器所管理的对象。

  bean=[spring ioc容器创建、管理] java POJO（简单java类）+ 配置元数据

  配饰元数据（xml中的bean定义包含）包括：

  * 如何创建一个bean

  * bean的生命周期

  * bean的依赖关系

    <center> <img src="Pics\bean创建.jpg"/></center>

* **Bean 的作用域** **scope**

  默认是**singleton**

  <center> <img src="Pics\bean作用域.png"/></center>

* **bean的生命周期**

  bean的生命周期可以表达为：<u>bean定义-bean初始化-bean的使用-bean的销毁</u>

  * **初始化回调**

    ```java
    <bean id="exampleBean"  class="examples.ExampleBean" init-method="init"/>
    ```

    在POJO定义中实现 init 方法

  * **销毁回调**

    ```java
    <bean id="exampleBean"  class="examples.ExampleBean" destory-method="destory"/>
    ```

    在POJO定义中实现 destory 方法

* **Bean定义继承**

  bean 定义可以包含很多的配置信息，包括 构造函数的参数，属性值，容器的具体信息如：初始化方法，静态工厂方法等

  子bean继承父类的配置数据，可以重写一些值，可以添加一些值

  spring bean的继承跟java基本类的继承无关

  * **直接继承**

    ```java
    <bean id="helloWorld" class="com.tutorialspoint.HelloWorld">
          <property name="message1" value="Hello World!"/>
          <property name="message2" value="Hello Second World!"/>
    </bean>
    
    <bean id="helloIndia" class="com.tutorialspoint.HelloIndia" parent="helloWorld">
          <property name="message1" value="Hello India!"/>
          <property name="message3" value="Namaste India!"/>
    </bean>
    ```

  * **定义bean模板，通过模板继承**

    ```java
    <bean id="beanTeamplate" abstract="true">   //bean模板不能实例化，是抽象的
          <property name="message1" value="Hello World!"/>
          <property name="message2" value="Hello Second World!"/>
          <property name="message3" value="Namaste India!"/>
    </bean>
    
    <bean id="helloIndia" class="com.tutorialspoint.HelloIndia" parent="beanTeamplate">
          <property name="message1" value="Hello India!"/>
          <property name="message3" value="Namaste India!"/>
    </bean>
    ```

## Spring 依赖注入

* 基于构造函数的依赖注入

  在bean的定义中 指定构造器的输入参数

  ```java
  <!-- Definition for textEditor bean -->
  <bean id="textEditor" class="com.tutorialspoint.TextEditor">
  <constructor-arg ref="spellChecker"/>
  </bean>
  ```

* 基于设置函数的依赖注入

  ```java
  <!-- Definition for textEditor bean -->
  <bean id="textEditor" class="com.tutorialspoint.TextEditor">
  <property name="spellChecker" ref="spellChecker"/>
  </bean>
  ```

  如果你要把一个引用传递给一个对象，那么你需要使用 标签的 **ref** 属性，而如果你要直接传递一个值，那么你应该使用 **value**(如对一个 String类型的属性值 赋值) 属性。 

* 可以通过 <list> <set>  <map> 注入集合类型

### 装配Bean

由spring容器负责将对象依赖的另一个对象注入到其中，使得各个对象实现之间实现松耦合。

装配bean 就是spring创建bean之间的协作关系

### 自动装配

* 可以通过xml中bean定义中的<autowire>属性 来定义自动装配的模式

  <center> <img src="Pics\bean自动装配的模式.png"/></center>

### 基于注解的配置

首先在spring配置文件中允许使用注解配置 `<beans>...<context:annotation-config/>...</beans> `

<center> <img src="Pics\bean基于注解的配置.png"/></center>

### 基于Java的配置

**@Configuration** 的注解类表示这个类可以使用 Spring IoC 容器作为 bean 定义的来源。 

**@Bean** 注解告诉 Spring，一个带有 @Bean 的注解方法将返回一个对象，该对象应该被注册为在 Spring 应用程序上下文中的 bean。 

* **bean装配的方法**

  * 在XML文件中 显式的配置
  * 在Java中显式配置
  * 隐式的bean发现机制和自动装配

* 自动化装配bean

  * 对于有`@Component`注解的类，spring会为这个类创建bean,而不需要在配置文件中显式配置，spring会启动组件扫描（默认不启动组件扫描，需要显示的配置），自动发现应用上下文中创建的bean
  * 在需要进行装配的方法（构造器或者普通方法）上，通过`@Autowired`注解来进行声明，实现自动化的装配

* 在Java中显式配置

  * 通过`JavaConfig`类来显式配置

    创建JavaConfig类的关键在于为其添加@Configuration注解

  * 创建bean

    ```
    @Bean
    public CompactDisc sgtPeppers(){
        return new SgtPeppers();
    }
    ```

    `@Bean`注解告诉spring这个java方法会返回一个对象，该对象要注册成为一个bean，bean的ID与方法名一样

  * 解决依赖注入

    ```
    @Bean
    public CDPlayer cdPlayer(){
        return new CDPlayer(SgtPeppers());
    }
    ```

    spring会拦截所有对`sgtPeppers()`的调用，并确保直接返回该方法创建的bean，而不是每次都对该方法进行实际的调用；因为spring中默认的bean都是单例的，所以都过sgtPeppers()返回的bean实例都是一个

* 都过XML显式装配

  * `<bean id="customer" class="springdemo.Customer"></bean>`

  * 借助构造器注入初始化bean

    ```
    <bean id="customer" class="springdemo.Customer">
    <constructor-arg ref="compactDisc">  //要注入的bean的 ID
    </bean>
    ```

    可以使用spring的`C-命名空间`来表示：<c:cd-ref="compactDisc">（c:构造器参数名-ref="bean的ID"）

    不带ref就是将字面量（value）注入到属性中

    

    将字面量注入初始化bean

    ```
    <bean id="customer" class="springdemo.Customer">
    <constructor-arg value="the beatles">  //要注入的bean的 ID
    </bean>
    ```

    可以将`Set、List、HashMap`注入到构造器中

    ```/bean
    <bean id="customer" class="springdemo.Customer">
    <constructor-arg>
    	<list>
    		<value>...</value>   //<ref bean=".." />list中的元素可以是值可以是bean对象
    		<value>...</value>
    		<value>...</value>
    	</list>
    </constructor-arg>
    </bean>
    ```

  * 借助<property>设置bean的属性

    <property name="str" value="STJcccU"/>

    可以使用spring的`p-命名空间`，<p:cd-ref="compactDisc">（p:属性名称-ref="bean的ID"）

    不带ref就是将字面量（value）注入到属性中

## Spring中的AOP

* AOP的相关概念
  * aspect 切面
  * pointcut 切点  
  * advice 通知 

* AOP通知的类型
  * before  方法执行之前
  * after     执行之后
  * after-returning  在一个方法执行之后，只有在方法成功完成时，才能执行通知。 
  * after-throwing   在一个方法执行之后，只有在方法退出抛出异常时，才能执行通知。 
  * around 在建议方法调用之前和之后，执行通知
* AOP的术语

### AOP的基于XML的架构

```java
<aop:config>
   <aop:aspect id="myAspect" ref="aBean">   //ref 指示的也是一个bean实例
   <aop:pointcut id="businessService"
      expression="execution(* com.xyz.myapp.service.*.*(..))"/>   //定义一个切点
   <aop:before pointcut-ref="businessService" 
         method="doRequiredTask"/>                               //在相应的切点执行什么样的操作
   </aop:aspect>                                                 //method 是bean实例中定义的方法
</aop:config>
<bean id="aBean" class="...">
...
</bean>
```

### AOP的基于@AspectJ的架构

AspectJ是一个面向切面的框架 ，通过给普通的java类添加注释来实现 AOP

* 声明一个aspect

  ```java
  package org.xyz;
  import org.aspectj.lang.annotation.Aspect;
  @Aspect
  public class AspectModule {
  }
  ```

* 声明一个切入点

  ```java
  import org.aspectj.lang.annotation.Pointcut;
  @Pointcut("execution(* com.xyz.myapp.service.*.*(..))") // expression 
  private void businessService() {}  // signature
  ```

* 声明建议

  ```java
  @Before("businessService()")
  public void doBeforeTask(){
   ...
  }
  ```

* **AOP的实现原理&各种不同实现方式的区别**

  AOP的底层实现原理是基于动态代理的，细分为两种：

  * 基于JDK的动态代理（反射）

  * 基于CGLIB的动态代理

     CGLib采用非常底层的字节码技术，可以为一个类创建子类，并在子类中采用方法拦截的技术拦截所有父类方法的调用，并顺势织入横切逻辑。 

  **两者的区别&适用场景**

  *  <u>CGLib所创建的动态代理对象的性能比JDK的高大概10倍，但CGLib在创建代理对象的时间比JDK大概多8倍</u>，所以对于singleton的代理对象或者具有实例池的代理，因为无需重复的创建代理对象，所以比较适合CGLib动态代理技术，反之选择JDK代理。值得一提的是由于CGLib采用动态创建子类的方式生成代理对象，<u>所以不能对目标类中final的方法进行代理</u>。 

## Spring JDBC框架

* **JDBCTemplate**

  JdbcTemplate 类执行 SQL 查询、更新语句和存储过程调用，执行迭代结果集和提取返回参数值。它也捕获 JDBC 异常并转换它们到 org.springframework.dao 包中定义的通用类、更多的信息、异常层次结构。 

  创建一个JDBCTemplate

* **DAO 数据访问对象**

  DAO 代表常用的数据库交互的数据访问对象。<u>DAOs 提供一种方法来读取数据并将数据写入到数据库中</u>，它们应该通过一个接口显示此功能，应用程序的其余部分将访问它们。 

  DAO是一个数据访问接口，将所有对数据源的访问操作抽象封装在一个公共的API中；建立一个接口，接口中定义了此应用程序会用到的所有的事务方法。

  在应用程序中，当需要和数据源进行交互的时候则使用这个接口，并且编写一个单独的类来实现这个接口。

## Spring事务管理

* **局部事务 & 全局事务**

* **编程式 & 声明式**

  编程式事务管理： 你在编程的帮助下有管理事务。这给了你极大的灵活性，但却很难维护。 

  声明式事务管理：你从业务代码中分离事务管理。你仅仅使用注释或 XML 配置来管理事务。 

  声明式事务管理比编程式事务管理更可取，尽管它不如编程式事务管理灵活，但它允许你通过代码控制事务。但作为一种横切关注点，声明式事务管理可以使用 **AOP** 方法进行模块化。<u>Spring 支持使用 Spring AOP 框架的声明式事务管理。</u> 

* spring的事物抽象

  spring的事物抽象是由**org.springframework.transaction.PlatformTransactionManager** 接口定义 

  ```java
  public interface PlatformTransactionManager {
     TransactionStatus getTransaction(TransactionDefinition definition);
     throws TransactionException;
     void commit(TransactionStatus status) throws TransactionException;
     void rollback(TransactionStatus status) throws TransactionException;
  }
  ```