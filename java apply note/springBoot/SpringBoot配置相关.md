



# SpringBoot配置相关内容

## 1. SpringBoot简介

### 1.1 为什么要学习SpringBoot

​	我们之前的SSM还是使用起来不够爽。

- 还需要**写很多的配置**才能进行正常的使用。
- 实现一个功能需要引入很多的依赖，尤其是要自己去维护依赖的版本，特别容易出现**依赖冲突**等问题。

​	SpringBoot就能很好的解决上述问题。



### 1.2 SpringBoot是什么

​	Spring Boot是**基于Spring**开发的全新框架，相当于**对Spring做了又一层封装**。

​	其设计目的是用来简化Spring应用的初始搭建以及开发过程。该框架使用了特定的方式来进行配置，从而使开发人员不再需要定义样板化的配置。（自动配置）**约定大于配置**。

​	并且对第三方依赖的添加也进行了封装简化。（起步依赖）**解决依赖冲突的问题，不需要自己来指定版本**。（由于版本问题导致某个包的某个类找不到了等）



​	所以Spring能做的他都能做，并且简化了配置。

​	并且还提供了一些Spring所没有的比如：

- 内嵌web容器，不再需要部署到web容器中

  提供准备好的特性，如指标、健康检查和外部化配置 



​	最大特点：**自动配置**、**起步依赖**



​	官网：https://spring.io/projects/spring-boot





## 2. 快速开始

**创建一个空项目，再创建一个maven模块**。再进行相关的配置：

**注意**：其中空项目的命名规则：

Group: com.sangeng

Artifact: spring (不能使用大写)

### 2.1 maven项目转springBoot项目的相关配置

​	springBoot2.5的版本要求：

​	JDK : 8

​    Maven ：3.5.x

#### 在idea中查看与修改使用的JDK版本：

1.在左上角File的Project Structrue中的Project与Model中分别进行设置JDK版本为8。

2.在maven的setting.xml文件中配置编译的jdk版本：

在idea中右击pom.xml文件，选择maven，选择打开Open setting.xml文件进行下面的修改：

~~~~xml
  <profiles>
    <profile>
      <id>jdk-1.8</id>
      <activation>
        <activeByDefault>true</activeByDefault>
        <jdk>1.8</jdk>
      </activation>
      <properties>
        <maven.compiler.source>1.8</maven.compiler.source>
        <maven.compiler.target>1.8</maven.compiler.target>
        <maven.compiler.compilerVersion>1.8</maven.compiler.compilerVersion>
      </properties>
    </profile>
  </profiles>
~~~~
#### Maven配置

~~~~xml
  配置阿里云镜像
<mirrors>
    <mirror>
      <id>aliyunmaven</id>
      <mirrorOf>central</mirrorOf>
      <name>aliyun maven</name>
      <url>https://maven.aliyun.com/repository/public </url>
    </mirror>
  </mirrors>
~~~~
#### 在idea中查看与修改maven的版本

1..在左上角File的setting中搜索maven即可



#### 清理Maven仓库脚本

出现lastUpdated文件，导致不能重新重新下载jar包，使用下面的脚本进行删除:

~~~~
@echo off
rem create by NettQun
  
rem 这里写你的仓库路径
set REPOSITORY_PATH=E:\Develop\maven_rep
rem 正在搜索...
for /f "delims=" %%i in ('dir /b /s "%REPOSITORY_PATH%\*lastUpdated*"') do (
    echo %%i
    del /s /q "%%i"
)
rem 搜索完毕
pause
~~~~

创建一个bat文件，然后复制上述脚本进去，修改其中maven本地仓库的地址，保存后双击执行即可。



### 2.2 转换为项目springBoot项目

①继承父工程

在pom.xml中添加一下配置，继承spring-boot-starter-parent这个父工程，指定springBoot父工程进行**版本锁定**

~~~~xmL
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.5.0</version>
    </parent>
~~~~



②添加依赖

不需要指定版本

~~~~xml
   <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
    </dependencies>
~~~~



③创建启动类

创建一个类在其实加上@SpringBootApplication注解标识为启动类。

~~~~java
@SpringBootApplication
public class HelloApplication {

    public static void main(String[] args) {
        SpringApplication.run(HelloApplication.class, args);
    }
}
~~~~

方法的返回值为:

