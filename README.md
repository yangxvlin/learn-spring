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
## spring mvc

## spring boot

## spring cloud
