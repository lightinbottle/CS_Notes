## Spring

* Java 应用程序开发框架，数以百万的来自世界各地的开发人员使用 Spring 框架来创建性能好、易于测试、可重用的代码。 

* **IOC** 

  控制反转，对于`spring`框架来说，就是由`spring`来负责控制对象的生命周期和对象间的关系。 

  所有的类都会在**`spring`容器**中登记，告诉`spring`你是个什么东西，你需要什么东西，然后`spring`会在系统运行到适当的时候，把你要的东西主动给你，**同时也把你交给其他需要你的东西**。所有的类的创建、销毁都由`spring`来控制，也就是说**控制对象生存周期的不再是引用它的对象，而是`spring`**。对于某个具体的对象而言，**以前是它控制其他对象，现在是所有对象都被`spring`控制**，所以这叫**控制反转**。

* Spring IOC的原理

* 使用**IOC的好处**

  资源集中管理；降低了资源双方的依赖程度，解耦；

* **DI依赖注入**

  `IoC`的一个重点是在系统运行中，动态的向某个对象提供它所需要的其他对象。这一点是通过`DI`（`Dependency Injection`，依赖注入）来实现的。 

  比如对象A需要操作数据库，以前我们总是要在A中自己编写代码来获得一个Connection对象，有了 spring我们就只需要告诉spring，A中需要一个Connection，至于这个Connection怎么构造，何时构造，A不需要知道。在系统运行时，spring会在适当的时候制造一个Connection，然后像打针一样，注射到A当中，这样就完成了对各个对象之间关系的控制。**A需要依赖 Connection才能正常运行，而这个Connection是由spring注入到A中的，依赖注入的名字就这么来的**。

  IoC容器在运行期间，动态地将某种依赖关系注入到对象之中。 

  **工厂模式**

* **AOP** 

  面向切片编程，AOP采用一种称为“横切”的技术，将**涉及多业务流程的通用功能抽取并单独封装**，形成独立的切面，在**合适的时机**将这些切面横向切入到业务流程指定的位置中。 

  在一个业务系统中，**用户登录是基础功能**，凡是涉及到用户的业务流程都要求用户进行系统登录。如果把用户登录功能代码写入到每个业务流程中，会造成**代码冗余，维护也非常麻烦**，当需要修改用户登录功能时，就需要修改每个业务流程的用户登录代码，这种处理方式显然是不可取的。比较好的做法是把用户登录功能抽取出来，形成独立的模块，**当业务流程需要用户登录时，系统自动把登录功能切入到业务流程中**。

  AOP的底层是**动态代理**

  **代理模式**

* **Spring 中IOC AOP的原理**

* **Spring bean**

  被称作 bean 的对象是构成应用程序的支柱也是由 Spring IoC 容器管理的。**bean 是一个被实例化，组装，并通过 Spring IoC 容器所管理的对象**。这些 bean 是由用容器提供的配置元数据创建的，例如，已经在先前章节看到的，在 XML 的表单中的 定义。 

* **bean的生命周期**

  定义 初始化 调用 销毁

* **spring IoC容器**

  Spring BeanFactory  Spring ApplicationContext

  BeanFactory实现了工厂设计模式

* **spring mvc**

  按照spring设计的mvc框架，主要用于**开发WEB应用**和网络接口，它是Spring的一个模块，通过Dispatcher Servlet, ModelAndView 和 View Resolver，让应用开发变得很容易。

* **Spring boot**

  实现自动配置，降低项目搭建的复杂度,其设计目的是为了**简化 Spring 应用的搭建和开发过程**。 

* Springboot和**SpringCloud**之间的区别

  Spring boot 是 Spring 的一套快速配置[脚手架](https://www.baidu.com/s?wd=%E8%84%9A%E6%89%8B%E6%9E%B6&tn=24004469_oem_dg&rsv_dl=gh_pl_sl_csd) ，可以基于spring boot 快速开发单个微服务 ；

  springCloud 是一个基于Spring Boot实现的云应用开发工具，是一种云端分布式架构解决方案 ；

* **spring boot 依赖注入的方式**

* spring中用到哪些设计模式

* **Hibernate**   第三方的数据库访问框架，Spring能与其无缝集成

* spring事务管理

  声明式的事务管理

* **SSH框架**

  * Struts 一个轻量级的MVC框架 主要用来开发java web
  * Spring        
  * Hibernate  在Java对象与关系数据库之间**建立某种映射，以实现直接存取Java对象**！ 

* **ORM 对象关系映射**

  