~~~java
@SpringBootApplication
public class HelloApplication {
    public static void main(String[] args) {
        // 返回一个spring容器
        ConfigurableApplicationContext run = SpringApplication.run(HelloApplication.class, args);
        User user = run.getBean(User.class);
        System.out.println(user);
    }
}
~~~



④定义Controller

创建Controller,**注意：Controller要放在启动类所在包或者其子包下（即与启动类在同一级目录下）。**

~~~~java
@RestController
public class HelloController {
    @RequestMapping("/hello")
    public String hello(){
        return "hello";
    }
}
~~~~



⑤运行测试

直接**运行启动类的main方法**即可。



### 2.3 打包为jar包后运行

​	我们可以把springboot的项目打成jar包直接去运行。

①添加maven插件(不添加的话也可以打成jar包，但是java -jar xxx.jar不能运行,会报错：没有主菜单属性)

~~~~xml
    <build>
        <plugins>
            <!--springboot打包插件-->
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
        </plugins>
    </build>
~~~~

②maven打包

![image-20210528143119257](C:\Users\GLOOM\Desktop\for zip\not system\sangGeng files\需要三连资料\SpringBoot\img\SpringBoot项目打包.png)

③运行jar包

打开cmd，进入jar包所在目录

在jar包所在目录执行命令 

~~~~sh
java -jar jar包名称
~~~~

即可运行。

另外，可以**在运行时指定参数**，并且优先级是高于application.yml配置文件的：

如：application.yml中为：

~~~yaml
server:
	port: 8080
~~~

终端中为：

~~~sh
java -jar jar包名称 --server.port=7777 --sever.name=app
~~~

则应用程序会运行在7777端口上。

### 2.4 直接创建springBoot项目

创建一个空项目，在idea左上角File中选择Project Structure,点击+号创建一个新的模块，选择Spring Initializr填写相关的信息。

url:`start.aliyun.com`

选择springBoot的版本，选择相关的依赖。

### 2.5 yaml多环境配置

参考链接：

https://blog.csdn.net/qq_37992410/article/details/121008415



方式一：多个yml文件
  步骤1：创建多个配置文件

```sh
application.yml      #主配置文件
application-dev.yml  #开发环境的配置
application-prod.yml #生产环境的配置
application-test.yml #测试环境的配置
```

  步骤2：applicaiton.yml中指定配置
在application.yml中选择需要使用的配置文件（当选择的文件和主配置文件application.yml文件存在相同的配置时，application.yml中的配置会被覆盖掉）

```yaml
spring:
 profiles:
   active: dev #需要使用的配置文件的后缀
```

在application.yml主配置文件中做项目通用的配置，在其他配置文件中做不同环境下的配置，以避免重复配置的情况。





## 3.起步依赖

​	SpringBoot依靠父项目中的**版本锁定**和**starter机制**让我们能更轻松的实现对依赖的管理。



### 3.0 依赖冲突及其解决方案 

#### 3.0.1 依赖冲突

​	一般程序在运行时发生类似于 java.lang.ClassNotFoundException，Method not found: '……'，或者莫名其妙的异常信息，这种情况一般很大可能就是 **jar包依赖冲突**的问题引起的了。

​	**出现原因：**由于maven的依赖传递特性，当同时导入A与B时，A依赖C(低版本)，B也依赖C(高版本)。 由于A先导入了C(低版本)，导致B引入C(高版本)后，A与B都一起使用的是C(低版本)，当B想要使用C(高版本)中才有的东西时，在使用的C(低版本)中找不到，就会出现依赖冲突，报找不到类的错误。

​	

#### 3.0.2 解决方案

​	如果出现了类似于 java.lang.ClassNotFoundException，Method not found: 这些异常检查相关的依赖冲突问题，**排除掉低版本的依赖，留下高版本的依赖。**

可以使用maven插件：maven helper的使用，快速查看与解决maven的依赖冲突。

​	

​	

### 3.1 版本锁定

​	我们的SpringBoot模块都需要继承一个父工程：**spring-boot-starter-parent**。在spring-boot-starter-parent的父工程**spring-boot-dependencies**中对常用的依赖进行了**版本锁定**。这样我们在添加依赖时，很多时候都**不需要添加依赖的版本号**了。（尽力避免依赖冲突，但是**没办法完全避免依赖冲突**，自己手动导入两个不同版本的jar包时，肯定会冲突）

​	

​	我们也可以采用**覆盖properties配置**或者**直接指定版本号**的方式修改依赖的版本。

