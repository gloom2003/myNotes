# Spring MVC-01

## 1.SpringMVC概述

​	Spring 为展现层提供的基于 MVC 设计理念的优秀的 Web 框架，是目前最主流的MVC 框架之一。

​	一种轻量级的、基于MVC的**Web层应用框架**。它能让我们对请求数据的出来，响应数据的处理，页面的跳转等等常见的**web操作变得更加简单方便。**



## 使用maven创建web项目

1.在ided的左上角File中的Project Structure中新建一个模块(或者新建一个maven项目,使用新的模块)，并且设置为web模块(都是点击左上角的+号)

2.web模块多了一个web目录，把web目录移动到src/main目录下面，并且改名为webapp目录

3.在pom.xml中设置打包方式为war

~~~xml
    <!--web模块需要进行的设置,设置打包方式为打包为war包的形式-->
    <packaging>war</packaging>
~~~

4.确认，如图：	

![https://image.itbaima.net/images/173/image-20231104198255478.png](https://image.itbaima.net/images/173/image-20231104198255478.png)



## 2.入门案例以及Servlet详解

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
                    <!--项目路径,例如：localhost:81/中的"/"-->
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

webapp/WEB-INF/web.xml配置：

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

**选择XML Configuration File中的Spring File(不同的选择xml的最上面的模版是不同的)**,在resources目录下创建mvc的配置文件spring-mvc.xml

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

​	

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

​	**如果不指定method属性，默认情况下@RequestMapping注解可以处理所有的HTTP请求方法。**

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



​	我们期望让请求的资源路径为**/test/testConsumes**的POST请求,并且请求头中的Content-Type头**必须为 multipart/from-data** 的请求才能够被testConsumes方法处理。则可以写如下代码

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

## 5.获取请求参数

### postman各种参数的含义

参考链接：

1. https://www.cnblogs.com/dw3306/p/14200862.html

1. **java后台如何接收参数？**  **https://www.cnblogs.com/dw3306/p/13691917.html**

1. 

   

#### postman中 form-data、x-www-form-urlencoded、raw、binary的区别

 

**1、form-data:** 

​          就是http请求中的multipart/form-data,它会将表单的数据处理为一条消息，以标签为单元，用分隔符分开。既可以上传键值对，也可以上传文件。当上传的字段是文件时，会有Content-Type来说明文件类型；content-disposition，用来说明字段的一些信息；

由于有boundary隔离，所以multipart/form-data既可以上传文件，也可以上传键值对，它采用了键值对的方式，所以可以上传多个文件，在springmvc中可以使用MultipartHttpServletRequest接收通过api根据"name"获取不同的键值，也可以通过MulTipartFile数组接收多个文件。

![img](https://img-blog.csdn.net/20151118130933756?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

**2、x-www-form-urlencoded：**

​       就是application/x-www-from-urlencoded,会将表单内的数据转换为键值对，&分隔。
当form的action为get时，浏览器用x-www-form-urlencoded的编码方式，将表单数据编码为
(name1=value1&name2=value2…)，然后把这个字符串append到url后面，用?分隔，跳转
到这个新的url。
当form的action为post时，浏览器将form数据封装到http body中，然后发送到server。
这个格式不能提交文件。

![img](https://img-blog.csdn.net/20151118131219584?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

![img](https://img-blog.csdn.net/20151118131234865?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

**3、raw**

​      可以上传任意格式的文本，可以上传text、json、xml、html等

![img](https://img-blog.csdn.net/20151118131504612?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

![img](https://img-blog.csdn.net/20151118131523976?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

**4、binary**

​     相当于Content-Type:application/octet-stream,从字面意思得知，只可以上传二进制数据，通常用来上传文件，由于没有键值，所以，一次只能上传一个文件。

 

multipart/form-data与x-www-form-urlencoded区别

​        multipart/form-data：既可以上传文件等二进制数据，也可以上传表单键值对，只是最后会转化为一条信息；

​        x-www-form-urlencoded：只能上传键值对，并且键值对都是间隔分开的。

 

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



### 5.2 获取请求体中的Json格式参数(存放到entry,map,LIst< entry>中)

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

##### 3 使用List接受参数

~~~java
    @PostMapping("/saveUserFacilityList")
    @ResponseBody
    @ApiOperation("保存用户授权的院舍列表")
    public ResultDTO<Boolean> saveUserFacilityList(@ApiParam(value = "用户ID", required = true)
                                                   @RequestParam(value = "userId") Long userId,
                                                   @ApiParam(value = "用户授权的院舍ID集合", required = true)
                                                   @RequestParam(value = "facilityIds") List<Long> facilityIds) {
        return ResultDTO.success(userService.saveUserFacilityList(userId, facilityIds));
    }
~~~

请求方式：

使用postman选择Body的x-www-form-urlencodeed

~~~
userId = 2
facilityIds = 1,2,3 
~~~





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

~~~~java
@Controller
@RequestMapping("/response")
public class ResponseController {
    @GetMapping("/user/{id}")
    @ResponseBody //表示这方法的返回值将会放入响应体中
    public User testResponse(@PathVariable Integer id){
        User user = new User(id,null,null,null);
        return user;//因为上面做过json转换的配置，所以会把返回的对象转换成json字符串写入响应体中
    }
}

~~~~



#### 范例二

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

​	如果一个Controller中的所有方法返回值都要放入响应体，那么我们可以**直接在Controller类上加@ResponseBody。**

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

​	**请求转发**和**重定向**是两种不同的HTTP请求处理方式。

1 请求转发是指在服务器端将请求转发给另一个资源，然后由该资源处理请求并生成响应返回给客户端。客户端不会察觉到请求被转发了，因为整个处理过程是在服务器端进行的。请求转发通常用于服务器内部资源之间的交互，比如在Servlet中进行请求转发。

2 重定向是指在服务器端接收到客户端的请求后，向客户端发送一个特殊的响应，包含目标资源的URL，客户端收到响应后会发起新的请求去获取目标资源。在重定向的过程中，客户端会看到浏览器地址栏中的URL发生变化。重定向通常用于告知客户端资源已经被移到了其他位置，需要在新的URL上重新发起请求。

简而言之，请求转发是服务器端内部资源的交互过程，而重定向是告知客户端资源已经被移到了其他位置。

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

​	默认的跳转其实是**请求转发**的方式跳转的。我们也可以选择加上标识，在要跳转的路径前加上**forward:** 。这样SpringMVC也会帮我们进行请求转发。

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

​	**WEB-INF目录及下面的文件是受保护的，浏览器直接访问是访问不到的。**

​	不能够与@ResponseBody注解一起使用，因为@ResponseBody会把返回值写入响应体中。

​	视图解析器会在逻辑视图的基础上拼接得到物理视图。

~~~~java
    @RequestMapping("/testJumpToJsp")
    public String testJumpToJsp(){
//        实际上相当于： return "/WEB-INF/page/test.jsp";
        return "test";
    }
~~~~



### 4.2 不进行前后缀拼接

​	如果在配置了视图解析器的情况下，某些方法中并不想拼接前后缀去跳转。这种情况下我们可以在跳转路径前加**forward:** 或者**redirect:**进行标识。这样就不会进行前后缀的拼接了。

​	例如:

~~~~java
    @RequestMapping("/testJumpHtml")
    public String testJumpHtml(){
        //如果加了forward:  或者redirect: 视图解析器就不会进行前后缀的拼接
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
    // 获取Request、Response、Session等原生对象
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


    @RequestMapping("/getHeader") // 告诉Spring MVC从请求头中获取名为device-type的数据进行赋值
    public String getHeader(@RequestHeader(value = "device-type") String deviceType){
        System.out.println(deviceType);
        return "test";
    }
}

~~~~

或者**使用原生对象HttpServletRequest request**，String res = request.getHeader("device-type")

### 6.2 获取Cookie

​	在方法中定义一个参数，参数前加上**@CookieValue** 注解，知道要获取的cookie名即可获取对应cookie的值。

例如：

​	想要获取 JSESSIONID 的cookie值。则可以按照如下方式定义方法。

~~~~java
@Controller
public class RequestResponseController {

    @RequestMapping("/getCookie")// 告诉Spring MVC从cookie中获取名为JSESSIONID的数据进行赋值
    public String getCookie(@CookieValue("JSESSIONID") String sessionId){
        System.out.println(sessionId);
        return "test";
    }
}

~~~~



## 7.JSP开发模式（了解）

​	**如果我们使用JSP进行开发，那我们就需要在域中存数据，然后跳转到对应的JSP页面中，在JSP页面中获取域中的数据然后进行相关处理。**

​	使用如果是类似JSP的开发模式就会涉及到**往域中存数据**和**携带数据跳转页面**的操作。

​	所以我们来看下如果用SpringMVC进行相关操作。

**域、Request域、Session域**

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
        //往请求域存数据，实际上是存储到HttpServletRequest对象的一个map集合中，jsp页面再从map中获取数据
        model.addAttribute("name","三更");
        model.addAttribute("title","不知名Java教程UP主");
        return "testScope";
    }
}
~~~~

**jsp能够从RequestScope中获取到数据，因为是请求转发的方式进行页面跳转的，请求头是相同的。**

在jsp中获取相应的数据：

~~~jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>Title</title>
</head>
<body>
    ${requestScope.get("name")}
    ${requestScope.get("title")}
</body>
</html>
~~~



#### 7.1.2 使用ModelAndView

​	我们可以使用**ModelAndView**来往域中存数据和页面跳转。

例如：

~~~~java
@Controller
public class JspController {
    @RequestMapping("/testRquestScope2")
    public ModelAndView testRquestScope2(ModelAndView modelAndView){
        //往域中添加数据
        modelAndView.addObject("name","三更");
        modelAndView.addObject("title","不知名Java教程UP主");
        //页面跳转：相当于return "testScope"，也会被视图解析器添加前后缀
        modelAndView.setViewName("testScope");
        return modelAndView;
    }
}
~~~~

​	**注意要把modelAndView对象作为方法的返回值返回。**

在jsp中获取相应的数据：

~~~jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>Title</title>
</head>
<body>
    ${name}
    ${title}
</body>
</html>
~~~



### 7.2 从Request域中获取数据

​	我们可以使用**@RequestAttribute** 把他加在方法参数上，可以让SpringMVC帮我们从Request域中获取相关数据。

**Request域中的数据实际上存储在HttpServletRequest对象的map集合属性中。**

例如

~~~~java
@Controller
public class JspController {

    @RequestMapping("/testGetAttribute")
    public String   testGetAttribute(@RequestAttribute("org.springframework.web.servlet.HandlerMapping.bestMatchingPattern")
                                               String value,HttpServletRequest request){
        // 表示让SpringMVC帮我们从Request域中获取默认的key = org.springframework.web.servlet.HandlerMapping.bestMatchingPattern的value
        System.out.println(value);
        return "testScope";
    }
}

~~~~





### 7.3 往Session域存数据并跳转

​	1.我们可以使用**@SessionAttributes**注解来进行标识，用里面的属性来标识哪些数据要存入Session域。

2.也可以使用HttpSession对象，使用httpSession.setAttribute("name","三更")把数据存储到session域中



例如

​	我们要求访问  **/testSessionScope **这个路径时能往域中存**name**和**title**数据，然后跳转到 **/WEB-INF/page/testScope.jsp** 这个页面。在jsp中获取**Session域**中的数据。



​	则可以使用如下写法

~~~~java
@Controller
@SessionAttributes({"name"})//表示name这个数据也要存储一份到session域中
public class JspController {


    @RequestMapping("/testSessionScope")
    public String testSessionScope(Model model){
        // 默认向request域中存储，但是使用@SessionAttributes({"name"})进行了配置
        // 所以name这个数据也要存储一份到session域中
        model.addAttribute("name","三更");
        model.addAttribute("title","不知名Java教程UP主");
        return "testScope";
    }
}
~~~~

在jsp中获取相应的数据：

~~~jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>Title</title>
</head>
<body>
    ${requestScope.get("name")}
    ${requestScope.get("title")}
    <hr>
    ${sessionScope.get("name")}
    ${sessionScope.get("title")} // 空
</body>
</html>
~~~



### 7.4 获取Session域中数据

​	我们可以使用**@SessionAttribute**把他加在方法参数上，可以让SpringMVC帮我们从**Session域**中获取相关数据。

**Session域中的数据实际上存储在HttpSession对象的map集合属性中。**

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

# SpringMVC-03

## 1.拦截器（对Handler方法进行增强专用的AOP）

​	注意，自己去使用AOP进行增强时，应该是对Service进行增强，不能对Controller进行增强。

因为切面类会被放入父容器(Spring容器)，而我们的Controller是在子容器（MVC容器）中的。父容器中的切面类不能访问子容器的Controller方法来进行增强。

​	并且我们如果需要**对Controller进行增强，使用拦截器也会更加的好用**。

### 1.1 应用场景

​	如果我们想在多个Handler方法执行之前或者之后都进行一些处理，甚至某些情况下需要拦截掉，不让Handler方法执行。那么可以使用SpringMVC为我们提供的拦截器（底层也是aop实现的）。



### 1.2 拦截器和过滤器的区别

​	过滤器是在Servlet执行之前或者之后进行处理。而拦截器是对Handler（处理器）执行前后进行处理。

如图：

![image-20210516215721896](img\image-20210516215721896.png)



### 1.3 创建并配置拦截器

#### ①创建类实现HandlerInterceptor接口

~~~~java
public class MyInterceptor implements HandlerInterceptor {
}
~~~~

#### ②实现方法

~~~~java
public class MyInterceptor implements HandlerInterceptor {
    
    //在handler方法执行之前会被调用
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        System.out.println("preHandle");
        //返回值代表是否放行，如果为true则放行，如果为fasle则拦截，目标方法执行不到
        return true;
    }

    public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView modelAndView) throws Exception {
        System.out.println("postHandle");
    }

    public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) throws Exception {
        System.out.println("afterCompletion");
    }
}
~~~~

#### ③配置拦截器

在spring-mvc.xml中：

~~~~xml
    <!--配置拦截器拦截的规则与注入自定义拦截器对象,告诉ioc容器这是一个拦截器对象-->
    <mvc:interceptors>
        <mvc:interceptor>
            <!--
                    配置拦截器要拦截的路径
                    /*    代表当前一级路径，不包含子路径
                    /**   代表当前一级路径和多级路径，使用的更多

                    例如：
                        /test/*   这种会拦截下面这种路径/test/add  /test/delete
                                  但是拦截不了多级路径的情况例如  /test/add/abc  /test/add/abc/bcd
                        /test/**  这种可以拦截多级目录的情况，无论    /test/add还是/test/add/abc/bcd 都可以拦截
            -->
            <mvc:mapping path="/**"/>
            <!--配置排除拦截的路径-->
            <!--<mvc:exclude-mapping path="/"/>-->
            <!--配置拦截器对象注入容器-->
            <bean class="com.sangeng.interceptor.MyInterceptor"></bean>
        </mvc:interceptor>
    </mvc:interceptors>
~~~~



### 1.4 拦截器方法及参数详解

- preHandle方法会**在Handler方法执行之前执行**，我们可以在其中进行一些前置的判断或者处理。
- postHandle方法会**在Handler方法执行之后执行，如果在Handler方法中出现异常则不会执行**，我们可以在其中对域中的数据进行修改，也可以修改要跳转的页面(修改modelAndView对象即可实现，一般方法的返回值为**字符串时也会转换为modelAndView对象)**。
- afterCompletion方法**会在最后执行**，这个时候已经**没有办法对域中的数据进行修改**，也没有办法修改要跳转的页面。我们在这个方法中一般进行一些资源的释放。

~~~~java
    /**
     * 在handler方法执行之前会被调用
     * @param request 当前请求对象
     * @param response 响应对象
     * @param handler 相当于是真正能够处理请求的handler方法封装成的对象，对象中有这方法的相关信息
     * @return 返回值代表是否放行，如果为true则放行，如果为fasle则拦截，目标方法执行不到
     * @throws Exception
     */
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        System.out.println("preHandle");
        //返回值代表是否放行，如果为true则放行，如果为fasle则拦截，目标方法执行不到
        return true;
    }
