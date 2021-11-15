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
    
## spring mvc

## spring boot

## spring cloud
