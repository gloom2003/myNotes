# Spring MVC-01

## 1.SpringMVC概述

​	Spring 为展现层提供的基于 MVC 设计理念的优秀的 Web 框架，是目前最主流的MVC 框架之一。

​	一种轻量级的、基于MVC的**Web层应用框架**。它能让我们对请求数据的出来，响应数据的处理，页面的跳转等等常见的**web操作变得更加简单方便。**



## 使用maven创建web项目

1.在ided是Project Structure中新建一个模块，并且设置为web模块(都是点击左上角的+号)

2.web模块多了一个web目录，把web目录移动到src/main目录下面，并且改名为webapp目录

3.在pom.xml中设置打包方式为war

~~~xml
    <!--web模块需要进行的设置,设置打包方式为打包为war包的形式-->
    <packaging>war</packaging>
~~~

4.确认，如图：	

![https://image.itbaima.net/images/173/image-20231104198255478.png](https://image.itbaima.net/images/173/image-20231104198255478.png)



## 2.入门案例以及详解

### ①导入相关依赖

~~~~xml
 <dependencies>
        <!-- servlet依赖 -->
        <dependency>
            <groupId>javax.servlet</groupId>
            <artifactId>javax.servlet-api</artifactId>
            <version>3.1.0</version>
            <scope>provided</scope>
        </dependency>
        <!--jsp依赖 -->
        <dependency>
            <groupId>javax.servlet.jsp</groupId>
            <artifactId>jsp-api</artifactId>
            <version>2.1</version>
            <scope>provided</scope>
        </dependency>
        <!--springmvc的依赖-->
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-webmvc</artifactId>
            <version>5.1.9.RELEASE</version>
        </dependency>

        <!-- jackson，把前端请求体中的json字符串转换为对象-->
        <dependency>
            <groupId>com.fasterxml.jackson.core</groupId>
            <artifactId>jackson-databind</artifactId>
            <version>2.9.0</version>
        </dependency>
    </dependencies>
	<!-- tomcat的maven插件-->
    <build>
        <plugins>
            <plugin>
                <groupId>org.apache.tomcat.maven</groupId>
                <artifactId>tomcat7-maven-plugin</artifactId>
                <version>2.2</version>
                <configuration>
                    <!--端口-->
                    <port>81</port>
                    <!--项目路径-->
                    <path>/</path>
                    <!--解决get请求中文乱码-->
                    <uriEncoding>utf-8</uriEncoding>
                </configuration>
            </plugin>
        </plugins>
    </build>
~~~~

### ②配置web.xml以及详解

tomcat中默认有两个Servlet:

- DefaultServlet(< url-pattern >/< /url-pattern >)
- JspServlet(< url-pattern >*.jsp< /url-pattern >)

访问hello.html就会被处理/hello路径的方法(@Conrroller("/hello"))进行处理

下面的配置**使用DispatcherServlet覆盖了tomcat的DefaultServlet，并且配置了default-servlet-handler处理访问静态资源的问题**。值得一提的是，tomcat的DefaultServlet即使不配置default-servlet-handle也可以访问静态资源。

~~~~xml
	<servlet>
        <servlet-name>DispatcherServlet</servlet-name>
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
        <!--
            为DispatcherServlet提供初始化参数的
            设置springmvc配置文件的路径
                name是固定的，必须是contextConfigLocation
                value指的是SpringMVC配置文件的位置
         -->
        <init-param>
            <param-name>contextConfigLocation</param-name>
            <param-value>classpath:spring-mvc.xml</param-value>
        </init-param>
        <!--
            指定项目启动就初始化DispatcherServlet
         -->
        <load-on-startup>1</load-on-startup>
    </servlet>
    <servlet-mapping>
        <servlet-name>DispatcherServlet</servlet-name>
        <!--
        <url-pattern>/</url-pattern> 缺省匹配:，功能与tomcat中的DefaultServlet一致，表示当前servlet映射除jsp之外的所有请求（包含静态资源）,因为jsp相关的请求有JspServlet进行处理，只有其他Servlet都匹配不上的时候才会进行匹配

        <url-pattern> *.do </url-pattern> 表示.do结尾的请求路径才能被SpringMVC处理(老项目会出现)

        <url-pattern> /* </url-pattern> 表示当前servlet映射所有请求（包含静态资源,jsp），不应该使用其配置DispatcherServlet
         -->
        <url-pattern>/</url-pattern><!-- 这样配置的话DispatcherServlet会覆盖tomcat的DefaultServlet -->
    </servlet-mapping>


    <!--乱码处理过滤器，由SpringMVC提供-->
    <!-- 处理post请求乱码 -->
    <filter>
        <filter-name>CharacterEncodingFilter</filter-name>
        <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
        <init-param>
            <!-- name固定不变，value值根据需要设置 -->
            <param-name>encoding</param-name>
            <param-value>UTF-8</param-value>
        </init-param>
    </filter>
    <filter-mapping>
        <filter-name>CharacterEncodingFilter</filter-name>
        <!-- 所有请求都设置utf-8的编码 -->
        <url-pattern>/*</url-pattern>
    </filter-mapping>
~~~~

### ③配置SpringMVC

在resources目录下创建mvc的配置文件spring-mvc.xml

~~~~xml
   <!--
        SpringMVC只扫描controller包即可
    -->
    <context:component-scan base-package="com.sangeng.controller"/>
    <!-- 解决静态资源访问问题，如果不加mvc:annotation-driven会导致无法访问handler
	配置后相当于有一个默认方法(handler)会处理那些其他方法无法匹配的请求，找对应的静态资源来响应请求
	例如：请求/hi.html,但是没有匹配路径/hi的Controller方法,此时就会被这个默认方法(handler)处理，找到对应的hi.html(前提是真的有这个文件)进行响应
-->
    <mvc:default-servlet-handler/>
    <!--配置注解驱动-->
    <mvc:annotation-driven>
        <!--解决响应乱码-->
        <mvc:message-converters>
            <bean class="org.springframework.http.converter.StringHttpMessageConverter">
                <constructor-arg value="utf-8"/>
            </bean>
        </mvc:message-converters>
    </mvc:annotation-driven>
~~~~

### ④创建测试用的jsp页面

在webapp下创建success.jsp

~~~~jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>Title</title>
</head>
<body>
    成功
</body>
</html>

~~~~

### ⑤编写Controller

定义一个类，在类上加上@Controller注解，声明其是一个Controller。要创建在之前注解扫描所配置的包下。

然后定义一个方法，在方法上加上@RequestMapping来指定哪些请求会被该方法所处理。

~~~~java
@Controller
public class TestController {

    @RequestMapping("/hello")//指定请求路径是/hello的才能被该方法处理,与请求路径的后缀无关(如:请求hello.html或者hello.css都是走这个方法)
    public String hello(){
        System.out.println("hello");
        return "/success.jsp";//跳转到success.jsp
    }

}
~~~~

## 3.设置请求映射规则@RequestMapping

​	该注解可以加到方法上或者是类上。（查看其源码可知）

​	我们可以用其来设定所能匹配请求的要求。只有符合了设置的要求，请求才能被加了该注解的方法或类处理。



### 3.1 指定请求路径

​	path或者value属性都可以用来指定请求路径。

例如：

​	我们期望让请求的资源路径为**/test/testPath**的请求能够被**testPath**方法处理则可以写如下代码

~~~~java
@Controller
@RequestMapping("/test")
public class TestController {
    @RequestMapping("/testPath")
    public String testPath(){
        return "/success.jsp";
    }
}
~~~~



### 3.2 指定请求方式

​	method属性可以用来指定可处理的请求方式。

例如：

​	我们期望让请求的资源路径为**/test/testMethod**的**POST**请求能够被**testMethod**方法处理。则可以写如下代码

~~~~java
@Controller
@RequestMapping("/test")
public class TestController {

    @RequestMapping(value = "/testMethod",method = RequestMethod.POST)
    public String testMethod(){
        System.out.println("testMethod处理了请求");
        return "/success.jsp";
    }
}

~~~~

### 3.3 指定请求参数

​	我们可以使用params属性来对请求参数进行一些限制。可以要求必须具有某些参数，或者是某些参数必须是某个值，或者是某些参数必须不是某个值。

例如：

​	我们期望让请求的资源路径为**/test/testParams**的**GET**请求,并且请求参数中**具有code参数**的请求能够被testParams方法处理。则可以写如下代码: **params = "code"**

~~~~java
@Controller
@RequestMapping("/test")
public class TestController {
    @RequestMapping(value = "/testParams",method = RequestMethod.GET,params = "code")
    public String testParams(){
        System.out.println("testParams处理了请求");
        return "/success.jsp";
    }
}
~~~~

​	指定**多个参数**，使用：**params = {"code","id"}**

​	如果是要求**不能有code**这个参数可以把改成如下形式: **params = "!code"**

~~~~java
@Controller
@RequestMapping("/test")
public class TestController {
    @RequestMapping(value = "/testParams",method = RequestMethod.GET,params = "!code")
    public String testParams(){
        System.out.println("testParams处理了请求");
        return "/success.jsp";
    }
}
~~~~

​	

​	如果要求有code这参数，并且这参数值必须**是某个值**可以改成如下形式

~~~~java
@Controller
@RequestMapping("/test")
public class TestController {
    @RequestMapping(value = "/testParams",method = RequestMethod.GET,params = "code=sgct")
    public String testParams(){
        System.out.println("testParams处理了请求");
        return "/success.jsp";
    }
}
~~~~



​	如果要求有code这参数，并且这参数值必须**不是某个值**可以改成如下形式	

~~~~java
@Controller
@RequestMapping("/test")
public class TestController {
    @RequestMapping(value = "/testParams",method = RequestMethod.GET,params = "code!=sgct")
    public String testParams(){
        System.out.println("testParams处理了请求");
        return "/success.jsp";
    }
}
~~~~



### 3.4 指定请求头

​	我们可以使用**headers**属性来对请求头进行一些限制。



例如：

​	我们期望让请求的资源路径为**/test/testHeaders的**GET**请求,并且请求头中具有deviceType**的请求能够被testHeaders方法处理。则可以写如下代码

~~~~java
@Controller
@RequestMapping("/test")
public class TestController {
    
    @RequestMapping(value = "/testHeaders",method = RequestMethod.GET,headers = "deviceType")
    public String testHeaders(){
        System.out.println("testHeaders处理了请求");
        return "/success.jsp";
    }
}
~~~~



​	如果是要求**不能有deviceType**这个请求头可以把改成如下形式

~~~~java
@Controller
@RequestMapping("/test")
public class TestController {
    
    @RequestMapping(value = "/testHeaders",method = RequestMethod.GET,headers = "!deviceType")
    public String testHeaders(){
        System.out.println("testHeaders处理了请求");
        return "/success.jsp";
    }
}
~~~~



​	如果要求有deviceType这个请求头，并且其值必须**是某个值**可以改成如下形式

~~~~java
@Controller
@RequestMapping("/test")
public class TestController {
    
    @RequestMapping(value = "/testHeaders",method = RequestMethod.GET,headers = "deviceType=ios")
    public String testHeaders(){
        System.out.println("testHeaders处理了请求");
        return "/success.jsp";
    }
}
~~~~



​	如果要求有deviceType这个请求头，并且其值必须**不是某个值**可以改成如下形式

~~~~java
@Controller
@RequestMapping("/test")
public class TestController {
    
    @RequestMapping(value = "/testHeaders",method = RequestMethod.GET,headers = "deviceType!=ios")
    public String testHeaders(){
        System.out.println("testHeaders处理了请求");
        return "/success.jsp";
    }
}
~~~~



### 3.5 指定请求头Content-Type

​	我们可以使用**consumes**属性来对**Content-Type**这个请求头进行一些限制。



​	我们期望让请求的资源路径为**/test/testConsumes**的POST请求,并且请求头中的Content-Type头**必须为 multipart/from-data** 的请求能够被testConsumes方法处理。则可以写如下代码

~~~~java
    @RequestMapping(value = "/testConsumes",method = RequestMethod.POST,consumes = "multipart/from-data")
    public String testConsumes(){
        System.out.println("testConsumes处理了请求");
        return "/success.jsp";
    }
~~~~



​	如果我们要求请求头Content-Type的值必须**不能为某个multipart/from-data**则可以改成如下形式：

~~~~java
    @RequestMapping(value = "/testConsumes",method = RequestMethod.POST,consumes = "!multipart/from-data")
    public String testConsumes(){
        System.out.println("testConsumes处理了请求");
        return "/success.jsp";
    }
~~~~

## 4.RestFul风格

​	 RestFul是一种网络应用程序的设计风格和开发方式 。现在很多互联网企业的**网络接口**定义都会符合其风格。



主要规则如下：

- ​	 每一个URI代表1种资源     

- ​     客户端使用GET、POST、PUT、DELETE 4个表示操作方式的动词对服务端资源进行操作：GET用来获取资源，POST用来新建资源，PUT用来更新资源，DELETE用来删除资源； 
- ​	 简单参数例如id等写到url路径上  例如： /user/1 HTTP GET：获取id=1的user信息      /user/1 HTTP DELETE ：删除id=1的user信息    
- ​	 复杂的参数转换成json或者xml（现在基本都是json）写到请求体中。

## 5.获取请求参数

### 5.1 获取路径参数 @PathVariable

​	RestFul风格的接口一些参数是在请求路径上的。类似： /user/1  这里的1就是id。

​	如果我们想获取这种格式的数据可以使用**@PathVariable**来实现。

定义(value = "/user/{id}"表示可以处理这样的请求，定义 @PathVariable("id")表示可以接收路径上的参数。

路径参数与方法参数相同时，可以省略id，直接使用@PathVariable

例如：

~~~~java
@Controller
public class UserController {

    @RequestMapping(value = "/user/{id}",method = RequestMethod.GET)
    public String findUserById( @PathVariable Integer id){
        System.out.println("findUserById");
        System.out.println(id);
        return "/success.jsp";
    }
}
~~~~

多个路径参数：

~~~~java
@Controller
public class UserController {
    @RequestMapping(value = "/user/{id}/{name}",method = RequestMethod.GET)
    public String findUser(@PathVariable("id") Integer id,@PathVariable("name") String name){
        System.out.println("findUser");
        System.out.println(id);
        System.out.println(name);
        return "/success.jsp";
    }
}

~~~~



### 5.2 获取请求体中的Json格式参数

​	RestFul风格的接口一些比较复杂的参数会转换成Json通过请求体传递过来。这种时候我们可以使用**@RequestBody**注解获取请求体中的数据。

#### 5.2.1 配置

​	SpringMVC可以帮我们把json数据转换成我们需要的类型。但是需要进行一些基本配置。SpringMVC默认会使用jackson来进行json的解析。所以我们需要导入jackson的依赖（前面我们已经导入过）。

~~~~xml
        <!-- jackson，帮助进行json转换-->
        <dependency>
            <groupId>com.fasterxml.jackson.core</groupId>
            <artifactId>jackson-databind</artifactId>
            <version>2.9.0</version>
        </dependency>
~~~~

​	然后还要配置注解驱动（前面已经配置过）

~~~~xml
    <mvc:annotation-driven>
    </mvc:annotation-driven>
~~~~



#### 5.2.2 使用

请求体数据:

~~~~json
{"name":"三更","age":15}
~~~~

​	

###### 	1.获取参数封装成实体对象

​	如果我们想让Spring MVC从请求体中把Json数据获取出来封装User对象并且赋值给user对象,我们可以这样定义方法：

~~~~~java
@Controller
public class UserController {
    @RequestMapping(value = "/user",method = RequestMethod.POST)
    public String insertUser(@RequestBody User user){
        System.out.println("insertUser");
        System.out.println(user);
        return "/success.jsp";
    }
}
~~~~~

​	User实体类如下：

**注意：1.需要有set,get方法 2.属性名称需要与参数一一对应**

~~~~java
@Data
@NoArgsConstructor
@AllArgsConstructor
public class User {
    private Integer id;
    private String name;
    private Integer age;
}

~~~~

​	

###### 	2.获取参数封装成Map集合

​	也可以把该数据获取出来封装成Map集合：

~~~~java
    @RequestMapping(value = "/user",method = RequestMethod.POST)
    public String insertUser(@RequestBody Map map){
        System.out.println("insertUser");
        System.out.println(map);
        return "/success.jsp";
    }
~~~~



​	如果请求体传递过来的数据是一个User数组转换成的json,如：

~~~~java
[{"name":"三更1","age":14},{"name":"三更2","age":15},{"name":"三更3","age":16}]
~~~~

​	则方法定义应为：

~~~~java
    @RequestMapping(value = "/users",method = RequestMethod.POST)
    public String insertUsers(@RequestBody List<User> users){
        System.out.println("insertUsers");
        System.out.println(users);
        return "/success.jsp";
    }
~~~~



#### 5.2.3 注意事项

​	如果需要使用**@RequestBody**来获取请求体中Json并且进行转换，要求请求头 Content-Type 的值要为： application/json 。



### 5.3 获取QueryString格式参数 

​	如果接口的参数是使用QueryString的格式的话，我们也可以使用SpringMVC快速获取参数。

​	我们可以使用**@RequestParam**来获取QueryString格式的参数。

​	**Spring MVC默认就是从路径中根据参数的名称一一对应的获取QueryString格式的参数，所以@RequestParam一般可以省略。**



#### 5.3.1 使用

##### 范例一

​	要求定义个接口，该接口请求路径要求为  /testRequestParam，请求方式无要求。参数为id和name和likes。使用QueryString的格式传递。

###### 	1.参数单独的获取

​	如果我们想把id，name，likes单独获取出来可以使用如下写法：

​	在方法中定义方法参数，**方法参数名要和请求参数名一致，这种情况下我们可以省略@RequestParam注解。**

​	**请求中的参数为：localhost:81/testRquestParam?id=1&name=jojo&likes=a&likes=b** ,

~~~~java
    @RequestMapping("/testRquestParam")
    public String testRquestParam(Integer id, String name, String[] likes){
        System.out.println("testRquestParam");
        System.out.println(id);
        System.out.println(name);
        System.out.println(Arrays.toString(likes));
        return "/success.jsp";
    }
~~~~

​	如果方法参数名和请求参数名不一致，我们可以加上**@RequestParam**注解例如：

~~~~java
    @RequestMapping("/testRquestParam")
    public String testRquestParam(@RequestParam("id") Integer uid,@RequestParam("name") String name, @RequestParam("likes")String[] likes){
        System.out.println("testRquestParam");
        System.out.println(uid);
        System.out.println(name);
        System.out.println(Arrays.toString(likes));
        return "/success.jsp";
    }
~~~~



###### 	2.获取参数封装成实体对象

​	如果我们想把这些**QueryString格式的参数封装到一个User对象**中可以使用如下写法：

​	**注意：不要加@RequestParam注解，否则Spring MVC会从路径中寻找参数名为user的参数**

~~~~java
    @RequestMapping("/testRquestParam")
    public String testRquestParam(User user){
        System.out.println("testRquestParam");
        System.out.println(user);
        return "/success.jsp";
    }
~~~~

​	User类定义如下：

~~~~java
@Data
@NoArgsConstructor
@AllArgsConstructor
public class User {
    private Integer id;
    private String name;
    private Integer age;
    private String[] likes;
}
~~~~

​	测试时请求url如下：

~~~~java
http://localhost:81/testRquestParam?id=1&name=三更草堂&likes=编程&likes=录课&likes=烫头
~~~~



​	**注意：实体类中的成员变量要和请求参数名对应上。并且要提供对应的set/get方法。**

使用postman选择Body的x-www-form-urlencodeed，**可以在请求体中设置QueryString格式的参数然后发送post请求**，Spring MVC也能够从请求体获取到QueryString格式的数据并且转换为对象。

### 5.4 相关注解其他属性

#### 5.4.1 required

​	代表是否必须，默认值为true也就是必须要有对应的参数。如果没有就会报错。

​	如果对应的参数可传可不传则可以把其设置为fasle

例如：

~~~~java
    @RequestMapping("/testRquestParam")
    public String testRquestParam(@RequestParam(value = "id",required = false) Integer uid,@RequestParam("name") String name, @RequestParam("likes")String[] likes){
        System.out.println("testRquestParam");
        System.out.println(uid);
        System.out.println(name);
        System.out.println(Arrays.toString(likes));
        return "/success.jsp";
    }
~~~~



#### 5.4.2 defaultValue

​	如果对应的参数没有，我们可以用defaultValue属性设置默认值。

例如：

~~~~java
    @RequestMapping("/testRquestParam")
    public String testRquestParam(@RequestParam(value = "id",required = false,defaultValue = "777") Integer uid,@RequestParam("name") String name, @RequestParam("likes")String[] likes){
        System.out.println("testRquestParam");
        System.out.println(uid);
        System.out.println(name);
        System.out.println(Arrays.toString(likes));
        return "/success.jsp";
    }
~~~~

# SpringMVC-02

## 1.类型转换器

​	虽然我们前面在获取参数时看起来非常轻松，但是在这个过程中是有可能出现一些问题的。

​	例如，请求参数为success=1 我们期望把这个请求参数获取出来赋值给一个Boolean类型的变量。

​    这里就会涉及到  Stirng-——>Boolean的类型转换了。实际上**SpringMVC中内置了很多类型转换器来进行类型转换**。也有专门进行Stirng-——>Boolean类型转换的转换器**StringToBooleanConverter**。

​	如果是符合SpringMVC内置转换器的转换规则就可以很轻松的实现转换。但是如果不符合转换器的规则呢？

​	例如，from表单的submit按钮发送的请求参数为birthday=2004-12-12 我们期望把这个请求参数获取出来赋值给一个Date类型的变量。就不符合内置的规则了。内置的只可以把 2004/12/12 这种格式进行转换。这种情况下我们就可以选择自定义类型转换。



### 1.1 自定义类型转换器

#### ①创建类实现Converter接口

~~~~java
public class StringToDateConverter implements Converter<String, Date> {//泛型表示要从String类型转换为Date类型
    public Date convert(String source) {
        return null;
    }
}
~~~~

#### ②实现convert方法

~~~~java
public class StringToDateConverter implements Converter<String, Date> {
    public Date convert(String source) {
        //String->Date   2005-12-12 
        Date date = null;
        try {
            SimpleDateFormat simpleDateFormat = new SimpleDateFormat("yyyy-MM-dd");
            date = simpleDateFormat.parse(source);
        } catch (ParseException e) {
            e.printStackTrace();
        }
        return date;
    }
}
~~~~

#### ③配置让SpringMVC使用自定义转换器

配置后，原来SpringMVC自带的String->Date的转换器就会失效

~~~~~xml
    <!--指定conversion-service属性为下面的ConversionServiceFactoryBean类的bean -->
    <mvc:annotation-driven conversion-service="myConversionService">
        <mvc:message-converters>
            <bean class="org.springframework.http.converter.StringHttpMessageConverter">
                <constructor-arg value="utf-8"/>
            </bean>
        </mvc:message-converters>
    </mvc:annotation-driven>

    <bean class="org.springframework.context.support.ConversionServiceFactoryBean" id="myConversionService">
        <!--给ConversionServiceFactoryBean类的converters属性进行赋值，为set集合，把自定义的Converter添加到set集合中即可-->
        <property name="converters">
            <set>
                <bean class="com.sangeng.converter.StringToDateConverter"></bean>
            </set>
        </property>
    </bean>
~~~~~





### 1.2 日期转换简便解决方案

​	如果是String到Date的转换我们也可以使用另外一种更方便的方式。使用@DateTimeFormat来指定字符串的格式。

如下：

~~~~java
    @RequestMapping("/testDateConverter")
    public String testDateConverter(@DateTimeFormat(pattern = "yyyy-MM-dd") Date birthday){
        System.out.println("testDateConverter");
        System.out.println(birthday);
        return "/success.jsp";
    }
~~~~

## 2.在响应体存放响应数据（重点）

​	无论是RestFul风格还是我们之前web阶段接触过的异步请求，都需要**把数据转换成Json放入响应体中**。

​	

### 2.1 数据放到响应体

​	我们的SpringMVC为我们提供了**@ResponseBody**来非常方便的**把方法的返回值（依靠json转换工具，转换为Json后）放到响应体中**。

​	**@ResponseBody**可以加在哪些东西上面？类上和方法上（看注解的源代码即可）



### 2.2 数据转换成Json

​	SpringMVC可以把我们进行Json的转换，不过需要进行相应配置（已经配置过）。



#### 2.2.1 配置

##### ①导入jackson依赖

~~~~xml
        <!-- jackson，帮助进行json转换-->
        <dependency>
            <groupId>com.fasterxml.jackson.core</groupId>
            <artifactId>jackson-databind</artifactId>
            <version>2.9.0</version>
        </dependency>
~~~~

##### ②开启mvc的注解驱动

~~~~xml
    <mvc:annotation-driven></mvc:annotation-driven>
~~~~



#### 2.2.2 使用

​	只要把要转换的数据直接作为方法的返回值返回即可。SpringMVC会帮我们把返回值转换成json。具体代码请参考范例。



### 2.3 范例

#### 范例一

​	要求定义个RestFul风格的接口，该接口可以用来根据id查询用户。请求路径要求为  /response/user  ，请求方式要求为GET。

​	而请求参数id要写在请求路径上，例如   /response/user/1   这里的1就是id。

​	要求获取参数id,去查询对应id的用户信息（模拟查询即可，可以选择直接new一个User对象），并且转换成json响应到响应体中。

~~~~java
@Controller
@RequestMapping("/response")
public class ResponseController {
    @GetMapping("/user/{id}")
    @ResponseBody //表示这方法的返回值将会放入响应体中
    public User testResponse(@PathVariable Integer id){
        User user = new User(id,null,null,null);
        return user;//因为上面做过json转换的配置，所以会把返回的对象转换成json字符串
    }
}

~~~~



#### 范例二

​	要求定义个RestFul风格的接口，该接口可以查询所有用户。请求路径要求为  /response/user  ，请求方式要求为GET。

​	去查询所有的用户信息（模拟查询即可，可以选择直接创建集合，添加几个User对象），并且转换成json响应到响应体中。

~~~~java
@Controller
@RequestMapping("/response")
@ResponseBody  //这类中所有方法的返回值都会放到响应体中
public class ResponseController {

    @GetMapping("/user/{id}")
    public User testResponse(@PathVariable Integer id){
        User user = new User(id,null,null,null);
        return user;
    }

    @GetMapping("/user")
    public List<User> testResponse2(){
        List<User> list = new ArrayList<User>();
        list.add(new User(1,"三更",15,null));
        list.add(new User(2,"四更",16,null));
        list.add(new User(3,"五更",17,null));
        return list;
    }
}
~~~~

​	如果一个Controller中的所有方法返回值都要放入响应体，那么我们可以直接在Controller类上加@ResponseBody。

​	我们可以使用**@RestController** 注解替换@Controller和@ResponseBody两个注解

~~~java
@RequestMapping("/response")
@RestController //相当于  @Controller+@ResponseBody
public class ResponseController {

    @GetMapping("/user/{id}")
    public User testResponse(@PathVariable Integer id){
        User user = new User(id,null,null,null);
        return user;
    }

    @GetMapping("/user")
    public List<User> testResponse2(){
        List<User> list = new ArrayList<User>();
        list.add(new User(1,"三更",15,null));
        list.add(new User(2,"四更",16,null));
        list.add(new User(3,"五更",17,null));
        return list;
    }
}

~~~



## 3.页面跳转

​	在SpringMVC中我们可以非常轻松的实现页面跳转，只需要把方法的返回值写成要跳转页面的路径即可。

例如：

~~~~java
@Controller
public class PageJumpController {
    @RequestMapping("/testJump")
    public String testJump(){
        return "/success.jsp";
    }
}
~~~~

​	

​	默认的跳转其实是转发的方式跳转的。我们也可以选择加上标识，在要跳转的路径前加上**forward:** 。这样SpringMVC也会帮我们进行请求转发。

例如：

~~~~java
@Controller
public class PageJumpController {
    @RequestMapping("/testJump")
    public String testJump(){
        return "forward:/success.jsp";
    }
}
~~~~



​	如果想实现重定向跳转则可以在跳转路径前加上 **redirect:**  进行标识。这样SpringMVC就会帮我们进行重定向跳转。

例如：

~~~~java
@Controller
public class PageJumpController {
    @RequestMapping("/testJump")
    public String testJump(){
        return "redirect:/success.jsp";
    }
}

~~~~



## 4.视图解析器

​	如果我们经常需要跳转页面，并且页面所在的路径比较长，我们每次写完整路径会显的有点麻烦。我们可以配置视图解析器，设置跳转路径的前缀和后缀。这样可以简化我们的书写。



### 4.1使用步骤

#### ①配置视图解析器

​	我们需要完SpringMVC容器中注入InternalResourceViewResolver对象。

~~~~xml
    <!--配置视图解析器-->
    <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver" id="viewResolver">
        <!--要求拼接的前缀-->
        <property name="prefix" value="/WEB-INF/page/"></property>
        <!--要拼接的后缀-->
        <property name="suffix" value=".jsp"></property>
    </bean>
~~~~

#### ②页面跳转

​	视图解析器会在逻辑视图的基础上拼接得到物理视图。

~~~~java
    @RequestMapping("/testJumpToJsp")
    public String testJumpToJsp(){
//        return "/WEB-INF/page/test.jsp";
        return "test";
    }
~~~~



### 4.2 不进行前后缀拼接

​	如果在配置了视图解析器的情况下，某些方法中并不想拼接前后缀去跳转。这种情况下我们可以在跳转路径前加**forward:** 或者**redirect:**进行标识。这样就不会进行前后缀的拼接了。

​	例如:

~~~~java
    @RequestMapping("/testJumpHtml")
    public String testJumpHtml(){
        //如果加了forward:  或者redirect: 就不会进行前后缀的拼接
        return "forward:/hello1.html";
    }
~~~~





## 5.获取原生对象

​	我们之前在web阶段我们经常要使用到request对象，response，session对象等。我们也可以通过SpringMVC获取到这些对象。（不过在MVC中我们很少获取这些对象，因为有更简便的方式，避免了我们使用这些原生对象相对繁琐的API。）

​	我们只需要在方法上添加对应类型的参数即可，但是注意数据类型不要写错了，SpringMVC会把我们需要的对象传给我们的形参。

​	例如：

~~~~java
@Controller
public class RequestResponseController {
    @RequestMapping("/getReqAndRes")
    public String getReqAndRes(HttpServletRequest request, HttpServletResponse response, HttpSession session){
        System.out.println();
        return "test";
    }
}
~~~~



​	

## 6.获取请求头和Cookie

### 6.1获取请求头

​	在方法中定义一个参数，参数前加上**@RequestHeader**注解，知道要获取的请求头名即可获取对应请求头的值。

例如：

​	想要获取 device-type 这个请求头则可以按照如下方式定义方法。

~~~~java
@Controller
public class RequestResponseController {


    @RequestMapping("/getHeader")
    public String getHeader(@RequestHeader(value = "device-type") String deviceType){
        System.out.println(deviceType);
        return "test";
    }
}

~~~~



### 6.2 获取Cookie

​	在方法中定义一个参数，参数前加上**@CookieValue** 注解，知道要获取的cookie名即可获取对应cookie的值。

例如：

​	想要获取 JSESSIONID 的cookie值。则可以按照如下方式定义方法。

~~~~java
@Controller
public class RequestResponseController {

    @RequestMapping("/getCookie")
    public String getCookie(@CookieValue("JSESSIONID") String sessionId){
        System.out.println(sessionId);
        return "test";
    }
}

~~~~



## 7.JSP开发模式（了解）

​	如果我们使用JSP进行开发，那我们就需要在域中存数据，然后跳转到对应的JSP页面中，在JSP页面中获取域中的数据然后进行相关处理。

​	使用如果是类似JSP的开发模式就会涉及到**往域中存数据**和**携带数据跳转页面**的操作。

​	所以我们来看下如果用SpringMVC进行相关操作。



### 7.1 往Requet域存数据并跳转

#### 7.1.1 使用Model

​	我们可以使用**Model**来往域中存数据。然后使用之前的方式实现页面跳转。



例如

​	我们要求访问  **/testRequestScope** 这个路径时能往Request域中存name和title数据，然后跳转到 **/WEB-INF/page/testScope.jsp** 这个页面。在Jsp中获取域中的数据。

​	则可以使用如下写法:

~~~~java
@Controller
public class JspController {
    @RequestMapping("/testRquestScope")
    public String testRquestScope(Model model){
        //往请求域存数据
        model.addAttribute("name","三更");
        model.addAttribute("title","不知名Java教程UP主");
        return "testScope";
    }
}
~~~~



#### 7.1.2 使用ModelAndView

​	我们可以使用**ModelAndView**来往域中存数据和页面跳转。

例如

​	我们要求访问  **/testRequestScope2**  这个路径时能往域中存name和title数据，然后跳转到 **/WEB-INF/page/testScope.jsp** 这个页面。在Jsp中获取域中的数据。

​	则可以使用如下写法:

~~~~java
@Controller
public class JspController {
    @RequestMapping("/testRquestScope2")
    public ModelAndView testRquestScope2(ModelAndView modelAndView){
        //往域中添加数据
        modelAndView.addObject("name","三更");
        modelAndView.addObject("title","不知名Java教程UP主");
        //页面跳转
        modelAndView.setViewName("testScope");
        return modelAndView;
    }
}
~~~~

​	**注意要把modelAndView对象作为方法的返回值返回。**



### 7.2 从Request域中获取数据

​	我们可以使用**@RequestAttribute** 把他加在方法参数上，可以让SpringMVC帮我们从Request域中获取相关数据。



例如

~~~~java
@Controller
public class JspController {

    @RequestMapping("/testGetAttribute")
    public String testGetAttribute(@RequestAttribute("org.springframework.web.servlet.HandlerMapping.bestMatchingPattern")
                                               String value,HttpServletRequest request){
        System.out.println(value);
        return "testScope";
    }
}

~~~~





### 7.3 往Session域存数据并跳转

​	我们可以使用**@SessionAttributes**注解来进行标识，用里面的属性来标识哪些数据要存入Session域。



例如

​	我们要求访问  **/testSessionScope **这个路径时能往域中存**name**和**title**数据，然后跳转到 **/WEB-INF/page/testScope.jsp** 这个页面。在jsp中获取**Session域**中的数据。



​	则可以使用如下写法

~~~~java
@Controller
@SessionAttributes({"name"})//表示name这个数据也要存储一份到session域中
public class JspController {


    @RequestMapping("/testSessionScope")
    public String testSessionScope(Model model){
        model.addAttribute("name","三更");
        model.addAttribute("title","不知名Java教程UP主");
        return "testScope";
    }
}
~~~~





### 7.4 获取Session域中数据

​	我们可以使用**@SessionAttribute**把他加在方法参数上，可以让SpringMVC帮我们从**Session域**中获取相关数据。



例如：

~~~~java
@Controller
@SessionAttributes({"name"})
public class JspController {
    @RequestMapping("/testGetSessionAttr")
    public String testGetSessionAttr(@SessionAttribute("name") String name){
        System.out.println(name);
        return "testScope";
    }

}

~~~~