~~~~

~~~~java
    /**
     * postHandle方法会在Handler方法执行之后执行
     * @param request 当前请求对象
     * @param response 响应对象
     * @param handler 相当于是真正能够处理请求的handler方法封装成的对象，对象中有这方法的相关信息
     * @param modelAndView handler方法执行后的modelAndView对象，我们可以修改其中要跳转的路径或者是域中的数据
     * @throws Exception
     */
    public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView modelAndView) throws Exception {
        System.out.println("postHandle");
    }
~~~~

~~~~java
    /**
     * afterCompletion方法会在最后执行
     * @param request 当前请求对象
     * @param response 响应对象
     * @param handler 相当于是真正能够处理请求的handler方法封装成的对象，对象中有这方法的相关信息
     * @param ex 异常对象
     * @throws Exception
     */
    public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) throws Exception {
        System.out.println("afterCompletion");
    }
~~~~



### 1.5 案例-登录状态拦截器

#### 1.5.1需求

​	我们的接口需要做用户登录状态的校验，如果用户没有登录则跳转到登录页面，登录的情况下则可以正常访问我们的接口。

#### 1.5.2 分析

​	怎么判断是否登录？

​			登录时往session写入用户相关信息，然后在其他请求中从session中获取这些信息，如果获取不到说明不是登录状态。