例如：

直接指定版本号

~~~~xml
        <dependency>
            <groupId>org.aspectj</groupId>
            <artifactId>aspectjweaver</artifactId>
            <version>1.7.2</version>
        </dependency>
~~~~

覆盖properties配置

~~~~xml
    <properties>
        <aspectj.version>1.7.2</aspectj.version>
    </properties>
~~~~



### 3.2 starter机制

​	当我们需要使用某种功能时只需要引入对应的starter即可。**一个starter针对一种特定的场景**，其内部引入了该场景所需的依赖。这样我们就不需要单独引入多个依赖了。

​	命名规律

- 官方starter都是以  `spring-boot-starter`开头后面跟上场景名称。例如：spring-boot-starter-data-jpa
- 非官方starter则是以 `场景名-spring-boot-starter`的格式，例如：mybatis-spring-boot-starter

注意：非官方starter在导入时需要指定版本，因为在官方的父工程中没有进行非官方依赖的版本配置





## 4.自动配置（面试类）

​	SpringBoot中**最重要**的特性就是自动配置。

​	Springboot遵循“**约定优于配置**”的原则，自动进行了默认配置。这样我们就不需要做大量的配置。

​	当我们需要使用什么**场景**时，就会**自动配置这个场景相关的配置**。

​	如果他的默认配置不符合我们的需求时修改这部分配置即可。



​	

​	

## 5.YML配置

### 5.1.简介	

#### 5.1.1YML是什么

