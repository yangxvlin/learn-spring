# learn-spring

## resource:
### book
1. spring in action 5th edition
### video
1. [尚硅谷Spring5框架教程（idea版）](https://www.bilibili.com/video/BV1Vf4y127N5)
2. [尚硅谷SpringMVC 2021新版教程丨一套快速上手spring mvc](https://www.bilibili.com/video/BV1Ry4y1574R)
3. [尚硅谷雷神SpringBoot2零基础入门springboot全套完整版（spring boot2）](https://www.bilibili.com/video/BV19K4y1L7MT)
4. [尚硅谷SpringCloud框架开发教程(SpringCloudAlibaba微服务分布式架构丨Spring Cloud)](https://www.bilibili.com/video/BV18E411x7eT)

## spring
- > 你对IoC的理解？为什么需要IoC/优点？
    - 1\) IOC=Inversion of Control=一种通过XML/注解的方式让spring获得object相关的配置信息并将object创建在Spring IOC容器中来管理object之间的关系以及生命周期。也就是说对象的控制权由应用代码转移到了外部容器。不同的客户端能通过ApplicationContext.getBean(id)获取到对应的object。其实现方法是依赖注入(DI, Dependency Injection)
    - 2a) 资源不由使用资源的双方管理而由不使用资源的第三方管理从而降低了使用资源的双方的耦合度。
    - 2b) 资源的配置集中在配置文件里而不是写在应用代码里。
- 容器
    - IOC底层原理
        - 1）xml解析、工厂模式、反射
        ```xml
        <!-- 第一步: xml配置文件, 配置创建的Bean对象 -->
        <bean id="dao" class="com.atguigu.UserDao"></bean>
        ```
        
        ```java
        // 第二步: 用工厂模式来解耦
        class UserFactory {
            public static UserDao getDao() {
                String classValue = xml的class属性;
                Class clazz = Class.forName(classValue); // 反射创建对象
                return (UserDao) clazz.newInstance();
            }
        }
        ```
    - > BeanFactory和ApplicationContext谁才是Spring IoC容器？BeanFactory和 ApplicationContext有什么区别/相同？
        - 1\) 都是。BeanFactory是Spring 底层 IoC 容器; ApplicationContext extend了BeanFactory，提供 IoC 容器以外更多的功能, 例如: AOP
        - 2a) BeanFactory是Spring 底层 IoC 容器; ApplicationContext extend了BeanFactory，提供 IoC 容器以外更多的功能, 例如: AOP
        - 2b) BeanFactroy是以延迟加载形式来注入Bean的，只有在调用getBean()时才对该Bean进行加载实例化。ApplicationContext是在容器启动时，一次性创建了所有的Bean。
        - 题外话: web项目更多的使用ApplicationContext因为我们更希望加载对象的cost都位于项目启动的时候
    - ApplicationContext接口的实现类
        - ```java
        //加载src/文件夹下的xml配置
        ApplicationContext fsx = new ClassPathXmlApplicationContext("bean.xml");
        ```
        - ```java
        //根据路径加载xml配置
        ApplicationContext fsx = new FileSystemXmlApplicationContext("D:/project/bean.xml");
        ```
- Bean对象 的 属性注入
    - > IoC和DI的区别?
        - DI不完全等同于IoC，DI是IoC这个设计思想的一种实现方式。
    - > 什么是Spring的依赖注入(DI)？有哪些不同类型的IOC(DI)方式, 好处？
        - 1\) 依赖: bean对象的创建依赖于容器; 注入: bean对象中所有的属性由容器来注入, 例如: constructor/set method
        - 2a) 构造器注入: contructor有参数, xml的<bean>tag里面```<constructor-arg name="argumentName" value="myValue">```; 好处: 组件不可变, 依赖不为空
        - 2b) set注入: constructor没有参数有setter(), xml的<bean>tag里面```<property name="attributeName" value="myValue">``` 好处: 灵活简便
    - set注入
        - (1)创建类，定义attributes和对应的setters
          ```java
          public class Book {
              //attributes
              private String name;
              private String author;

              //setters
              public void setAuthor(String author) {
                  this.author = author;
              }
              public void setName(String name) {
                  this.name = name;
              }

              public void testBook() {
                  System.out.println("<<"+name+">> by "+author);
              }
          }
          ```
        - (2) set方法注入属性
          ```xml
          <bean id="book1" class="com.atguigu.spring5.Book">
              <!-- 使用property完成属性注入
                      name：类里面attribute的名称
                      value：向属性注入的值
              -->
              <property name="author" value="Craig Walls"></property>
              <property name="name" value="Spring in action"></property>
          </bean>
          ```
        - (3) 使用
          ```java
          @Test
          public void testBook() {
              ApplicationContext cpx = new ClassPathXmlApplicationContext("bean1.xml");
              Book book1 = cpx.getBean("book1", Book.class);
              System.out.println(book1);
              book1.testBook();
          }
          // com.atguigu.spring5.Book@55634720
          // <<Spring in action>> by Craig Walls
          ```
    - constructor注入
        - (1)创建类，定义attributes和对应的有参数的constructor
          ```java
          public class Orders {
              // attributes
              private String name;
              private String address;
              // constructor with arguments for attributes
              public Orders(String name, String address) {
                  this.name    = name;
                  this.address = address;
              }

              public void testOrder() {
                  System.out.println(name+" made in "+address);
              }
          }
          ```
        - (2) constructor注入属性
          ```xml
          <bean id="order1" class="com.atguigu.spring5.Orders">
              <constructor-arg name="name" value="Computer"></constructor-arg>
              <constructor-arg name="address" value="China"></constructor-arg>
          </bean>
          ```
        - (3) 使用
          ```java
          @Test
          public void testOrder() {
              ApplicationContext cpx = new ClassPathXmlApplicationContext("bean1.xml");
              Orders order1 = cpx.getBean("order1", Orders.class);
              System.out.println(order1);
              order1.testOrder();
          }
          // com.atguigu.spring5.Orders@130c12b7
          // Computer made in China
          ```
## spring mvc

## spring boot

## spring cloud