​	很多接口都要去写判断的代码，难道在每个Handler中写判断逻辑？

​			用拦截器，在拦截器中进行登录状态的判断。

​	登录接口是否应该进行拦截？

​			不能拦截

​	静态资源是否要进行拦截？

​			不能拦截

#### 1.5.3 步骤分析

​	①登录页面，请求发送给登录接口

​	②登录接口中，校验用户名密码是否正确（模拟校验即可，先不查询数据库）。

​				如果用户名密码正确，登录成功。把用户名写入session中。

​	 ③定义其他请求的Handler方法

​	 ④定义拦截器来进行登录状态判断

​	 			如果能从session中获取用户名则说明是登录的状态，则放行

​				 如果获取不到，则说明未登录，要跳转到登录页面。

#### 1.5.4 代码实现

##### 1.5.4.1 登录功能代码实现

###### 	①编写登录页面

~~~~html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
    <form method="post" action="/login">
        用户名：<input type="text" name="username">
        密码：<input type="password" name="password">
        <input type="submit">
    </form>
</body>
</html>
~~~~

###### 	②编写登录接口

​	接口中，校验用户名密码是否正确（模拟校验即可，先不查询数据库）。如果用户名密码正确，登录成功。把用户名写入session中。

~~~~java
@Controller
public class LoginController {