​		YAML (YAML Ain't a Markup Language)YAML不是一种标记语言，通常以.yml为后缀的文件，是一种直观的能够被电脑识别的数据序列化格式，并且容易被人类阅读，容易和脚本语言交互的，可以被支持YAML库的不同的编程语言程序导入，一种专门用来写配置文件的语言。

​		 YAML试图用一种比XML更敏捷的方式，来完成XML所完成的任务。

​		 例如： 

~~~~yml
student:
    name: sangeng
    age: 15
~~~~



~~~~xml
<student>
    <name>sangeng</name>
    <age>15</age>
</student>
~~~~



#### 5.1.2YML优点

1. YAML易于人们阅读。

2. 更加简洁明了

    

### 5.2.语法

#### 5.2.1约定

- k: v 表示键值对关系，**冒号后面必须有一个空格**

- 使用空格的缩进表示层级关系，空格数目不重要，**只要是左对齐的一列数据，都是同一个层级的**

- 大小写敏感

- 缩进时**不允许使用Tab键，只允许使用空格**。

- java中对于驼峰命名法，可用原名或使用-代替驼峰，如java中的lastName属性,在yml中使用lastName或 last-name都可正确映射。

- yml中注释前面要加#

- java中默认写在Resources目录下面的**application.yml**文件中（**文件名不能改变**，否则报错）

  

#### 5.2.2键值关系

##### 普通值(字面量)

k: v：字面量直接写；

   字符串默认不用加上单引号或者双绰号；

   "": 双引号；转意字符能够起作用

​       name:   "sangeng \n caotang"：输出；sangeng 换行  caotang

   ''：单引号；**会转义特殊字符使其失效**，特殊字符最终只是一个普通的字符串数据

~~~~yml
name1: sangeng 
name2: 'sangeng  \n caotang'
name3: "sangeng  \n caotang"
age: 15
flag: true
~~~~

##### 日期

~~~~yml
date: 2019/01/01
~~~~



##### 对象(属性和值)、Map(键值对)

多行写法：

在下一行来写对象的属性和值的关系，注意缩进 

~~~~yml
student:
  name: zhangsan
  age: 20
~~~~

行内写法：

~~~~yml
student: {name: zhangsan,age: 20}
~~~~

**注意：冒号后面一定有空格！ map中的key不支持中文！**

##### 数组、list、set

 用- 值表示数组中的**一个元素** 

多行写法：

~~~~yml
pets:
  - dog
  - pig
  - cat
~~~~

**注意：- 号后面一定有空格！**

行内写法：

~~~~yml
pets: [dog,pig,cat]
~~~~



##### 对象数组、对象list、对象set

~~~~yml
students:
 - name: zhangsan # - 的后面表示一个元素，这里表示一个对象，有name和age两个属性
   age: 22
 - name: lisi
   age: 20
 - {name: wangwu,age: 18}
~~~~



#### 5.2.3 占位符赋值

可以使用 **${key:defaultValue}** 的方式来赋值，若key不存在，则会使用defaultValue来赋值。

例如

~~~~yml
server:
  port: ${myPort:88}

myPort: 80   
~~~~



### 5.3.SpringBoot读取YML

#### 5.3.1 @Value注解

​	注意使用此注解只能获取简单类型的值（8种基本数据类型及其包装类，String,Date）

~~~~yml
student:
  lastName: sangeng
~~~~

~~~~java
@RestController
public class TestController {
    @Value("${student.lastName}")
    private String lastName;
    
    @RequestMapping("/test")
    public String test(){
        System.out.println(lastName);
        return "hi";
    }
    
}
~~~~

**注意：加了@Value的类必须是交由Spring容器管理的**

#### 5.3.2 @ConfigurationProperties注解

**使用前缀与变量名称拼接形成的结果再从配置文件中进行获取。**

作用：把配置文件中对应前缀的属性**映射**到类中的属性上来

​	yml配置

~~~~yml
student:
  lastName: sangeng
  age: 17
student2:
  lastName: sangeng2
  age: 15
~~~~

​    在类上添加注解@Component  和@ConfigurationProperties(prefix = "配置前缀")

**新创建一个类，用来专门读取配置**：**注意**：这个类要交由Spring容器管理

~~~~java
@Data
@AllArgsConstructor
@NoArgsConstructor
@Component
@ConfigurationProperties(prefix = "student")
public class Student {
    private String lastName;
    private Integer age;
}
~~~~

​	从spring容器中获取Student对象

~~~~java
@RestController
public class TestController {

    @Autowired
    private Student student;
    
    @RequestMapping("/test")
    public String test(){
        System.out.println(student);
        return "hi";
    }
}

~~~~

​	**注意事项：要求对应的属性要有set/get方法，并且key要和成员变量名一致才可以对应的上。**

### 5.4.练习

要求把下面实体类中的各个属性在yml文件中进行赋值。然后想办法读取yml配置的属性值，进行输出测试。

~~~~java
@Data
@AllArgsConstructor
@NoArgsConstructor
public class Student {
    private String lastName;
    private Integer age;
    private Boolean boss;

    private Date birthday;
    private Map<String,String> maps;
    private Map<String,String> maps2;
    private List<Dog> list;

    private Dog dog;
    private String[] arr;
    private String[] arr2;

    private Map<String,Dog> dogMap;
}
@Data
@AllArgsConstructor
@NoArgsConstructor
class Dog {
    private String name;
    private Integer age;
}
~~~~



#### 答案


~~~~~yml
# 练习
student:
  lastName: sangeng
  age: 15
  boss: true
  birthday: 2006/2/3
  maps:
    name: sangeng
    age: 11
  maps2: {name: caotang,age: 199}
  list:
    - name: 小白
      age: 3
    - name: 小黄
      age: 4
    - {name: 小黑,age: 1}
  dog:
    name: 小红
    age: 5
  arr:
    - sangeng
    - caotang

  arr2: [sangeng,caotang]
  dogMap:
    xb: {name: 小白,age: 9}
    xh:
      name: 小红
      age: 6

~~~~~

~~~~java
@Data
@AllArgsConstructor
@NoArgsConstructor
@Component
@ConfigurationProperties(prefix = "student")
public class Student {
    private String lastName;
    private Integer age;
    private Boolean boss;

    private Date birthday;
    private Map<String,String> maps;
    private Map<String,String> maps2;
    private List<Dog> list;

    private Dog dog;
    private String[] arr;
    private String[] arr2;

    private Map<String,Dog> dogMap;
}
@Data
@AllArgsConstructor
@NoArgsConstructor
class Dog {
    private String name;
    private Integer age;
}
~~~~

### 5.5 YML和properties配置的相互转换

​	我们可以使用一些网站非常方便的实现YML和properties格式配置的相互转换。

转换网站：https://www.toyaml.com/index.html



### 5.6 配置提示

​	如果使用了@ConfigurationProperties注解，可以增加以下依赖，让我们在书写配置时有相应的提示。

~~~~xml
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-configuration-processor</artifactId>
            <optional>true</optional>
        </dependency>
~~~~

​	**注意：添加完依赖加完注解后要运行一次程序才会有相应的提示。**





## 6 多模块项目相关

### 6.1 多模块springBoot项目的例子(三更博客)

关于多模块项目，多模块下父项目存在一个`packing`打包类型标签，**所有的父级项目的packing都为pom**，**packing默认是jar类型**，如果不作配置，maven会将该项目打成jar包。作为父级项目，还有一个重要的属性，那就是modules，通过modules标签将项目的所有子项目引用进来，在build父级项目时，会根据子模块的相互依赖关系整理一个build顺序，然后依次build。

配置父工程的pom.xml文件：

~~~xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
   
	<!-- 项目默认的-->
    <groupId>com.sangeng</groupId>
    <artifactId>SGBlog</artifactId>
    <version>1.0-SNAPSHOT</version>
    <modelVersion>4.0.0</modelVersion>
    <!--确保父工程是用pom方式打包的-->
    <packaging>pom</packaging>
    
	<!--把子模块聚合起来，方便进行统一管理(root) （插件：install,clean...），添加子模块时会自动添加-->
    <modules>
        <module>sangeng-framework</module>
        <module>sangeng-admin</module>
        <module>sangeng-blog</module>
    </modules>

    <!-- 统一项目的jdk版本与编码方式-->
    <properties>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <java.version>1.8</java.version>
    </properties>
    
        <!-- 此标签可以进行所有子模块的依赖版本控制-->
    <dependencyManagement>
	<!-- 作用：在父工程中进行非springBoot官方提供的依赖的锁定，从而使使用子模块都不需要指定依赖的版本-->
	<!-- 在dependencyManagement标签下面的dependencies标签并没有真正的导入依赖，只是对子模块的依赖版本进行了锁定-->
    <dependencies>
        <!-- SpringBoot的依赖配置-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-dependencies</artifactId>
            <version>2.5.0</version>
            <!-- 这里进行了很多其他依赖的版本锁定，ctrl进入spring-boot-dependencies.xml即可查看-->
            <type>pom</type>
            <scope>import</scope>
        </dependency>
        <!--fastjson依赖-->
        <dependency>
            <groupId>com.alibaba</groupId>
            <artifactId>fastjson</artifactId>
            <version>1.2.33</version>
        </dependency>
        <!--jwt依赖-->
        <dependency>
            <groupId>io.jsonwebtoken</groupId>
            <artifactId>jjwt</artifactId>
            <version>0.9.0</version>
        </dependency>
        <!--mybatisPlus依赖-->
        <dependency>
            <groupId>com.baomidou</groupId>
            <artifactId>mybatis-plus-boot-starter</artifactId>
            <version>3.4.3</version>
        </dependency>

        <!--阿里云OSS-->
        <dependency>
            <groupId>com.aliyun.oss</groupId>
            <artifactId>aliyun-sdk-oss</artifactId>
            <version>3.10.2</version>
        </dependency>


        <dependency>
            <groupId>com.alibaba</groupId>
            <artifactId>easyexcel</artifactId>
            <version>3.0.5</version>
        </dependency>

        <dependency>
            <groupId>io.springfox</groupId>
            <artifactId>springfox-swagger2</artifactId>
            <version>2.9.2</version>
        </dependency>
        <dependency>
            <groupId>io.springfox</groupId>
            <artifactId>springfox-swagger-ui</artifactId>
            <version>2.9.2</version>
        </dependency>
    </dependencies>


    </dependencyManagement>
    
	<!--这段配置的目的是在构建（编译）过程中使用 maven-compiler-plugin 插件，以 Java 8 版本来编译源代码，同时确保编译过程使用的源代码编码与项目定义的编码一致。这样可以确保构建的一致性和跨平台的便携性。-->
    <build>
        <plugins>
            <plugin>
                 <!--这个插件的功能是编译项目的源代码。-->
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <version>3.1</version>
                <configuration>
                    <!--引入上面设置好的变量，设置jdk版本与编码方式-->
                    <source>${java.version}</source>
                    <target>${java.version}</target>
                    <encoding>${project.build.sourceEncoding}</encoding>
                </configuration>
            </plugin>
        </plugins>
    </build>
</project>
~~~

配置公共子模块的pom.xml文件

~~~xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <!--展示了父工程中pom.xml文件的信息，表示属于哪一个父工程，并且子模块继承了父工程的相关配置，包括（版本锁定、jdk版本、编码格式的统一）-->
    <parent>
        <artifactId>SGBlog</artifactId>
        <groupId>com.sangeng</groupId>
        <version>1.0-SNAPSHOT</version>
    </parent>
    <!--默认信息-->
    <modelVersion>4.0.0</modelVersion>
    <artifactId>sangeng-framework</artifactId>
	<!--公共子模块中的依赖：存放使用模块中都会使用到的依赖，并且都不需要写版本标签来指定版本信息，因为父模块中已经进行了版本锁定-->
    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <!--lombk-->
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <optional>true</optional>
        </dependency>
        <!--junit-->
        <dependency>
             <!--继承了父工程中spring-boot-dependencies依赖的相关配置，在spring-boot-dependencies中进行了spring-boot-starter-test版本的配置，所以这就是即使在父工程中找不到版本锁定也可以不指定版本的原因-->
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
        <!--SpringSecurity启动器-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-security</artifactId>
        </dependency>
        <!--redis依赖-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-data-redis</artifactId>
        </dependency>
        <!--fastjson依赖-->
        <dependency>
            <groupId>com.alibaba</groupId>
            <artifactId>fastjson</artifactId>
        </dependency>
        <!--jwt依赖-->
        <dependency>
            <groupId>io.jsonwebtoken</groupId>
            <artifactId>jjwt</artifactId>
        </dependency>
        <!--mybatisPlus依赖-->
        <dependency>
            <groupId>com.baomidou</groupId>
            <artifactId>mybatis-plus-boot-starter</artifactId>
        </dependency>
        <!--mysql数据库驱动-->
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
        </dependency>

        <!--阿里云OSS-->
        <dependency>
            <groupId>com.aliyun.oss</groupId>
            <artifactId>aliyun-sdk-oss</artifactId>
        </dependency>

        <!--AOP-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-aop</artifactId>
        </dependency>

        <dependency>
            <groupId>com.alibaba</groupId>
            <artifactId>easyexcel</artifactId>
        </dependency>
        <dependency>
            <groupId>io.springfox</groupId>
            <artifactId>springfox-swagger2</artifactId>
        </dependency>
        <dependency>
            <groupId>io.springfox</groupId>
            <artifactId>springfox-swagger-ui</artifactId>
        </dependency>

    </dependencies>
</project>

~~~

其他子模块（非公共模块）的创建，直接导入公共子模块作为依赖：

~~~xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <parent>
        <artifactId>SGBlog</artifactId>
        <groupId>com.sangeng</groupId>
        <version>1.0-SNAPSHOT</version>
    </parent>
    <modelVersion>4.0.0</modelVersion>

    <artifactId>sangeng-admin</artifactId>

    <dependencies>
        <!--直接导入公共子模块的依赖，就能够继承公共子模块中导入的依赖了-->
        <dependency>
            <groupId>com.sangeng</groupId>
            <artifactId>sangeng-framework</artifactId>
            <version>1.0-SNAPSHOT</version>
        </dependency>
    </dependencies>
</project>

~~~



### 6.2 多模块springBoot项目的模版

1. 以创建工程时自带的模块作为父模块，在这个父模块下面创建多个子模块

2. 只需要父工程的pom.xml文件，不需要src文件夹（直接删除即可）

父工程的pom.xml文件：

~~~xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
   
	<!-- 项目默认的-->
    <groupId>com.sangeng</groupId>
    <artifactId>SGBlog</artifactId>
    <version>1.0-SNAPSHOT</version>
    <modelVersion>4.0.0</modelVersion>
    <!--确保父工程是用pom方式打包的-->
    <packaging>pom</packaging>
    
	<!--把子模块聚合起来，方便进行统一管理(root) （插件：install,clean...），添加子模块时会自动添加-->
    <modules>
        <module>sangeng-framework</module>
        <module>sangeng-admin</module>
        <module>sangeng-blog</module>
    </modules>

    <!-- 统一项目的jdk版本与编码方式-->
    <properties>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <java.version>1.8</java.version>
    </properties>
    
        <!-- 此标签可以进行所有子模块的依赖版本控制-->
    <dependencyManagement>

	<!-- 在dependencyManagement标签下面的dependencies标签并没有真正的导入依赖，只是对子模块的依赖版本进行了锁定-->
    <dependencies>
        <!-- 1 SpringBoot的依赖配置-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-dependencies</artifactId>
            <version>2.5.0</version>
            <!-- 这里进行了很多其他依赖的版本锁定，ctrl进入spring-boot-dependencies.xml即可查看-->
            <type>pom</type>
            <scope>import</scope>
        </dependency>
         <!--2 其他依赖 官方springBoot没有的依赖 按需导入即可-->
        <!--mybatisPlus依赖-->
        <dependency>
            <groupId>com.baomidou</groupId>
            <artifactId>mybatis-plus-boot-starter</artifactId>
            <version>3.4.3</version>
        </dependency>

    </dependencyManagement>

    <build>
        <plugins>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                compile命令可以将项目编译为.class文件。
                <!--compile命令可以将项目编译为.class文件,编译插件？-->
                <artifactId>maven-compiler-plugin</artifactId>
                <version>3.1</version>
                <configuration>
                    <!--引入上面设置好的变量，设置jdk版本与编码方式-->
                    <source>${java.version}</source>
                    <target>${java.version}</target>
                    <encoding>${project.build.sourceEncoding}</encoding>
                </configuration>
            </plugin>
        </plugins>
    </build>
</project>
~~~

公共子模块：

~~~xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <!--展示了父工程中pom.xml文件的信息，表示属于哪一个父工程，并且子模块继承了父工程的相关配置，包括（版本锁定、jdk版本、编码格式的统一）-->
    <parent>
        <artifactId>SGBlog</artifactId>
        <groupId>com.sangeng</groupId>
        <version>1.0-SNAPSHOT</version>
    </parent>
    <!--默认信息-->
    <modelVersion>4.0.0</modelVersion>
    <artifactId>sangeng-framework</artifactId>
    
<!--公共子模块中的依赖：存放其他模块中都会使用到的依赖-->
    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <!--lombk-->
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <optional>true</optional>
        </dependency>
        <!--junit-->
        <dependency>
             <!--继承了父工程中spring-boot-dependencies依赖的相关配置，在spring-boot-dependencies中进行了spring-boot-starter-test版本的配置，所以这就是即使在父工程中找不到版本锁定也可以不指定版本的原因-->
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>


    </dependencies>
</project>

~~~

其他子模块（非公共模块）的创建，直接导入公共子模块作为依赖：

~~~xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <parent>
        <artifactId>SGBlog</artifactId>
        <groupId>com.sangeng</groupId>
        <version>1.0-SNAPSHOT</version>
    </parent>
    <modelVersion>4.0.0</modelVersion>

    <artifactId>sangeng-admin</artifactId>

    <dependencies>
        <!--直接导入公共子模块的依赖，就能够继承公共子模块中导入的依赖了-->
        <dependency>
            <groupId>com.sangeng</groupId>
            <artifactId>sangeng-framework</artifactId>
            <version>1.0-SNAPSHOT</version>
        </dependency>
    </dependencies>
</project>

~~~



### 6.3 多模块项目打包

#### 例子1：三更博客(有公共子项目的打包)

**项目结构**：

父项目：kanaBlog

子项目：

 - 公共子项目：kana-framework
 - 其他项目（导入了公共子项目作为依赖）kana-admin,kana-blog 

1 父项目与公共子项目都不需要配置打包模块（配置了反而不行）。

父项目pom.xml:

~~~xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.kana</groupId>
    <artifactId>kanaBlog</artifactId>
    <!--确保父工程是用pom方式打包的-->
    <packaging>pom</packaging>
    <version>1.0-SNAPSHOT</version>
    <!--父工程自动把子工程聚合起来，方便进行统一管理 （插件：install,clean...）-->
    <modules>
        <module>kana-framework</module>
        <module>kana-admin</module>
        <module>kana-blog</module>
    </modules>


    <!--创建父工程，进行多模块开发，提高代码复用性-->

    <properties>
        <!--统一版本、字符编码-->
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <java.version>1.8</java.version>
    </properties>
    <dependencyManagement>
        <!--进行版本锁定-->
    <dependencies>
        <!-- SpringBoot的依赖配置-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-dependencies</artifactId>
            <version>2.5.0</version>
            <type>pom</type>
            <scope>import</scope>
        </dependency>
    </dependencyManagement>
	<!-- 编译插件,打包的话可以不添加-->
    <build>
        <plugins>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <version>3.1</version>
                <configuration>
                    <source>1.8</source>
                    <target>1.8</target>
                    <encoding>${project.build.sourceEncoding}</encoding>
                </configuration>
            </plugin>
        </plugins>
    </build>

</project>
~~~

公共子项目pom.xml文件：没有打包插件，继承的父项目也没有

~~~xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <parent>
        <artifactId>kanaBlog</artifactId>
        <groupId>com.kana</groupId>
        <version>1.0-SNAPSHOT</version>
    </parent>
    <modelVersion>4.0.0</modelVersion>
    <artifactId>kana-framework</artifactId>

    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
    </dependencies>

</project>
~~~



2 要打包的模块：kana-blog模块(依赖了kana-framework模块)，配置好的pom.xml文件如下：有打包插件

~~~xml
<!-- 继承本项目的父工程 -->
    <parent>
        <artifactId>kanaBlog</artifactId>
        <groupId>com.kana</groupId>
        <version>1.0-SNAPSHOT</version>
    </parent>

    <modelVersion>4.0.0</modelVersion>
    <!--告知此模块是要打包成jar包-->
    <packaging>jar</packaging>
    <artifactId>kana-blog</artifactId>


    <dependencies>
        <!--导入了kana-framework，依赖于kana-framework-->
        <dependency>
            <groupId>com.kana</groupId>
            <artifactId>kana-framework</artifactId>
            <version>1.0-SNAPSHOT</version>
        </dependency>
    </dependencies>

<!--修改每一个有启动类的子模块。加入下面这段配置-->
    <build>
        <plugins>
            <plugin>
                <!--该插件主要用途：构建可执行的JAR -->
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
                <version>2.3.3.RELEASE</version>
                <configuration>
                    <mainClass>com.kana.KanaBlogApplication</mainClass>
                    <layout>ZIP</layout>
                </configuration>
                <executions>
                    <execution>
                        <goals>
                            <goal>repackage</goal>
                        </goals>
                    </execution>
                </executions>
            </plugin>
        </plugins>
        <!-- 项目打包时会将java目录中的*.xml文件也进行打包 -->
        <resources>
            <resource>
                <directory>src/main/java</directory>
                <includes>
                    <include>**/*.xml</include>
                </includes>
                <filtering>false</filtering>
            </resource>

            <!--设置自己目录下的配置文件-->
            <resource>
                <!--下方resources的文件夹名字要和自己项目的文件夹名确认一致才行 很多人就是忽略了名字不一致 -->
                <directory>src/main/resources</directory>
                <includes>
                    <include>**/*</include>
                </includes>
            </resource>
        </resources>
    </build>
~~~



其他博客的做法：最开始在root模块中进行clean + install 先在kana-framework模块的LifeCycle中打包(packge)kana-framework模块(因为kana-blog模块依赖于kana-framework模块),然后在kana-blog的LifeCycle中打包kana-blog模块，最后使用kana-blog中的jar包上传到服务器即可。

我测试了一下，直接在root模块中进行clean + install ，公共子模块与其他子模块就已经都有打包好的jar包了，直接使用其他子模块的jar包在cmd中运行也是能够正常返回响应的。甚至不需要配置这么多其他的配置，直接配置一个打包插件即可（否则报错找不到主类）。

##### 总结：

01健康管理的微服务项目证明：配置好打包插件后，**直接在root项目clean + packge即可**，最简单并且最容易成功。

在**有启动类的模块中配置好打包插件**后(其他模块配置了会出问题找不到主类)，**直接在root模块中进行clean + install即可**得到所有子模块(公共+其他)的jar包，并且有启动类的模块的jar包可以正常运行。

最合理的思路：在root模块中clean ,在公共子模块中install,然后在有启动类的模块中packge，然后把有启动类的模块的jar包进行运行。

~~~xml
    <build>
        <plugins>
            <!--springboot打包插件-->
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
        </plugins>
    </build>
~~~



**容易错的地方**：

1. 父项目与公共子项目中也配置了打包插件，公共子项目默认packing标签为jar,就会去打包,因为公共子模块中没有设置主类，结果报错：找不到主类。
2. 主类的全路径写错，没有idea提示时，就应该检查是否写错了

#### 例子2：没有公共子项目的打包

导入springBoot打包插件到需要打包的子模块后，直接在maven的子项目模块packge即可。

只有一个子模块时使用root模块的packge也可以（springBoot打包插件导入到root模块中）。

~~~xml
    <build>
        <plugins>
            <!--springboot打包插件-->
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
        </plugins>
    </build>
~~~

