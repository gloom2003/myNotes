# Spring-01

## java文件编译后文件的存储知识

**类加载路径**：即target/class目录，**对应**src/main/**java目录**和**Recourses目录**

src/main/**java目录**下面的代码编译成.class字节码文件后就存放在target/class目录中，例如：

**src/main/java**/com/sg/Main.java文件对应

​	 **target/class**/com/sg/Main.class文件

**Recourses目录下面的资源文件编译后也会存放到target/class目录中**。

在**单元测试中的类加载路径**为target/test-classes，**对应test/java目录**

## 1.Spring简介

​	 Spring是一个开源框架，它由[Rod Johnson](https://baike.baidu.com/item/Rod Johnson)创建。它是为了解决企业应用开发的复杂性而创建的。 

​	 目前是JavaEE开发的灵魂框架。他可以简化JavaEE开发，可以非常方便整合其他框架，无侵入的进行功能增强。

​	 **Spring的核心就是 控制反转(IoC)和面向切面(AOP) 。**

## 2.IOC控制反转

### 2.1 概念

​	控制反转，之前对象的控制权在类手上，现在反转后到了Spring手上。

### 2.2 入门案例

#### ①导入依赖

导入SpringIOC相关依赖

~~~~xml
        <dependency>
                <!--
        ioc依赖被spring-context依赖了（依赖传递），会自动导入
    -->
            <groupId>org.springframework</groupId>
            <artifactId>spring-context</artifactId>
            <version>5.1.9.RELEASE</version>
        </dependency>
~~~~

#### ②编写配置文件

在resources目录下创建applicationContext.xml文件(XML Configuration File中选择Spring Config)，文件名可以任意取。但是建议叫applicationContext。

内容如下：

~~~~xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

    <!--
        classs:配置类的全类名
        id:配置一个唯一标识
    -->
    <bean class="com.sangeng.dao.impl.StudentDaoImpl" id="studentDao"  >
    </bean>


</beans>
~~~~

#### ③创建容器从容器中获取对象并测试

~~~~java
    public static void main(String[] args) {

//        1.获取StudentDaoImpl对象
        //创建Spring容器，指定要读取的配置文件路径(会读取配置文件然后根据配置文件中的全类名使用反射创建对象，作为value存储在Map中，配置中的id作为key)
        ApplicationContext app = new ClassPathXmlApplicationContext("applicationContext.xml");
        //从容器中获取对象(传入key后从Map中获取value对象)
        StudentDao studentDao = (StudentDao) app.getBean("studentDao");
        //调用对象的方法进行测试
        System.out.println(studentDao.getStudentById(1));
    }
~~~~

### 2.3 Bean的常用属性配置

#### 2.3.1 id 

​	bean的唯一标识，同一个Spring容器中不允许重复，**使用name属性设置Bean的名称，没有设置时Bean的名称默认为id的值。**

#### 2.3.2 class

​	全类名，用于反射创建对象	

#### 2.3.3 scope 

​	scope主要有两个值：singleton(单例)和prototype(多例)

**singleton**: 1.**单例** 2.**bean创建时机**：**默认**容器创建的时候就会创建该对象（还可以修改）,读取配置文件的class属性使用反射创建对象并且以id为key存储到map中,后面不会创建对象了，所以是单例。

**prototype**：1.**多例** 2.**bean创建时机**：当调用getBean("studentDao")方法时才会读取配置文件的class属性使用反射创建对象并且以id为key存储到map中，当getBean("studentDao")方法再次被调用时，会重复上面的步骤，把创建出来的对象覆盖原来map中key为id的value,以此实现多例。

## 3.DI依赖注入 (属性注入)

​	依赖注入可以理解成IoC的一种应用场景，**反转**的是对象间依赖关系维护权（**属性的维护权**）。

​	What：从ioc容器中获取的对象中的属性默认是没有赋值的，是使用空参构造器创建的，DI依赖注入的作用就是让spring自动给这些属性进行赋值。

### 3.1 set方法注入

在要注入属性的bean标签中进行配置。**前提是该类有提供属性对应的set方法。**

~~~xml
	<bean class="com.sangeng.domain.Dog" id="dog">
        <property name="name" value="小白"></property>
        <property name="age" value="6"></property>
    </bean>

    <bean class="com.sangeng.domain.Student" id="student" >
        <!--
            name属性用来指定要设置哪个属性
            value属性用来设置要设置的值
            ref属性用来给引用类型的属性设置值，可以写上Spring容器中bean的id
        -->
        <property name="name" value="东南枝"></property>
        <property name="age" value="20"></property>
        <property name="id" value="1"></property>
        <property name="dog" ref="dog"></property>
    </bean>
~~~

### 3.2 有参构造注入

在要注入属性的bean标签中进行配置。**前提是该类有提供对应的有参构造。**

~~~~java
public class Student {

    private String name;
    private int id;
    private int age;

    private Dog dog;

    public Student(String name, int id, int age, Dog dog) {
        this.name = name;
        this.id = id;
        this.age = age;
        this.dog = dog;
    }
    //.....省略其他
}
~~~~

~~~~xml
    <!--使用有参构造进行注入,容器中可以注入同一个类的对象，id设置的不同就行-->
    <bean class="com.sangeng.domain.Student" id="student2" >
        <constructor-arg name="name" value="自挂东南枝"></constructor-arg>
        <constructor-arg name="age" value="20"></constructor-arg>
        <constructor-arg name="id" value="30"></constructor-arg>
        <constructor-arg name="dog" ref="dog"></constructor-arg>
    </bean>
~~~~



### 3.3 复杂类型属性的注入

实体类如下：

~~~~java
@Data
@NoArgsConstructor
@AllArgsConstructor
public class User {
    private int age;
    private String name;
    private Phone phone;
    private List<String> list;
    private List<Phone> phones;
    private Set<String> set;
    private Map<String, Phone> map;
    private int[] arr;
    private Properties properties;
}
~~~~

~~~~java
@Data
@NoArgsConstructor
@AllArgsConstructor
public class Phone {
    private double price;
    private String name;
    private String password;
    private String path;

}
~~~~



配置如下：完全可以根据idea的提示配合猜测完成

~~~~xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean class="com.sangeng.domain.Phone" id="phone">
        <property name="price" value="3999"></property>
        <property name="name" value="黑米"></property>
        <property name="password" value="123"></property>
        <property name="path" value="qqqq"></property>
    </bean>
    
    <bean class="com.sangeng.domain.User" id="user">
        <property name="age" value="10"></property>
        <property name="name" value="大队长"></property>
        <property name="phone" ref="phone"></property>
        <!--对list<String> list进行赋值-->
        <property name="list">
            <list>
                <value>三更</value>
                <value>西施</value>
            </list>
        </property>

        <property name="phones">
            <list>
                <ref bean="phone"></ref>
            </list>
        </property>

        <property name="set">
            <set>
                <value>setEle1</value>
                <value>setEle2</value>
            </set>
        </property>

        <property name="map">
            <map>
                <entry key="k1" value-ref="phone"></entry>
                <entry key="k2" value-ref="phone"></entry>
            </map>
        </property>

        <property name="arr">
            <array>
                <value>10</value>
                <value>11</value>
            </array>
        </property>

        <property name="properties">
            <props>
                <prop key="k1">v1</prop>
                <prop key="k2">v2</prop>
            </props>
        </property>
    </bean>
</beans>
~~~~

### 3.4 SPEL表达式 另一种写法

​	我们可以再配置文件中使用SPEL表达式。写法如下:

~~~~xml
 			<!--可以进行运算-->
<property name="age" value="#{20*3}"/>
			<!--使用value属性赋值其他对象-->
<property name="car" value="#{car}"/>
~~~~

​	注意：SPEL需要写到value属性中，不能写到ref属性。

## 4.Lombok的使用

### ①导入依赖

~~~~xml
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <version>1.18.16</version>
        </dependency>
~~~~

### ②增加注解

~~~~java
@Data //根据属性生成set，get方法，toString,equal,hashCode
@NoArgsConstructor //生成空参构造
@AllArgsConstructor //生成全参构造
public class Phone {
    private double price;
    private String name;
    private String password;
    private String path;

}
~~~~

### 3.安装插件

直接搜索名称：lombok,安装了才能够正常使用

## 5.配置文件

### 5.1 读取properties文件

​	我们可以让Spring读取properties文件中的key/value，然后使用其中的值。

#### ①设置读取properties

在Spring配置文件中加入如下标签：指定要读取的文件的路径。

~~~~xml
<context:property-placeholder location="classpath:filename.properties">
~~~~

其中的classpath表示类加载路径(targer/classes)下。

我们也会用到如下写法：classpath:**.properties  其中的*  * 表示文件名任意。

**注意：context命名空间的引入是否正确**

~~~xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       <!--context标签的命名空间-->
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/context https://www.springframework.org/schema/context/spring-context.xsd">
    <context:property-placeholder location="classpath:filename.properties"></context:property-placeholder>
~~~



#### ②使用配置文件中的值

在我们需要使用的时候可以**使用${key}**来表示具体的值。注意要在value属性中使用才可以。例如：

~~~~xml
<!--表示读取filename.properties文件中key为jdbc.username的value值-->
<property name="propertyName" value="${jdbc.username}"/>
~~~~

### 5.2 引入Spring配置文件

​	我们可以在**主的配置文件**中通过import标签的resource属性，引入其他的xml配置文件,防止项目过大时使用一个xml配置文件，导致难以进行管理。

~~~~xml
<import resource="classpath:jdbc.xml"/>
~~~~

## 6. 低频知识点

### 6.1 bean的配置

#### 6.1.1 name属性

​	我们可以用name属性来给bean取名。多个别名使用逗号进行分隔。例如：

~~~~xml
    <bean class="com.alibaba.druid.pool.DruidDataSource" id="dataSource" name="dataSource2,dataSource3">
        <property name="driverClassName" value="${jdbc.driver}"></property>
        <property name="url" value="${jdbc.url}"></property>
        <property name="username" value="${jdbc.username}"></property>
        <property name="password" value="${jdbc.password}"></property>
    </bean>
~~~~

​	获取的时候就可以使用这个名字来获取了

~~~~java
    public static void main(String[] args) {

        ApplicationContext app = new ClassPathXmlApplicationContext("applicationContext.xml");
        DruidDataSource dataSource = (DruidDataSource) app.getBean("dataSource3");
        System.out.println(dataSource);

    }
~~~~



#### 6.1.2 lazy-init 懒加载

​	可以控制bean的创建时机，如果设置为true就是在第一次获取该对象的时候才去创建(**虽然还是单例，但是bean的创建时机改变了**)。

~~~~xml
    <bean class="com.alibaba.druid.pool.DruidDataSource" lazy-init="true"  id="dataSource" name="dataSource2,dataSource3">
        <property name="driverClassName" value="${jdbc.driver}"></property>
        <property name="url" value="${jdbc.url}"></property>
        <property name="username" value="${jdbc.username}"></property>
        <property name="password" value="${jdbc.password}"></property>
    </bean>
~~~~



#### 6.1.3 init-method

​	可以用来设置初始化方法，设置完后容器创建完对象就会自动帮我们调用对应的方法。

~~~~java
@Data
@NoArgsConstructor
@AllArgsConstructor
public class Student {

    private String name;
    private int id;
    private int age;
	//初始化方法
    public void init(){
        System.out.println("对学生对象进行初始化操作");
    }

}

~~~~

~~~~xml
<bean class="com.sangeng.domain.Student" id="student" init-method="init"></bean>
~~~~

**注意：配置的初始化方法只能是空参的。**



#### 6.1.4 destroy-method

​	可以用来设置此对象销毁之前调用的方法，用于释放资源，设置完后容器销毁对象前就会自动帮我们调用对应的方法。

调用容器的close()方法来关闭容器:

~~~java
        ApplicationContext applicationContext = new ClassPathXmlApplicationContext("applicationContext.xml");
        ((ClassPathXmlApplicationContext) applicationContext).close();
~~~





~~~~xml
    <bean class="com.sangeng.domain.Student" id="student"  destroy-method="close"></bean>
~~~~

~~~~java
@Data
@NoArgsConstructor
@AllArgsConstructor
public class Student {

    private String name;
    private int id;
    private int age;

    public void init(){
        System.out.println("对学生对象进行初始化操作");
    }

    public void close(){
        System.out.println("对象销毁之前调用，用于释放资源");
    }
}

~~~~

**注意：配置的方法只能是空参的。**



#### 6.1.5 factory-bean & factory-method

​	当我们需要让Spring容器使用工厂类的工厂方法来创建对象放入Spring容器的时候，可以使用factory-bean和factory-method属性。



##### 6.1.5.1 配置实例工厂创建对象

配置文件中进行配置

~~~~xml
    <!--创建实例工厂-->
    <bean class="com.sangeng.factory.CarFactory" id="carFactory"></bean>
    <!--使用实例工厂创建Car放入容器-->
    <!--factory-bean 用来指定使用哪个工厂对象-->
    <!--factory-method 用来指定使用哪个工厂方法-->
    <bean factory-bean="carFactory" factory-method="getCar" id="car"></bean>
~~~~

等价于这段代码：

~~~java
CarFactory carFactory = new CarFactory();
Car car = carFactory.getCar();
~~~

创建容器获取对象测试

~~~~java
        ClassPathXmlApplicationContext app = new ClassPathXmlApplicationContext("applicationContext.xml");
        //获取car对象
        Car c = (Car) app.getBean("car");
        System.out.println(c);
~~~~



##### 6.1.5.2 配置静态工厂创建对象

配置文件中进行配置

~~~~xml
    <!--使用静态工厂创建Car放入容器-->
    <bean class="com.sangeng.factory.CarStaticFactory" factory-method="getCar" id="car2"></bean>
~~~~

等价于这段代码：

~~~java
Car car = CarStaticFactory.getCar();
~~~

创建容器获取对象测试

~~~~java
        ClassPathXmlApplicationContext app = new ClassPathXmlApplicationContext("applicationContext.xml");
        //获取car对象
        Car c = (Car) app.getBean("car2");
        System.out.println(c);
~~~~

# Spring-02

## 1.注解开发

​	为了简化配置，Spring支持使用注解代替xml配置。

## 2.Spring常用注解

### 2.0 注解开发准备工作

​	如果要使用注解开发必须要开启组件扫描，这样**加了注解的类才会被识别出来**。Spring才能去解析其中的注解。

~~~~xml
<!--启动组件扫描，指定对应扫描的包路径，该包及其子包下所有的类都会被扫描，加载包含指定注解的类-->
<context:component-scan base-package="com.sangeng"/>
~~~~

### 2.1 IOC相关注解

#### 2.1.1 @Component,@Controller,@Service ,@Repository	

​	上述4个注解都是加到类上的。

​	他们都可以起到类似bean标签的作用。可以把加了该注解类的对象放入Spring容器中。

​	实际再使用时选择任意一个都可以。但是后3个注解是语义化注解。

​	如果是Service类要求使用@Service。

​	如果是Dao类(数据访问层)要求使用@Repository

​	如果是Controllerl类(SpringMVC中会学习到)要求使用@Controller

​	如果是Entity类要求使用@Entity?

​	如果是其他类可以使用@Component

### 2.2 DI依赖注入相关注解

​	如果一个bean已经放入Spring容器中了。那么我们可以使用下列注解实现属性注入，让Spring容器帮我们完成属性的赋值。



#### 2.2.1 @Value

​	主要用于String,Integer等可以直接赋值的属性注入。不依赖setter方法，支持SpEL表达式。

​	使用此注解只能获取简单类型的值（8种基本数据类型及其包装类，String,Date）

例如：

~~~~java
@Service("userService")
@Data
@NoArgsConstructor
@AllArgsConstructor
public class UserServiceImpl implements UserService {
    private UserDao userDao;
    
    @Value("199")
    private int num;
    
    @Value("三更草堂")
    private String str;
    
    @Value("#{19+3}")
    private Integer age;


    public void show() {
        userDao.show();
    }
}
~~~~



#### 2.2.2 @AutoWired

​	Spring会给加了该注解的属性**自动注入数据类型相同(或者其子类的对象)的对象**。

例如：

~~~~java
@Service("userService")
@Data
@NoArgsConstructor
@AllArgsConstructor
public class UserServiceImpl implements UserService {

    @Autowired
    private UserDao userDao;

    @Value("199")
    private int num;
    @Value("三更草堂")
    private String str;

    @Value("#{19+3}")
    private Integer age;


    public void show() {
        userDao.show();
    }
}

~~~~



​	**required属性代表这个属性是否是必须的，默认值为true。**

**如果是true的话Spring容器中如果找不到相同类型的对象完成属性注入就会出现异常。**



##### 2.2.2.1 @Autowired与@Resource的区别
@Autowired和@Resource都是用于自动装配Bean的注解，但它们在使用上有一些区别。

来源：@Autowired是Spring的注解，而@Resource是JavaEE的注解。

根据名称或类型匹配：

**@Autowired根据类型进行匹配，如果有多个匹配的Bean，则根据名字进行匹配**（寻找名称等于当前注入属性名称的bean），

还**可以通过@Qualifier注解指定特定的Bean**。

**@Resource首先根据名称进行匹配，如果名称匹配失败，则根据类型进行匹配**。

注解的位置：@Autowired可以放在字段、构造方法、setter方法和任何自定义的带有参数的方法上。@Resource只能放在字段、setter方法和对应的JavaBean属性上。

默认注入方式：@Autowired默认为必要依赖注入，即被注入的Bean必须存在，如果没有匹配的Bean，会抛出异常。@Resource默认为非必要依赖注入，即被注入的Bean可以为空，不会抛出异常。

高级特性：@Autowired支持JSR-330的@Qualifier注解来指定特定的Bean。@Resource可以通过name属性指定特定的Bean。

总的来说，@Autowired是Spring专用的注解，更为灵活，而@Resource是JavaEE的注解，在JavaEE环境下更常用。在Spring环境下，两者的大部分功能是相同的，可以根据实际需求选择使用。



#### 2.2.3 @Qualifier

​	如果相同类型的bean在容器中有多个时，单独使用@AutoWired就不能满足要求，这时候可以再加上@Qualifier来指定bean的名字从容器中获取bean注入。

例如：

~~~~java
    @Autowired
    @Qualifier("userDao2")
    private UserDao userDao;
~~~~



**注意：该直接不能单独使用。单独使用没有作用**

### 2.3 替换xml配置文件的相关注解

#### @Configuration

​	标注在类上，表示当前类是一个配置类。我们可以用注解类来完全替换掉主xml配置文件。

​	**注意：如果使用配置类替换了xml配置，spring容器要使用：AnnotationConfigApplicationContext类进行创建**

例如：

~~~~java
@Configuration
public class ApplicationConfig {
}

    public static void main(String[] args) {
        AnnotationConfigApplicationContext applicationContext = new AnnotationConfigApplicationContext(ApplicationConfig.class);
        User user = (User) applicationContext.getBean("user");
        System.out.println(user);

    }

~~~~



#### @ComponentScan

​	可以用来代替context:component-scan标签来配置组件扫描。

​	basePackages属性(或者value属性)来指定要扫描的包。

​	**注意**：@ComponentScan要加在配置类上。并且在sprnigBoot项目中，当启动类没有配置@ComponentScan时，默认扫描的是启动类所在的包及其子包，如果使用@Configuration定义的配置类A与启动类不在同一个包及其子包下，则@Configuration注解不会生效，A仍然是一个普通类，如果A与启动类在同一个包及其子包下，并且A配置了@ComponentScan("com")注解，则会生效，会对com包进行扫描。

​	

例如：

~~~~java
@Configuration
@ComponentScan(basePackages = "com.sangeng")//指定要扫描的包
public class ApplicationConfig {
}

~~~~



#### @Bean

​	可以用来**代替bean标签**，主要**用于第三方类的注入**。

​	使用：定义一个方法，在方法中创建对应的对象并且作为返回值返回。然后在方法上加上@Bean注解，

##### 第一种写法：

注解的value属性来设置bean的名称，并且根据bean的名称来获取。

例如：

~~~~java
@Configuration
@ComponentScan(basePackages = "com.sangeng")
public class ApplicationConfig {

    @Bean("dataSource")
    public DruidDataSource getDataSource(){
        DruidDataSource druidDataSource = new DruidDataSource();
        druidDataSource.setDriverClassName("com.mysql.jdbc.Driver");
        druidDataSource.setUsername("root");
        druidDataSource.setUrl("jdbc:mysql://localhost:3306/mybatis_db");
        druidDataSource.setPassword("root");
        return druidDataSource;
    }

}
~~~~



##### 第二种写法：

不指定Bean名称，获取时传入类的字节码对象来获取（**注意事项：同一种类型的对象（或者子类）在容器中只有一个，我们可以不设置bean的名称。**）

具体写法如下：

~~~~java
@Configuration
@ComponentScan(basePackages = "com.sangeng")
public class ApplicationConfig {

    @Bean
    public DruidDataSource getDataSource(){
        DruidDataSource druidDataSource = new DruidDataSource();
        druidDataSource.setDriverClassName("com.mysql.jdbc.Driver");
        druidDataSource.setUsername("root");
        druidDataSource.setUrl("jdbc:mysql://localhost:3306/mybatis_db");
        druidDataSource.setPassword("root");
        return druidDataSource;
    }

}
~~~~

获取方式如下：

~~~~java
    public static void main(String[] args) {
        //创建注解容器
        AnnotationConfigApplicationContext app = new AnnotationConfigApplicationContext(ApplicationConfig.class);
		//根据对应类的字节码对象获取,这里传入的是父接口的字节码对象来获取唯一子类的实现类
        DataSource bean = app.getBean(DataSource.class);
        System.out.println(userService);
    }
~~~~





#### @PropertySource

​	可以用来代替context:property-placeholder，让Spring读取指定的properties文件。然后可以使用@Value来获取读取到的值。



​	**使用：在配置类上加@PropertySource注解，注解的value属性来设置properties文件的路径。**

​	**然后在配置类中定义成员变量。在成员变量上使用@Value注解 + ${}来获取读到的值并给对应的成员变量赋值。**



例如：

jdbc.properties的内容为：

~~~~properties
jdbc.driver=com.mysql.jdbc.Driver
jdbc.url=jdbc:mysql://localhost:3306/mybatis_db
jdbc.username=root
jdbc.password=root
~~~~

读取文件并且获取值

~~~~java
@Configuration
@ComponentScan(basePackages = "com.sangeng")
@PropertySource("jdbc.properties")
public class ApplicationConfig {

    @Value("${jdbc.driver}")
    private String driverClassName;
    
    @Value("${jdbc.url}")
    private String url;
    
    @Value("${jdbc.username}")
    private String username;
    
    @Value("${jdbc.password}")
    private String password;


    @Bean
    public DruidDataSource getDataSource(){
        DruidDataSource druidDataSource = new DruidDataSource();
        druidDataSource.setDriverClassName(driverClassName);
        druidDataSource.setUsername(username);
        druidDataSource.setUrl(url);
        druidDataSource.setPassword(password);
        return druidDataSource;
    }

}

~~~~

**注意事项：使用@Value获取读到的properties文件中的值时使用的是${key},而不是#{key}。**

## 3.如何选择 xml与注解

①SSM  

​		自己项目中的类的IOC和DI都使用注解，对第三方jar包中的类，配置组件扫描时使用xml进行配置。

②SpringBoot

​		纯注解开发





# Spring-03

## AOP核心概念

 - Aspect（切面）：是切入点和通知（引介）的结合，即切面类(使用@Aspect进行标识)

- Advice（通知/ 增强）：所谓通知是**指具体增强的代码**

- Pointcut（切入点）：所谓切入点是**指被增强的方法**

**在切面类中设置切点与通知。**

## Aop的使用

​	**SpringAOP:  批量对Spring容器中bean的方法做增强，并且这种增强不会与原来方法中的代码耦合。**

#### 准备工作

##### 添加依赖

需要添加SpringIOC相关依赖和AOP相关依赖。

~~~~xml
        <!--SpringIOC相关依赖-->
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-context</artifactId>
            <version>5.1.9.RELEASE</version>
        </dependency>
        <!--AOP相关依赖-->
        <dependency>
            <groupId>org.aspectj</groupId>
            <artifactId>aspectjweaver</artifactId>
            <version>1.8.13</version>
        </dependency>
~~~~

##### 把相关bean要注入容器中

1.开启组件扫描

~~~~xml
<context:component-scan base-package="com.sangeng"></context:component-scan>
~~~~

或者使用配置类 + @ComponentScan("com.sangeng") **使用这个注解后如何开启AOP注解支持？**

2.加@Service等注解

##### 开启AOP注解支持

在主配置文件applicationContext.xml中：

~~~~xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/context https://www.springframework.org/schema/context/spring-context.xsd http://www.springframework.org/schema/aop https://www.springframework.org/schema/aop/spring-aop.xsd">


    <!--配置组件扫描-->
    <context:component-scan base-package="com.sangeng"></context:component-scan>
    <!--启动AOP注解支持-->
    <aop:aspectj-autoproxy></aop:aspectj-autoproxy>
</beans>
~~~~



## 1:切点表达式execution + 前置通知等 的aop使用

​		可以使用切点表达式来表示要对哪些方法进行增强。



### 1.1写法：**execution([修饰符] 返回值类型 包名.类名.方法名(参数))**

- 访问修饰符可以省略，大部分情况下省略
- 返回值类型、包名、类名、方法名可以使用星号*  代表任意
- 包名与类名之间一个点 . 代表当前包下的类，**两个点 .. 表示当前包及其子包下的类**
- 参数列表可以使用**两个点 .. 表示任意参数**，任意类型的参数列表



**execution(返回值类型 包名.类名.方法名(参数))** 例如：

```java
execution(* com.sangeng.service.*.*(..))   表示com.sangeng.service包下任意类，方法名任意，参数列表任意，返回值类型任意
   
execution(* com.sangeng.service..*.*(..))   表示com.sangeng.service包及其子包下任意类，方法名任意，参数列表任意，返回值类型任意
    
execution(* com.sangeng.service.*.*())     表示com.sangeng.service包下任意类，方法名任意，要求方法不能有参数，返回值类型任意
    
execution(* com.sangeng.service.*.delete*(..))     表示com.sangeng.service包下任意类，要求方法不能有参数，返回值类型任意,方法名要求已delete开头
```

### 1.2使用：

#### 书写切面类

创建一个类，在类上加上@Component和@Aspect

使用@Pointcut注解来指定要被增强的方法

使用@Before注解来给我们的增强代码所在的方法进行标识，并且指定了增强代码是在被增强方法执行之前执行的。

~~~~java
@Component
@Aspect //表示这是一个切面类
public class MyAspect {
		//切点：被增强的方法
//    用Pointcut注解中的属性来指定对哪些方法进行增强
    @Pointcut("execution(* com.sangeng.service.*.*(..))")
    public void pt(){}
	
    //通知：增强的具体代码
    /*
        用@Before注解来指定该方法中是增强的代码，并且是在被增强方法执行前执行的
        @Before的属性写上加了@Pointcut注解的方法: 方法名()
    */
    @Before("pt()")
    public void methodbefore(){
        System.out.println("方法被调用了");
    }

}
~~~~

### 1. 3 通知的分类

- @Before：前置通知,在目标方法执行前执行
- @AfterReturning： 返回后通知，在目标方法执行后执行，如果出现异常不会执行
- @After：后置通知，在目标方法之后执行，无论是否出现异常都会执行 
- @AfterThrowing：异常通知，在目标方法抛出异常后执行

- **@Around：环绕通知，围绕着目标方法执行**

理解不同通知执行时机。（**下面的伪代码是用来理解单个通知的执行时机的，不能用来理解多个通知情况下的执行顺序。如果需要配置多个通知我们会选择使用Around通知，更加的清晰并且好用**）

~~~~java
    public Object test() {
        before();//@Before 前置通知
        try {
            Object ret = 目标方法();//目标方法调用
            afterReturing();//@AfterReturning 返回后通知
        } catch (Throwable throwable) {
            throwable.printStackTrace();
            afterThrowing();//@AfterThrowing 异常通知通知
        }finally {
            after();//@After 后置通知
        }
        return ret;
    }
~~~~



## 2.切点函数@annotation+注解方式 (环绕通知)的aop使用



### 2.1自定义一个注解

~~~java
/**
 * 自定义注解用来标识实现aop增强
 */
@Target(ElementType.METHOD)//只能作用于方法上面
//设置注解保留到代码运行时，这样才能够生效
@Retention(RetentionPolicy.RUNTIME)
public @interface SystemLog {
    //注意：定义了一个属性而不是方法
    String BusinessName();
}

~~~



### 2.2创建一个日志打印切面类

~~~java
@Component
@Aspect//切面注解
@Slf4j//日志打印注解 酸辣粉4j
public class LogAspect {
    //指定切点(增强对象)为被打上这个注解的方法
    @Pointcut("@annotation(com.kana.annotation.SystemLog)")
    public void pt(){

    }
    //使用环绕方法设置通知内容并且设置作用范围
    @Around("pt()")
    public Object around(ProceedingJoinPoint joinPoint) throws Throwable {
        //处理前进行打印
        handleBefore(joinPoint);
        Object res = null;
        try {
            //执行原方法
            res = joinPoint.proceed();
            //处理后进行打印
            handleAfter(res);
        }finally{
            // 结束后换行
            log.info("=======End=======" + System.lineSeparator());
        }
        return res;
    }

    private void handleAfter(Object object) {
        // 打印出参
        log.info("Response       : {}", JSON.toJSONString(object));
    }

    private void handleBefore(ProceedingJoinPoint joinPoint){
        Object[] args = joinPoint.getArgs();
        MethodSignature signature = (MethodSignature) joinPoint.getSignature();
        String classPath = signature.getDeclaringTypeName();
        String methodName = signature.getName();
        //获取http请求封装而成的对象
        ServletRequestAttributes requestAttributes = (ServletRequestAttributes) RequestContextHolder.getRequestAttributes();
        HttpServletRequest request = requestAttributes.getRequest();
        //获取被增强方法的注解对象以及注解内部的信息
        SystemLog systemLog  = getAnnotation(signature);
        String businessName = systemLog.BusinessName();
        log.info("=======Start=======");
        // 打印请求 URL
        log.info("URL            : {}",request.getRequestURL());
        // 打印描述信息
        log.info("BusinessName   : {}",businessName);
        // 打印 Http method
        log.info("HTTP Method    : {}",request.getMethod() );
        // 打印调用 controller 的全路径以及执行方法
        log.info("Class Method   : {}.{}",classPath,methodName);
        // 打印请求的 IP
        log.info("IP             : {}",request.getRemoteHost());
        // 打印请求入参  把对象进行json的序列化之后进行打印
        log.info("Request Args   : {}", JSON.toJSONString(args));
    }

    private SystemLog getAnnotation(MethodSignature signature){
        return signature.getMethod().getAnnotation(SystemLog.class);
    }
}

~~~

### 2.3 设置作用于哪一个方法

~~~java
	@SystemLog(BusinessName = "更新用户信息")//打上注解启动aop
    @PutMapping("/userInfo")
    //@RequestBody 告诉mvc请求头中有json格式的数据并赋值给user对象
    public ResponseResult putUserInfo(@RequestBody User user){
        return userService.putUserInfo(user);
    }
~~~

## 3 获取被增强方法的相关信息

我们实际对方法进行增强时往往还需要获取到被增强代码的相关信息，比如方法名，参数，返回值，异常对象等。

### 3.1 JoinPoint对象 (前置通知等)

​	我们可以在除了环绕通知外的所有通知方法中增加一个**JoinPoint类型**的参数。这个参数封装了被增强方法的相关信息。**我们可以通过这个参数获取到除了异常对象和返回值之外的所有信息。**

例如：

~~~~java
    @Before("pt()")
    public void methodbefore(JoinPoint jp){
        Object[] args = jp.getArgs();//方法调用时传入的参数
        Object target = jp.getTarget();//被代理对象
        MethodSignature signature = (MethodSignature) jp.getSignature();//获取被被增强方法签名封装的对象
        String methodName = signature.getMethod().getName()
        System.out.println(methodName+"方法即将被调用");
    }
~~~~

案例：

需求：要求让所有service包下类的所有方法被调用前都输出全类名，方法名，以及调用时传入的参数

~~~~java
@Component
@Aspect
public class PrintLogAspect {

    //对哪些方法增强
    @Pointcut("execution(* com.sangeng.service..*.*(..))")
    public void pt(){}

    //怎么增强
    @Before("pt()")
    public void printLog(JoinPoint joinPoint){
        //输出 被增强的方法所在的类名 方法名 调用时传入的参数   joinPoint.getSignature().getName()  joinPoint.getArgs()
        MethodSignature signature = (MethodSignature) joinPoint.getSignature();
        //类名
        String className = signature.getDeclaringTypeName();
        //方法名
        String methodName = signature.getName();
        //调用时传入的参数
        Object[] args = joinPoint.getArgs();

        System.out.println(className+"=="+methodName+"======"+ Arrays.toString(args));
    }
}

~~~~

​	如果需要**获取被增强方法中的异常对象或者返回值**则需要在方法参数上增加一个对应类型的参数，并且使用注解的属性进行配置。这样Spring会把你想获取的数据赋值给对应的方法参数。

例如：

~~~~java
    @AfterReturning(value = "pt()",returning = "ret")//使用returning属性指定了把目标方法返回值赋值给下面方法的参数ret
    public void AfterReturning(JoinPoint jp,Object ret){
        System.out.println("AfterReturning方法被调用了");
    }
~~~~

~~~~java
    @AfterThrowing(value = "pt()",throwing = "t")//使用throwing属性指定了把出现的异常对象赋值给下面方法的参数t
    public void AfterThrowing(JoinPoint jp,Throwable t){
        System.out.println("AfterReturning方法被调用了");
    }
~~~~

### 3.2 ProceedingJoinPoint对象（环绕通知）

​	相信你肯定觉得上面的获取方式特别的麻烦难以理解。就可以使用下面这种万能的方法。

​	直接在环绕通知方法中增加一个**ProceedingJoinPoint类型**的参数。这个参数封装了被增强方法的相关信息。

该参数的proceed()方法被调用相当于被增强方法被执行，调用后的返回值就相当于被增强方法的返回值。

例如：

~~~~java
    @Around(value = "pt()")
    public Object around(ProceedingJoinPoint pjp) {
        Object[] args = pjp.getArgs();//方法调用时传入的参数
        Object target = pjp.getTarget();//被代理对象
        // pjp.getSignature()方法返回一个父接口，操作时使用MethodSignature这个实现类进行操作
        MethodSignature signature = (MethodSignature) pjp.getSignature();//获取被被增强方法签名封装的对象
        String methodName = signature.getMethod().getName()
        System.out.println(methodName+"方法即将被调用");
        Object ret = null;
        try {
            ret = pjp.proceed();//ret就是目标方法执行后的返回值
        } catch (Throwable throwable) {
            throwable.printStackTrace();//throwable就是出现异常时的异常对象
        }
        return ret;
    }
~~~~

## 4 xml配置AOP

### ①定义切面类

~~~~java
public class MyAspect {


    public void before(JoinPoint joinPoint){
        System.out.println("before");
    }

//    @AfterReturning(value = "pt()",returning = "ret")
    public void afterReturning(JoinPoint joinPoint,Object ret){
        System.out.println("afterReturning:"+ret);
    }
//    @After("pt()")
    public void after(JoinPoint joinPoint){
        System.out.println("after");
    }

//    @AfterThrowing(value = "pt()",throwing = "e")
    public void afterThrowing(JoinPoint joinPoint,Throwable e){
        String message = e.getMessage();
        System.out.println("afterThrowing:"+message);
    }

    public Object around(ProceedingJoinPoint pjp){
        //获取参数
        Object[] args = pjp.getArgs();
        MethodSignature signature = (MethodSignature) pjp.getSignature();
        Object target = pjp.getTarget();
        Object ret = null;
        try {
            ret = pjp.proceed();//目标方法的执行
            //ret就是被增强方法的返回值
            System.out.println(ret);
        } catch (Throwable throwable) {
            throwable.printStackTrace();
            System.out.println(throwable.getMessage());
        }
//        System.out.println(pjp);
        return ret;
    }
}

~~~~



### ②目标类和切面类注入容器

在切面类和目标类上加是对应的注解。注入如果是使用注解的方式注入容器要记得开启组件扫描。

当然你也可以在xml中使用bean标签的方式注入容器。

~~~~java
@Component//把切面类注入容器
public class MyAspect {
	//..。省略无关代码
}
~~~~

~~~~java
@Service//把目标类注入容器
public class UserService {
	//..。省略无关代码
}
~~~~

### ③配置AOP

~~~~xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/context https://www.springframework.org/schema/context/spring-context.xsd http://www.springframework.org/schema/aop https://www.springframework.org/schema/aop/spring-aop.xsd">

    <!--开启组件扫描-->
    <context:component-scan base-package="com.sangeng"></context:component-scan>

    <!--配置AOP-->
    <aop:config>
        <!--定义切点-->
        <aop:pointcut id="pt1" expression="execution(* com.sangeng.service..*.*(..))"></aop:pointcut>
        <aop:pointcut id="pt2" expression="@annotation(com.sangeng.aspect.InvokeLog)"></aop:pointcut>
        <!--配置切面-->
        <aop:aspect ref="myAspect">
            <aop:before method="before" pointcut-ref="pt1"></aop:before>
            <aop:after method="after" pointcut-ref="pt1"></aop:after>
            <aop:after-returning method="afterReturning" pointcut-ref="pt1" returning="ret"></aop:after-returning>
            <aop:after-throwing method="afterThrowing" pointcut-ref="pt2" throwing="e"></aop:after-throwing>
        </aop:aspect>
    </aop:config>
</beans>
~~~~



## 5 多切面顺序问题 @Order

​	在实际项目中我们可能会存在配置了多个切面并且对同一个方法进行了增强的情况。这种情况下我们很可能需要**控制切面的顺序**。

​	我们在默认情况下Spring有它自己的排序规则。（按照类名排序 abcd...）

​	默认排序规则往往不符合我们的要求，我们需要进行特殊控制。

​	如果是注解方式配置的AOP可以在切面类上加**@Order注解**来控制顺序。**@Order中的属性越小优先级越高。**

​	如果是XML方式配置的AOP,可以通过调整**先后配置顺序**来控制。



例如：

下面这种配置方式就会先使用CryptAspect里面的增强，再使用APrintLogAspect里的增强

~~~~java
@Component
@Aspect
@Order(2)
public class APrintLogAspect {
    //省略无关代码
}
@Component
@Aspect
@Order(1)
public class CryptAspect {
    //省略无关代码
}
~~~~





### 6.4 切换默认动态代理方式

​	有的时候我们需要修改AOP的代理方式。

​	我们可以使用以下方式修改：

如果我们是采用注解方式配置AOP的话：

设置aop:aspectj-autoproxy标签的proxy-target-class属性为true，代理方式就会修改成Cglib

~~~~xml
<aop:aspectj-autoproxy proxy-target-class="true"/>
~~~~



如果我们是采用xml方式配置AOP的话：

设置aop:config标签的proxy-target-class属性为true,代理方式就会修改成Cglib

~~~~xml
<aop:config proxy-target-class="true">
</aop:config>
~~~~





 # Spring-04

## 1.Spring整合Junit 使得可以在单元测试中直接获取并测试容器中的对象

### ①导入依赖

~~~~xml
<!-- junit -->
<dependency>
    <groupId>junit</groupId>
    <artifactId>junit</artifactId>
    <version>4.12</version>
</dependency>
<!-- spring整合junit的依赖 -->
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-test</artifactId>
    <version>5.1.9.RELEASE</version>
</dependency>
~~~~



### ② 编写测试类

在测试类上加上

**@RunWith(SpringJUnit4ClassRunner.class)**注解，指定让测试运行于Spring环境

**@ContextConfiguration注解**，指定Spring容器创建需要的配置文件或者配置类

~~~~java
@RunWith(SpringJUnit4ClassRunner.class)//让测试运行与Spring测试环境
@ContextConfiguration(locations = "classpath:配置文件1.xml")//设置Spring配置文件或者配置类
//@ContextConfiguration(classes = SpringConfig.class)
public class SpringTest {}
~~~~



### ③注入对象进行测试

在测试类中注入要测试的对象，定义测试方法，在其中使用要测试的对象。

~~~~java
@RunWith(SpringJUnit4ClassRunner.class)//让测试运行与Spring测试环境
@ContextConfiguration(locations = "classpath:配置文件1.xml")//设置Spring配置文件或者配置类
//@ContextConfiguration(classes = SpringConfig.class)
public class SpringTest {
    
    // 想测哪个对象，就注入哪个对象
    @Autowired
    private UserService userService;
    
    //定义测试方法
    @Test
    public void testUserService() {
        userService.findById(10);
    }
    
}
~~~~

## 2.Spring整合Mybatis 可以直接从容器中获取mapper的实现类对象

​	我们如果想把Mybatis整合到Spring中需要使用一个整合包**mybatis-spring**

​	官方文档：http://mybatis.org/spring/zh/index.html



### ①导入依赖

~~~~xml
	<!-- spring-jdbc -->
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-jdbc</artifactId>
        <version>5.1.9.RELEASE</version>
    </dependency>

    <!-- mybatis整合到Spring的整合包 -->
    <dependency>
        <groupId>org.mybatis</groupId>
        <artifactId>mybatis-spring</artifactId>
        <version>2.0.4</version>
    </dependency>

    <!--mybatis依赖-->
    <dependency>
        <groupId>org.mybatis</groupId>
        <artifactId>mybatis</artifactId>
        <version>3.5.4</version>
    </dependency>
    <!--mysql驱动-->
    <dependency>
        <groupId>mysql</groupId>
        <artifactId>mysql-connector-java</artifactId>
        <version>5.1.47</version>
    </dependency>

    <!-- druid数据源 -->
    <dependency>
        <groupId>com.alibaba</groupId>
        <artifactId>druid</artifactId>
        <version>1.1.16</version>
    </dependency>

~~~~

### ②往容器中注入整合相关对象

~~~~xml
    <!--读取properties文件-->       <!--或者写：location="classpath*:*.properties"来指定所有的类加载路径，因为在单元测试中的类加载路径为target/test-classes而不是target/classes-->
    <context:property-placeholder location="classpath:jdbc.properties"></context:property-placeholder>
    <!--创建连接池注入容器，相当于mybatis配置文件中的environment标签，配置连接数据库的相关信息-->
    <bean class="com.alibaba.druid.pool.DruidDataSource" id="dataSource">
        <property name="url" value="${jdbc.url}"></property>
        <property name="username" value="${jdbc.username}"></property>
        <property name="password" value="${jdbc.password}"></property>
        <property name="driverClassName" value="${jdbc.driver}"></property>
    </bean>   
<!--spring整合mybatis:注入SqlSessionFactory对象到容器中（SqlSessionFactoryBean实现了FactoryBean这个接口，这个接口非常特殊，实现了这个接口的类注入容器后会自动调用getObject()方法，并且把方法的返回值存放到容器中，这里的返回值就是SqlSessionFactory对象,所以下面再配置好mybatis配置文件的路径与数据源，就能够从容器中使用SqlSessionFactory.openSession()方法获取到SqlSession对象，从而能够使用getMapper()方法获取到接口的实现类对象）-->
    <bean class="org.mybatis.spring.SqlSessionFactoryBean" id="sessionFactory">
        <!--配置连接池-->
        <property name="dataSource" ref="dataSource"></property>
        <!--配置mybatis配置文件的路径-->
        <property name="configLocation" value="classpath:mybatis-config.xml"></property>
    </bean>

    <!--mapper扫描配置，扫描到的mapper对象会被注入Spring容器中（根据mapper的xml映射文件的sql动态代理(JDK)生成mapper的实现类存放到容器中）,相当于mybatis配置文件中的mappers、package标签-->
    <bean class="org.mybatis.spring.mapper.MapperScannerConfigurer" id="mapperScannerConfigurer">
        <property name="basePackage" value="com.sangeng.dao"></property>
    </bean>
~~~~

mybatis配置文件**mybatis-config.xml**如下:

~~~~xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
    <!--也可以为空-->
    <typeAliases>
        <package name="com.sangeng.domain"></package>
    </typeAliases>
</configuration>
~~~~



### ③从容器中获取Mapper对象进行使用

省去了：读取mybatis配置文件、创建工厂、获取会话的过程，可以直接从容器中获取mapper的实现类对象

~~~~java
    @Autowired
    private UserDao userDao;
~~~~

## 3.Spring声明式事务

### 3.1 事务回顾

### 	

#### **3.1.1 事务的概念**

​		**保证一组数据库的操作，要么同时成功，要么同时失败**



#### 3.1.2 四大特性(三更故事记忆法)

- 隔离性
多个事务之间要相互隔离，不能互相干扰

- 原子性
  指事务是一个不可分割的整体，类似一个不可分割的原子
  
- 持久性
指事务一旦被提交，这组操作修改的数据就真的的发生变化了(不会再被回滚了)。即使接下来数据库故障也不应该对其有影响。

- 一致性
  保障事务前后这组数据的状态是一致的。要么都是成功的，要么都是失败的。

### 3.2 实现声明式事务

​		**声明式事务底层是通过AOP实现**

  ​	如果我们自己去对事务进行控制的话我们就需要在原来核心代码的基础上加上事务控制相关的代码（就会耦合在一起）。而在我们的实际开发中这种事务控制的操作也是非常常见的。所以Spring提供了声明式事务的方式让我们去控制事务。

  ​	只要简单的加个注解(或者是xml配置)就可以实现事务控制，不需要事务控制的时候只需要去掉相应的注解即可(解耦了)。

  #### 3.2.1 注解实现

  ##### ①配置事务管理器和事务注解驱动

  在spring的配置文件applicationContext.xml中添加如下配置：

  ~~~~xml
      <!--把事务管理器注入Spring容器，需要配置一个连接池-->
      <bean id="txManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
          <property name="dataSource" ref="dataSource"/>
      </bean>
      <!--开启事务注解驱动，配置使用的事务管理器-->
      <tx:annotation-driven transaction-manager="txManager"/>
  ~~~~

  ##### ②添加注解

  在需要进行事务控制的方法或者类上添加@Transactional注解就可以实现事务控制。

  ~~~~java
      @Transactional
      public void transfer(Integer outId, Integer inId, Double money) {
          //增加
          accoutDao.updateMoney(inId,money);
  //        System.out.println(1/0);
          //减少
          accoutDao.updateMoney(outId,-money);
      }
  ~~~~

  **注意：如果加在类上，这个类的所有方法都会受事务控制，如果加在方法上，就是那一个方法受事务控制。**

  注意，因为声明式事务底层是通过AOP实现的，所以最好把AOP相关依赖都加上。

  ~~~~xml
         <dependency>
              <groupId>org.aspectj</groupId>
              <artifactId>aspectjweaver</artifactId>
              <version>1.9.6</version>
          </dependency>
  ~~~~

  

  #### 3.2.2 xml方式实现

 

  ##### ①配置事务管理器

  ~~~~xml
      <bean id="txManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
          <property name="dataSource" ref="dataSource"/>
      </bean>
  ~~~~

  ##### ②配置事务切面

  ~~~~xml
   	<!--定义事务管理的通知类-->
      <tx:advice transaction-manager="txManager" id="txAdvice">
          <tx:attributes>
              <!--第二次筛选：使用对方法名称进行-->
              <tx:method name="trans*"/>
          </tx:attributes>
      </tx:advice>
  
      <aop:config>
          <!--第一次筛选:使用切点表达式-->
          <aop:pointcut id="pt" expression="execution(* com.sangeng.service..*.*(..))"></aop:pointcut>
          <aop:advisor advice-ref="txAdvice" pointcut-ref="pt"></aop:advisor>
      </aop:config>
  ~~~~

  注意，因为**声明式事务底层是通过AOP实现**的，所以最好把AOP相关依赖都加上。

  ~~~~xml
         <dependency>
              <groupId>org.aspectj</groupId>
              <artifactId>aspectjweaver</artifactId>
              <version>1.9.6</version>
          </dependency>
  ~~~~

### 3.3 属性配置

#### 3.3.1 事务传播行为propagation

​	当**事务方法嵌套调用时**，需要控制是否开启新事务，可以使用事务传播行为来控制。



测试案例:

~~~~java
@Service
public class TestServiceImpl {
    @Autowired
    AccountService accountService;

    @Transactional
    public void test(){
        accountService.transfer(1,2,10D);
        accountService.log();
    }
}
~~~~

~~~~java
public class AccountServiceImpl implements AccountService {
	//...省略其他不相关代码
    @Transactional
    public void log() {
        System.out.println("打印日志");
        int i = 1/0;
    }
    
    // 具体效果：当这个方法没有出现异常，事务正常提交后就不会被回滚了，成功了就是肯定成功了
	@Transactional(propagation = Propagation.REQUIRES_NEW)
    public void transfer(Integer outId, Integer inId, Double money) {
        //增加
        accoutDao.updateMoney(inId,money);
        //减少
        accoutDao.updateMoney(outId,-money);
    }

}
~~~~

测试：

~~~~java
  	@Autowired
    AccountService accountService;  

	@Test
    public void test(){
        //结果：transfer方法成功向数据库写入了数据，没有被log()方法引起的异常回滚。
        accountService.test();
    }
~~~~

@Transactional注解，propagation属性的其他值：

新建：即：使用一个新的连接对象去开启事务(

原来：c1.setAutoCommit(false),c1.commit(),c1.rollback()

新建后: c2.setAutoCommit(false),c2.commit(),c2.rollback() , c2调用commit()方法后，即使c1调用了rollback()方法，c2的提交也不会被回滚。

)

| 属性值                                                   | 行为                                                   |
| -------------------------------------------------------- | ------------------------------------------------------ |
| REQUIRED（必须要有：别人有我就进入别人的，没有就新建）   | 外层方法有事务，内层方法就加入。外层没有，内层就新建   |
| REQUIRES_NEW（必须要有新事务：不管别人有没有，我都新建） | 外层方法有事务，内层方法新建。外层没有，内层也新建     |
| SUPPORTS（支持有：别人有我就进入别人的，没有就摆了）     | 外层方法有事务，内层方法就加入。外层没有，内层就也没有 |
| NOT_SUPPORTED（支持没有:我谁也不加）                     | 外层方法有事务，内层方法没有。外层没有，内层也没有     |
| MANDATORY（强制要求外层有）                              | 外层方法有事务，内层方法加入。外层没有。内层就报错     |
| NEVER(绝不允许有)                                        | 外层方法有事务，内层方法就报错。外层没有。内层就也没有 |

**重点掌握：前三个即可**。

**(内层)事务为什么失效？**可能是事务传播行为propagation设置错误，比如设置为SUPPORTS并且此时外层方法没有设置事务时，内层的事务就会失效。



#### 3.3.2 隔离级别isolation

1. Isolation.DEFAULT 使用数据库默认隔离级别

2. Isolation.READ_UNCOMMITTED 读未提交,存在脏读问题

3. Isolation.READ_COMMITTED 读已提交,解决脏读问题

4. Isolation.REPEATABLE_READ 重复读,解决可能的不可重复读问题

5. Isolation.SERIALIZABLE 序列化,可以避免脏读、不可重复读与幻读。但是这种事务隔离级别效率低下，比较耗数据库性能，一般不使用。

大多数数据库默认的事务隔离级别是Read committed，比如Sql Server , Oracle。

而Mysql的默认隔离级别是Repeatable read。

使用方法：

~~~~java
   @Transactional(propagation = Propagation.REQUIRES_NEW,isolation = Isolation.READ_COMMITTED)
    public void transfer(Integer outId, Integer inId, Double money) {
        //增加
        accoutDao.updateMoney(inId,money);
        //减少
        accoutDao.updateMoney(outId,-money);
    }
~~~~



#### 3.3.3 只读readOnly

​	如果事务中的操作**都是读操作（select）**，没涉及到对数据的写操作可以设置readOnly为true。这样可以提高效率。

~~~~java
    @Transactional(readOnly = true)
    public void log() {
        System.out.println("打印日志");
        int i = 1/0;
    }
~~~~