    @PostMapping("/login")
    public String longin(String username, String password, HttpSession session){
        //往session域中写入用户名用来代表登录成功
        session.setAttribute("username",username);
        return "/WEB-INF/page/success.jsp";
    }
}

~~~~



##### 1.5.4.2 登录状态校验代码实现

###### ①定义拦截器

~~~~java
public class LoginInterceptor implements HandlerInterceptor {
}
~~~~

###### ②重写方法，在preHandle方法中实现状态校验

~~~~java
public class LoginInterceptor implements HandlerInterceptor {

    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        //从session中获取用户名，判断是否存在
        HttpSession session = request.getSession();
        String username = (String) session.getAttribute("username");
        if(StringUtils.isEmpty(username)){
            //如果获取不到说明未登录 ，重定向跳转到登录页面
            String contextPath = request.getServletContext().getContextPath();
            // sendRedirect方法设置状态码为重定向304并且设置请求头location的值为contextPath+"/static/login.html,表示浏览器重新发送请求的路径
            response.sendRedirect(contextPath+"/static/login.html");
        }else{
            //如果获取到了，说明之前登录过。放行。
            return true;
        }
        return false;
    }
}
~~~~

###### ③配置拦截器

- ​	登录相关接口不应该拦截
- ​	静态资源不拦截

