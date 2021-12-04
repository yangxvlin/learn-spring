# learn-spring
<!-- TOC -->

- [learn-spring](#learn-spring)
    - [resource:](#resource)
        - [book](#book)
        - [video](#video)
    - [spring](#spring)
        - [spring IOC](#spring-ioc)
        - [spring AOP](#spring-aop)
        - [spring jdbc](#spring-jdbc)
    - [spring mvc](#spring-mvc)
    - [spring boot](#spring-boot)
    - [spring cloud](#spring-cloud)

<!-- /TOC -->
## resource:
### book
1. spring in action 5th edition
### video
1. [尚硅谷Spring5框架教程（idea版）](https://www.bilibili.com/video/BV1Vf4y127N5)
2. [尚硅谷SpringMVC 2021新版教程丨一套快速上手spring mvc](https://www.bilibili.com/video/BV1Ry4y1574R)
3. [尚硅谷雷神SpringBoot2零基础入门springboot全套完整版（spring boot2）](https://www.bilibili.com/video/BV19K4y1L7MT)
4. [尚硅谷SpringCloud框架开发教程(SpringCloudAlibaba微服务分布式架构丨Spring Cloud)](https://www.bilibili.com/video/BV18E411x7eT)

## spring
### spring IOC
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
        - setter注入
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
    - 注入其他类型的属性
        - 一个attribute为null
            ```xml
            <property name="address"> <null/> </property>
            ```
        - 属性值包含特殊符号
            ```xml
            <!--属性值包含特殊符号 
                1 把<>进行转义 &lt; &gt; 
                2 把带特殊符号内容写到CDATA 
            --> 
            <property name="address"> <value><![CDATA[<<南京>>]]></value> </property>
            ```
        - 一个attribute的type为Object
            - 外部bean
                ```xml
                <bean id="emp" class="com.atguigu.spring5.bean.Emp"> 
                    <!--设置两个普通属性--> 
                    <property name="ename" value="lucy"></property> 
                    <property name="gender" value="女"></property> 
                    <!--设置对象类型属性--> 
                    <property name="dept" ref="dept"> </property> 
                </bean>

                <bean id="dept" class="com.atguigu.spring5.bean.Dept"> 
                    <property name="dname" value="安保部"></property> 
                </bean> 
                ```
            - 内部bean
                ```xml
                <bean id="emp" class="com.atguigu.spring5.bean.Emp"> 
                    <!--设置两个普通属性--> 
                    <property name="ename" value="lucy"></property> 
                    <property name="gender" value="女"></property> 
                    <!--设置对象类型属性--> 
                    <property name="dept"> 
                        <bean id="dept" class="com.atguigu.spring5.bean.Dept"> 
                            <property name="dname" value="安保部"></property> 
                        </bean> 
                    </property> 
                </bean>
                ```
        - 一个attribute的type为```Array, List, Map, Set```
            - 1\) java类
                ```java
                public class Stu {
                    // 1 数组类型属性
                    private String[] courses;
                    // 2 list集合类型属性
                    private List<String> list;
                    // 3 map集合类型属性
                    private Map<String,String> maps;
                    // 4 set集合类型属性
                    private Set<String> sets;
                    // 5 collection of Java Object属性
                    private List<Course> courseList;

                    public void setCourses(String[] courses) { this.courses = courses; }
                    public void setList(List<String> list) { this.list = list; }
                    public void setMaps(Map<String, String> maps) { this.maps = maps; }
                    public void setSets(Set<String> sets) { this.sets = sets; }
                    public void setCourseList(List<Course> courseList) { this.courseList = courseList; }

                    @Override
                    public String toString() {
                        return "Stu{" +
                                "courses=" + Arrays.toString(courses) +
                                ", list=" + Arrays.toString(list.toArray()) +
                                ", maps=" + Arrays.toString(maps.entrySet().toArray()) +
                                ", sets=" + Arrays.toString(sets.toArray()) +
                                ", courseList=" + Arrays.toString(courseList.toArray()) +
                                '}';
                    }
                }
                public class Course {
                    private String name;
                    private double points;

                    public void setName(String name) { this.name = name; }
                    public void setPoints(double points) { this.points = points; }

                    @Override
                    public String toString() {
                        return "Course{" +
                                "name='" + name + '\'' +
                                ", points=" + points +
                                '}';
                    }
                }
                ```
            - 2\) xml配置
                ```xml
                <bean id="student1" class="com.atguigu.spring5.Stu">
                    <!--1. Array属性注入-->
                    <property name="courses">
                        <array>
                            <value>Java Course</value>
                            <value>Database Course</value>
                        </array>
                    </property>
                    <!--2. List属性注入-->
                    <property name="list">
                        <list>
                            <value>value1</value>
                            <value>value2</value>
                        </list>
                    </property>

                    <!--3. Map属性注入-->
                    <property name="maps">
                        <map>
                            <entry key="key1" value="val1" />
                            <entry key="key2" value="val2" />
                        </map>
                    </property>

                    <!--4. Set属性注入-->
                    <property name="sets">
                        <set>
                            <value>Java</value>
                            <value>Redis</value>
                            <value>Spring</value>
                        </set>
                    </property>

                    <!--5. collection of Java Object属性注入-->
                    <property name="courseList">
                        <list>
                            <ref bean="course1"></ref>
                            <ref bean="course2"></ref>
                        </list>
                    </property>
                </bean>
                <!--5. 创建多个course对象-->
                <bean id="course1" class="com.atguigu.spring5.Course">
                    <property name="name" value="Spring5框架"></property>
                    <property name="points" value="12.5"></property>
                </bean>
                <bean id="course2" class="com.atguigu.spring5.Course">
                    <property name="name" value="MyBatis框架"></property>
                    <property name="points" value="12.5"></property>
                </bean>
                ```
            - 3\) 使用
                ```java
                @Test
                public void testStu() {
                    ApplicationContext cpx = new ClassPathXmlApplicationContext("bean1.xml");
                    Stu stu = cpx.getBean("student1", Stu.class);
                    System.out.println(stu);
                }
                //Stu{courses=[Java Course, Database Course], list=[value1, value2], maps=[key1=val1, key2=val2], sets=[Java, Redis, Spring], courseList=[Course{name='Spring5框架', points=12.5}, Course{name='MyBatis框架', points=12.5}]}
                ```
- > 解释Spring支持的几种bean的作用域
    - 1a) <Bean ... scope="singleton" /> 默认, 每次ApplicationContext.getBean(id)的都是同一个对象(i.e., hashCode()一样)
    - 1b) <Bean ... scope="prototype" /> 每次ApplicationContext.getBean(id)的都是不同的对象(i.e., hashCode()不一样)
    - 1c) request (request结束自动销毁), session(用户会话结束自动销毁), application (全局存在的)
- FactoryBean TODO 看不懂
    - https://juejin.cn/post/6844903954615107597
    - https://blog.csdn.net/weixin_42195284/article/details/109339203
    - https://www.bilibili.com/video/BV1Vf4y127N5?p=15
- Bean的生命周期
    1. 通过无参constructor instantiate bean
    2. setter注入
    3. 把bean instance传递到```postProcessBeforeInitialization()```方法
    4. 调用bean的```initMethod()```(initMethod需要被配置)
    5. 把bean instance传递到```postProcessAfterInitialization()```方法
    6. ApplicationContext.getBean(id)
    7. 当容器关闭时候(```ApplicationContext.close()```)，调用bean的```destroyMethod()```(destroyMethod需要被配置)
    - xml配置
        ```xml
        <bean id="order2" class="com.atguigu.spring5.BeanLifecycle.Order" init-method="initMethod" destroy-method="destroyMethod">
            <property name="name" value="Order: sim card"></property>
        </bean>
        <!-- 注意：当前bean.xml里所有的bean都会被配置这个 -->
        <bean id="myBeanPostProcessor" class="com.atguigu.spring5.BeanLifecycle.MyBeanPostProcessor"></bean>
        ```
    - Java 对象
        ```java
        public class Order {
            public String name;
            public void setName(String name) {
                this.name = name;
                System.out.println("2. setter注入");
            }

            public Order() {
                System.out.println("1. 通过无参constructor instantiate bean");
            }

            public void initMethod() {
                System.out.println("4. 调用bean的initMethod()(initMethod需要被配置)");
            }
            public void destroyMethod() {
                System.out.println("7. 当容器关闭时候(ApplicationContext.close())，调用bean的destroyMethod()(destroyMethod需要被配置)");
            }
        }
        ```
    - postProcessor
        ```java
        public class MyBeanPostProcessor implements BeanPostProcessor {
            @Override
            public Object postProcessBeforeInitialization(Object bean, String beanName) throws BeansException {
                System.out.println("3. 把bean instance传递到postProcessBeforeInitialization()方法");
                return bean;
            }

            @Override
            public Object postProcessAfterInitialization(Object bean, String beanName) throws BeansException {
                System.out.println("5. 把bean instance传递到postProcessAfterInitialization()方法");
                return bean;
            }
        }
        ```
    - 使用
        ```java
        @Test
        public void testBeanLifecycle() {
            ClassPathXmlApplicationContext cpx = new ClassPathXmlApplicationContext("bean2.xml");
            Order order = cpx.getBean("order2", Order.class);
            System.out.println("6. ApplicationContext.getBean(id)");
            System.out.println(order);
            cpx.close();
        }
        ```
- bean的自动装配
    - > 什么是bean的自动装配？
        - 自动装配是满足Bean的依赖关系的一种方式
    - > 解释不同方式的自动装配? 
        - |语法|含义|注意|
          |---|---|---|
          |```<bean ... autowire="byName" />```|自动在容器上下文中查找, 装配和setter参数名一样的bean id。|要保证所有的bean id唯一
          |```<bean ... autowire="byType" />```|自动在容器上下文中查找, 装配和arrirbute类型一样的bean。|要保证所有的bean的class唯一
          |使用注解自动装配
    - > 自动装配有哪些局限性?
        1. 不能自动装配简单的属性，例如primitive
        2. 自动装配不如显式装配精确
- bean的注解装配
    - 1\) import spring-aop.jar
    - 2\) config
        - xml: bean3.xml
          ```xml
          <beans ...
                 xmlns:context="http://www.springframework.org/schema/context"
                 xsi:schemaLocation="...
                                    http://www.springframework.org/schema/context 
                                    http://www.springframework.org/schema/context/spring-context.xsd"
          >
              <context:component-scan base-package="com.atguigu.spring5.annotationBeanDemo">
              </context:component-scan>
            </beans>
          ```
        - annotation: config/SpringConfig.java
          ```java
          @Configuration
          @ComponentScan(basePackages = {"com.atguigu.spring5"})
          public class SpringConfig {
          }
          ```
    - 3\) java class
        - UserService
            ```java
            @Component(value = "userService")
            public class UserService {
                @Autowired
                @Qualifier(value = "userDao")
                private UserDAO userDAO;

                public void add() {
                    System.out.println("UserService add() .........");
                    userDAO.add();
                }

                @Override
                public String toString() {
                    return "UserService{" +
                            "userDAO=" + userDAO +
                            '}';
                }
            }
            ```
        - UserDAO
            ```java
            @Component(value = "userDao")
            public class UserDAO {
                @Value(value = "Peter")
                private String name;

                public void add() {
                    System.out.println("UserDAO add() ......... ");
                }

                @Override
                public String toString() {
                    return "UserDAO{" +
                            "name='" + name + '\'' +
                            '}';
                }
            }
            ```
    - 4\) 使用
        - xml
            ```java
            @Test
            public void testAnnotationBeanDemoXML() {
                ClassPathXmlApplicationContext cpx = new ClassPathXmlApplicationContext("bean3.xml");
                UserService userService = cpx.getBean("userService", UserService.class);
                System.out.println(userService);
                userService.add();
            }
            ```
        - annotation
            ```java
            @Test
            public void testAnnotationBeanDemo() {
                ApplicationContext acac = new AnnotationConfigApplicationContext(SpringConfig.class);
                UserService userService = acac.getBean("userService", UserService.class);
                System.out.println(userService);
                userService.add();
            }
            ```
- > 谈谈@Required, @Autowired, @Qualifier, @Resource注解。
    - 2a) @Required: 表明bean的属性必须在配置的时候设置。若@Required注解的attribute未被设置，容器将抛出BeanInitializationException。
    - 2b) @Autowired: byType给attribute匹配bean
    - 2c) @Qualifier(value="bean id"): 与@Autowired搭配使用, 当@Autowired的class对应多个bean id的时候按照我们指定的匹配。
    - 2d) @Resource(可以value="bean id"): 先是byName给attribute匹配bean, 一个bead id有多个class的话再byName, 不然报错
- @Component, @Repository, @Service, @Controller的区别
    |annotation|meaning|
    |---|---|
    |@Component|最普通的组件，可以被注入到spring容器进行管理
    |@Repository|作用于持久层
    |@Service|作用于业务逻辑层
    |@Controller|作用于表现层（spring-mvc的注解
    [reference](https://www.cnblogs.com/jsoso/p/11243559.html)
### spring AOP
- 什么是AOP?
    - 面向切面编程，利用AOP可以对业务逻辑的各个部分进行隔离，从而使得业务逻辑各部分之间的耦合度降低，提高程序的可重用性，同时提高了开发的效率。
    - 通俗描述：不通过修改源代码方式，在主干功能里面添加新功能
- AOP 底层原理?
    - 有Interface情况，使用JDK动态代理
    - 没有Interface情况，使用CGLIB动态代理
- JDK 动态代理?
    - Proxy.newProxyInstance
        ```java
        /**
         * Returns a proxy instance for the specified interfaces that dispatches method invocations to the specified invocation handler.
         * @params loader     - the class loader to define the proxy class
         * @params interfaces - the list of interfaces for the proxy class to implement
         * @params h          - the invocation handler to dispatch method invocations to
         */
        public static Object newProxyInstance​(ClassLoader loader, Class<?>[] interfaces, InvocationHandler h)
        ```
        [reference](https://docs.oracle.com/en/java/javase/15/docs/api/java.base/java/lang/reflect/Proxy.html#newProxyInstance(java.lang.ClassLoader,java.lang.Class%5B%5D,java.lang.reflect.InvocationHandler))
    - 编写JDK动态代理代码
        1. 创建Interface里面包含被代理的methods
            ```java
            public interface UserDao {
                public int add(int a,int b);
                public String update(String id);
            }
            ```
        2. 创建class来实现interface来被代理
            ```java
            public class UserDaoImpl implements UserDao {
                @Override
                public int add(int a, int b) {
                    return a+b;
                }

                @Override
                public String update(String id) {
                    return id;
                }
            }
            ```
        3. 使用InvocationHandler来代理
            ```java
            class UserDaoInvocationHandler implements InvocationHandler {
                // 1 把创建的是谁的代理对象，把谁传递过来
                // 有参数构造传递
                private Object obj;
                public UserDaoInvocationHandler(Object obj) {
                    this.obj = obj;
                }

                @Override
                public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
                    //方法之前
                    System.out.println("方法之前执行.... "+method.getName()+": 传递的参数:"+ Arrays.toString(args));
                    //被增强的方法执行
                    Object res = method.invoke(obj, args);
                    //方法之后
                    System.out.println("方法之后执行...."+obj);
                    return res;
                }
            }
            ```
        4. 使用
            ```java
            public class JDKProxy {
                public static void main(String[] args) {
                    //创建接口实现类代理对象
                    Class[] interfaces = { UserDao.class };
                    UserDaoImpl userDao = new UserDaoImpl();
                    UserDao dao = (UserDao) Proxy.newProxyInstance(UserDaoImpl.class.getClassLoader(), interfaces, new UserDaoInvocationHandler(userDao));
                    int result = dao.add(1, 2);
                    System.out.println("result: "+result);
                }
            }
            ```
- Aop 名词
    |名词||含义|
    |---|---|---|
    |JoinPoint|连接点|class里面能被增强的method
    |PointCut|切入点|class里面实际被增强的method
    |Advice|通知(增强)|实际被增强的逻辑部分<br/>通知有多个类型:<br/>1. 前置通知（Before）<br/>2. 后置通知（After）<br/>3. 返回通知（After-returning）<br/>4. 异常通知（After-throwing）<br/>5. 环绕通知（Around）
    |Aspect|切面|把通知应用到切入点的过程
- PointCut 切入点表达式
    - 作用：知道对哪个类里面的哪个方法进行增强
    - 语法结构： ```execution([权限修饰符 optional] [返回类型] [类全路径].[方法名称]([参数1的类型], [参数2的类型], ...) )```
        - ```execution(void com.huge.service.impl.AccountServiceImpl.save())```
        - ```execution(* com.atguigu.dao.BookDao.add(..))```
            - 对com.atguigu.dao.BookDao类里面的add进行增强
        - ```execution(* com.atguigu.dao.BookDao.* (..))```
            - 对com.atguigu.dao.BookDao类里面的所有的方法进行增强
        - ```execution(* com.atguigu.dao.*.* (..))```
            - 对com.atguigu.dao包里面所有类，类里面所有方法进行增强
- Aop 代码
    1. dependency
        ```
        commons-logging-1.1.1.jar
        com.springsource.net.sf.cglib-2.2.0.jar
        com.springsource.org.aopalliance-1.0.0.jar
        com.springsource.org.aspectj.weaver-1.6.8.RELEASE.jar
        spring-framework-5.2.9.RELEASE/libs/spring-aop-5.2.9.RELEASE.jar
        spring-framework-5.2.9.RELEASE/libs/spring-aspects-5.2.9.RELEASE.jar
        spring-framework-5.2.9.RELEASE/libs/spring-beans-5.2.9.RELEASE.jar
        spring-framework-5.2.9.RELEASE/libs/spring-context-5.2.9.RELEASE.jar
        spring-framework-5.2.9.RELEASE/libs/spring-core-5.2.9.RELEASE.jar
        spring-framework-5.2.9.RELEASE/libs/spring-expression-5.2.9.RELEASE.jar
        ```
    2. 创建普通的类并在类里面定义方法
        ```java
        @Component(value = "user")
        public class User {
            public void add() {
                System.out.println("add.......");
            }
        }
        ```
    3. 创建增强类(编写增强逻辑): 在增强类里面，创建方法，让不同方法代表不同通知类型
        ```java
        @Component
        @Aspect
        public class UserAdvice {
            // 相同切入点抽取
            @Pointcut(value = "execution(* com.atguigu.spring5.aop_annotation.User.add(..))")
            public void pointdemo() {  }

            //前置通知
            @Before(value = "pointdemo()")
            public void before() {
                System.out.println("before......");
            }
            //返回通知
            @AfterReturning(value = "execution(* com.atguigu.spring5.aop_annotation.User.add(..))")
            public void afterReturning() {
                System.out.println("afterReturning.........");
            }
            //后置通知
            @After(value = "execution(* com.atguigu.spring5.aop_annotation.User.add(..))")
            public void after() {
                System.out.println("after.........");
            }
            //异常通知
            @AfterThrowing(value = "execution(* com.atguigu.spring5.aop_annotation.User.add(..))")
            public void afterThrowing() {
                System.out.println("afterThrowing.........");
            }
            //环绕通知
            @Around(value = "execution(* com.atguigu.spring5.aop_annotation.User.add(..))")
            public void around(ProceedingJoinPoint proceedingJoinPoint) throws Throwable {
                System.out.println("环绕之前.........");
                //被增强的方法执行
                proceedingJoinPoint.proceed();
                System.out.println("环绕之后.........");
            }
        }
        ```
    4. 配置
        - 使用注解开发 不需要xml
            ```java
            @Configuration
            @ComponentScan(basePackages = {"com.atguigu.spring5.aop_annotation"}) // 开启annotation 扫描
            @EnableAspectJAutoProxy(proxyTargetClass = true) // 开启Aspect生成代理对象
            public class AopConfig {
            }
            ```
        - 使用xml
            ```xml
            <beans ...
                   xmlns:aop="http://www.springframework.org/schema/aop"
                   xsi:schemaLocation="...
                                    http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop.xsd">
                <!--创建对象-->
                <bean id="user" class="com.atguigu.spring5.aop_annotation.User"></bean>
                <bean id="userAdvice" class="com.atguigu.spring5.aop_annotation.UserAdvice"></bean>

                <!--配置aop增强-->
                <aop:config>
                    <!--切入点-->
                    <aop:pointcut id="p" expression="execution(* com.atguigu.spring5.aop_annotation.User.add(..))"/>
                    <!--配置切面-->
                    <aop:aspect ref="userAdvice">
                        <!--增强作用在具体的方法上-->
                        <aop:before method="before" pointcut-ref="p"/>
                    </aop:aspect>
                </aop:config>
            </beans>
            ```
    5. 运行
        - 结果
            ```
            com.atguigu.spring5.aop_annotation.User@63a270c9
            环绕之前.........
            before......
            add.......
            afterReturning.........
            after.........
            环绕之后.........

            Process finished with exit code 0
            ```
        - 注解
            ```java
            @Test
            public void testAnnotationAop() {
                ApplicationContext acac = new AnnotationConfigApplicationContext(AopConfig.class);
                User user = acac.getBean("user", User.class);
                System.out.println(user);
                user.add();
            }
            ```
        - xml
            ```java
            @Test
            public void testXMLAop() {
                ClassPathXmlApplicationContext cpx = new ClassPathXmlApplicationContext("bean4.xml");
                User user = cpx.getBean("user", User.class);
                System.out.println(user);
                user.add();
            }
            ```
### spring jdbc
## spring mvc

## spring boot

## spring cloud