~~~~xml
    <mvc:interceptors>
        <mvc:interceptor>
            <!--要拦截的路径-->
            <mvc:mapping path="/**"/>
            <!--排除不拦截的路径-->
            <mvc:exclude-mapping path="/static/**"></mvc:exclude-mapping>
            <mvc:exclude-mapping path="/WEB-INF/page/**"></mvc:exclude-mapping>
            <mvc:exclude-mapping path="/login"></mvc:exclude-mapping>
            <bean class="com.sangeng.interceptor.LoginInterceptor"></bean>
        </mvc:interceptor>
    </mvc:interceptors>
~~~~



### 1.6 多拦截器执行顺序

​	如果我们配置了多个拦截器，拦截器的顺序是**按照配置的先后顺序**的。

​	这些拦截器中方法的执行顺序如图（**preHandler都返回true的情况下**）：

![image-20210517165433983](img\多拦截器执行顺序.png)

- 先执行preHandle,再执行postHandle,最后执行afterCompletion,不会出现篡位的情况。




如果**拦截器3的preHandle方法返回值为false**。执行顺序如图：

![image-20210519213325101](img\多拦截器执行顺序2.png)

- ​	只有所有拦截器都放行了，请求被Handler处理器处理之后，postHandle方法才会被执行。
- ​	只有当前拦截器放行了，当前拦截器的afterCompletion方法才会执行。



## 2.统一异常处理

​	我们在实际项目中**Dao层和Service层的异常都会被抛到Controller层**。但是如果我们在Controller的方法中都加上异常的try...catch处理也会显的非常的繁琐。

​	所以SpringMVC为我们提供了统一异常处理方案。可以把Controller层的异常进行统一处理。这样既提高了代码的复用性也让异常处理代码和我们的业务代码解耦。

​	一种是实现HandlerExceptionResolver接口的方式，一种是使用@ControllerAdvice注解的方式。



### 2.1 HandlerExceptionResolver

#### ①实现接口

~~~~java
public class MyHandlerExceptionResolver implements HandlerExceptionResolver {

}

~~~~



#### ②重写方法

~~~~java
public class MyHandlerExceptionResolver implements HandlerExceptionResolver {
    
    //如果handler中出现了异常，就会调用到该方法，我们可以在本方法中进行统一的异常处理。
    public ModelAndView resolveException(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) {// Object handler：发生异常的方法所封装的对象，Exception ex：出现的异常对象
        //获取异常信息，把异常信息放入域对象中
        String msg = ex.getMessage();
        ModelAndView modelAndView = new ModelAndView();
        modelAndView.addObject("msg",msg);
        //跳转到error.jsp
        modelAndView.setViewName("/WEB-INF/page/error.jsp");
        return modelAndView;
    }
}

~~~~



#### ③注入容器

​	可以使用注解注入也可以使用xml配置注入。这里使用注解注入的方式。在类上加**@Component**注解，注意要保证类能被组件扫描扫描到。

~~~~~java
@Component
public class MyHandlerExceptionResolver implements HandlerExceptionResolver {
	//....省略无关代码
}
~~~~~



### 2.2 @ControllerAdvice（重要）

#### ①创建类加上@ControllerAdvice注解进行标识

~~~~java
@ControllerAdvice
public class MyControllerAdvice {

}
~~~~



#### ②定义异常处理方法	

​	定义异常处理方法，使用**@ExceptionHandler**标识可以处理的异常。

~~~~java
@ControllerAdvice
public class MyControllerAdvice {

    @ExceptionHandler({NullPointerException.class,ArithmeticException.class})
    public ModelAndView handlerException(Exception ex){
        //如果出现了相关的异常，就会调用该方法
        String msg = ex.getMessage();
        ModelAndView modelAndView = new ModelAndView();
        //把异常信息存入域中
        modelAndView.addObject("msg",msg);
        //跳转到error.jsp
        modelAndView.setViewName("/WEB-INF/page/error.jsp");
        return modelAndView;
    }
}
~~~~



#### ③注入容器

​	可以使用注解注入也可以使用xml配置注入。这里使用注解注入的方式。在类上加**@Component**注解，注意要保证类能被组件扫描扫描到。

~~~~java
@ControllerAdvice
@Component
public class MyControllerAdvice {
	//省略无关代码
}
~~~~



### 2.3 总结

​	我们在实际项目中一般会选择使用@ControllerAdvice 来进行异常的统一处理。

​	因为如果在前后端不分离的项目中，异常处理一般是跳转到错误页面，让用户有个更好的体验。而前后端分离的项目中，异常处理一般是把异常信息封装到Json中写入响应体。无论是哪种情况，使用@ControllerAdvice的写法都能比较方便的实现。

​	例如下面这种方式就是前后端分离的异常处理方案，把异常信息封装到对象中，转换成json写入响应体。

~~~~java
@ControllerAdvice
@Component
public class MyControllerAdvice {

    @ExceptionHandler({NullPointerException.class,ArithmeticException.class})
    @ResponseBody
    public Result handlerException(Exception ex){
        Result result = new Result();
        result.setMsg(ex.getMessage());
        result.setCode(500);
        return result;
    }
}

~~~~



## 3.文件上传

### 3.1 文件上传要求

​	Http协议规定了我们在进行文件上传时的请求格式要求。所以在进行文件上传时，除了在表单中增加一个用于**上传文件的表单项（input标签，type=file）**外必须要满足以下的条件才能进行上传。

#### ①请求方式为POST请求

​	如果是使用表单进行提交的话，可以把form标签的**method**属性设置为**POST**。例如:

~~~~html
    <form action="/upload" method="post">

    </form>
~~~~

#### ②请求头**Content-Type**必须为**multipart/form-data**

​	如果是使用表单的话可以把表单的**entype**属性设置成**multipart/form-data**。例如：

~~~~html
    <form action="/upload" method="post" enctype="multipart/form-data">

    </form>
~~~~



​	



### 3.2 SpringMVC接收上传过来的文件

​	SpringMVC使用commons-fileupload的包对文件上传进行了封装，我们只需要引入相关依赖和进行相应配置就可以很轻松的实现文件上传的功能。

#### ①导入依赖

~~~~xml
        <!--commons文件上传，如果需要文件上传功能，需要添加本依赖-->
        <dependency>
            <groupId>commons-fileupload</groupId>
            <artifactId>commons-fileupload</artifactId>
            <version>1.4</version>
        </dependency>
~~~~

#### ②配置

~~~~xml
  <!--
            文件上传解析器
            注意：id 必须为 multipartResolver
        -->
    <bean id="multipartResolver" class="org.springframework.web.multipart.commons.CommonsMultipartResolver">
        <!-- 设置默认字符编码 -->
        <property name="defaultEncoding" value="utf-8"/>
        <!-- 一次请求上传的文件的总大小的最大值，单位是字节-->
        <property name="maxUploadSize" value="#{1024*1024*100}"/>
        <!-- 每个上传文件大小的最大值，单位是字节-->
        <property name="maxUploadSizePerFile" value="#{1024*1024*50}"/>
    </bean>
~~~~



#### ③接收上传的文件数据并处理

上传表单：提交参数必须有name属性

~~~~html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
    <form action="/upload" method="post" enctype="multipart/form-data">
        <input type="file" name="uploadFile">
        <input type="submit">
    </form>
</body>
</html>
~~~~



~~~~java
@Controller
public class UploadController {

    @RequestMapping("/upload")
    public String upload(MultipartFile uploadFile) throws IOException {
        //文件存储：把上传上来的文件存储的对应的路径
        uploadFile.transferTo(new File("test.sql"));
        return "/success.jsp";
    }
}
~~~~



注意：方法参数名要和表单提交上来的参数名一致(类似QueryString格式的提交)。

其他例子：

~~~java
/**
     * 接收前端上传的图片(MultipartFile对象)存储到七牛云的图床并把图片的链接(url)响应给前端
     */
    @Override
    public ResponseResult uploadImg(MultipartFile img) {//使用MultipartFile类接收图片数据
        //判断上传的文件类型
        String originalFilename = img.getOriginalFilename();
        assert originalFilename != null;
        //只能上传png格式
        if(!originalFilename.endsWith(".png")){
            throw new SystemException(AppHttpCodeEnum.FILE_TYPE_ERROR);
        }
        //生成文件的存储路径（原始路径+新的文件名）
        String filePath = PathUtils.generateFilePath(originalFilename);
        //存储到七牛云的图床中并返回url
        String url = uploadOSS(img,filePath);
        return ResponseResult.okResult(url);
    }

~~~



### 3.3 MultipartFile常见用法

- 获取上传文件的原名

  ~~~~java
  uploadFile.getOriginalFilename()
  ~~~~

- 获取文件类型的MIME类型

  ~~~~java
  uploadFile.getContentType()
  ~~~~

- 获取上传文件的大小

  ~~~~java
  uploadFile.getSize()
  ~~~~

- 获取对应上传文件的输入流

  ~~~~java
  uploadFile.getInputStream()
  ~~~~

  

## 4.文件下载

### 4.1 文件下载要求

​	如果我们想提供文件下载的功能。HTTP协议要求我们的必须满足如下规则。

#### ①设置响应头Content-Type

​	要求把提供下载文件的MIME类型作为响应头Content-Type的值

#### ②设置响应头Content-disposition

​	要求把文件名经过URL编码后的值写入响应头Content-disposition。但是要求符合以下格式，因为这样可以解决不同浏览器中文文件名 乱码问题。

~~~~java
Content-disposition: attachment; filename=%E4%B8%8B%E6%B5%B7%E5%81%9Aup%E4%B8%BB%E9%82%A3%E4%BA%9B%E5%B9%B4.txt;filename*=utf-8''%E4%B8%8B%E6%B5%B7%E5%81%9Aup%E4%B8%BB%E9%82%A3%E4%BA%9B%E5%B9%B4.txt
~~~~

#### ③文件数据写入响应体中



### 4.2 代码实现

#### 存储在OSS中



​	我们可以使用之前封装的下载工具类实现文件下载

工具类代码：

~~~~java
import javax.servlet.ServletContext;
import javax.servlet.ServletOutputStream;
import javax.servlet.http.HttpServletResponse;
import java.io.File;
import java.io.FileInputStream;
import java.net.URLEncoder;

public class DownLoadUtils {
    /**
     * 该方法可以快速实现设置两个下载需要的响应头和把文件数据写入响应体
     * @param filePath 该文件的相对路径
     * @param context  ServletContext对象
     * @param response
     * @throws Exception
     */
    public static void downloadFile(String filePath, ServletContext context, HttpServletResponse response) throws Exception {
        // 获得绝对路径
        String realPath = context.getRealPath(filePath);
        File file = new File(realPath);
        String filename = file.getName();
        FileInputStream fis = new FileInputStream(realPath);
        String mimeType = context.getMimeType(filename);//获取文件的mime类型
        // 1.设置响应头Content-Type
        response.setHeader("content-type",mimeType);
        String fname= URLEncoder.encode(filename,"UTF-8");
        // 2.设置响应头Content-disposition
        response.setHeader("Content-disposition","attachment; filename="+fname+";"+"filename*=utf-8''"+fname);
        ServletOutputStream sos = response.getOutputStream();
        byte[] buff = new byte[1024 * 8];
        int len = 0;
        // 写入响应体中
        while((len = fis.read(buff)) != -1){
            sos.write(buff,0,len);
        }
        sos.close();
        fis.close();
    }
}

~~~~

注意：**响应体中写完数据后就会进行提交，无法修改数据了**，如果此时进行页面跳转到jsp页面（相当于写入jsp页面的内容到响应体中），就会报错已经提交，不能再开启session进行写入了。



Handler方法定义

~~~~java
@Controller
public class DownLoadController {

    @RequestMapping("/download")
    public void download(HttpServletRequest request, HttpServletResponse response) throws Exception {
        //文件下载
        DownLoadUtils.downloadFile("/WEB-INF/file/下海做UP主那些年.txt",request.getServletContext(),response);
    }
}
~~~~

#### 存储在本地

