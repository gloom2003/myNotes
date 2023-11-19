# SpringBoot-常见场景

## 1.热部署(非必要)

​	SpringBoot为我们提供了一个方便我们开发测试的工具dev-tools。使用后可以实现热部署的效果。当我们运行了程序后对程序进行了修改并且离开了idea的界面时，程序会自动快速的重启。

​	 原理是**使用了两个ClassLoder(类加载器)**,一个ClassLoader加载哪些不会改变的类(第三方jar包),另一个ClassLoader加载会更改的类.称之为Restart ClassLoader,这样在有代码更改的时候,原来的Restart Classloader被丢弃,重新创建一个Restart ClassLoader,由于需要加载的类相比较少,所以实现了较快的重启。

​	

### 1.1 准备工作

①设置IDEA自动编译

​	 在idea中的setting做下面配置 

![自动编译配置](C:/Users/GLOOM/Desktop/for zip/not system/sangGeng files/需要三连资料/SpringBoot/img/自动编译配置.png)



②设置允许程序运行时自动启动

​	 ctrl + shift + alt + / 这组快捷键后会有一个小弹窗，点击Registry 就会进入下面的界面，找到下面的配置项并勾选，勾选后直接点close 

![允许运行时自动启动](C:/Users/GLOOM/Desktop/for zip/not system/sangGeng files/需要三连资料/SpringBoot/img/允许运行时自动启动.png)



### 1.2使用

①添加依赖

~~~~xml
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-devtools</artifactId>
            <optional>true</optional>
        </dependency>
~~~~

②触发热部署

​	当我们在修改完代码或者静态资源后可以切换到其它软件，让IDEA自动进行编译，自动编译后就会触发热部署。

​	或者使用Ctrl+F9手动触发重新编译。





## 2.单元测试

​	我们可以使用SpringBoot整合Junit进行单元测试。

​	**Spring Boot 2.2.0 版本开始引入 JUnit 5 作为单元测试默认库**。

​	

### 2.1 使用

#### ①添加依赖

~~~~xml
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
        </dependency>
~~~~

#### ②编写测试类

~~~~java
import com.sangeng.controller.HelloController;
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;

@SpringBootTest
public class ApplicationTest {

    @Autowired
    private HelloController helloController;

    @Test
    public void testJunit(){
        System.out.println(1);
        System.out.println(helloController);
    }
}
~~~~



**注意：测试类所在的包需要和启动类是在同一个包下。否则就要使用如下写法指定启动类。**

~~~~java
import com.sangeng.controller.HelloController;
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;

//classes属性来指定启动类
@SpringBootTest(classes = HelloApplication.class)
public class ApplicationTest {

    @Autowired
    private HelloController helloController;

    @Test
    public void testJunit(){
        System.out.println(1);
        System.out.println(helloController);
    }
}

~~~~



### 2.2 兼容老版本

​	如果是对老项目中的SpringBoot进行了版本升级会发现之前的单元测试代码出现了一些问题。

​	因为Junit5和之前的Junit4有比较大的不同。

​	先看一张图：

 ![img](C:/Users/GLOOM/Desktop/for zip/not system/sangGeng files/需要三连资料/SpringBoot/img/junit5.jpeg) 

​	从上图可以看出  **JUnit 5 = JUnit Platform + JUnit Jupiter + JUnit Vintage**



- **JUnit Platform**： 这是Junit提供的平台功能模块，通过它，其它的测试引擎也可以接入
- **JUnit JUpiter**：这是JUnit5的核心，是一个基于JUnit Platform的引擎实现，它包含许多丰富的新特性来使得自动化测试更加方便和强大。
- **JUnit Vintage**：这个模块是兼容JUnit3、JUnit4版本的测试引擎，使得旧版本的自动化测试也可以在JUnit5下正常运行。



​	虽然Junit5包含了**JUnit Vintage**来兼容JUnit3和Junit4，但是**SpringBoot 2.4 以上版本对应的spring-boot-starter-test移除了默认对** **Vintage 的依赖。**所以当我们仅仅依赖spring-boot-starter-test时会发现之前我们使用的@Test注解和@RunWith注解都不能使用了。

​	所以**只要导入这个被移除的JUnit Vintage依赖即可进行兼容**

~~~~xml
        <dependency>
            <groupId>org.junit.vintage</groupId>
            <artifactId>junit-vintage-engine</artifactId>
            <scope>test</scope>
        </dependency>
~~~~



**注意：**

​		**org.junit.Test对应的是Junit4的版本，就搭配@RunWith注解来使用。**

SpringBoot2.2.0之前版本的写法

~~~~java
import com.sangeng.controller.HelloController;
//import org.junit.jupiter.api.Test;
import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.test.context.junit4.SpringRunner;

//classes属性来指定启动类
@SpringBootTest
@RunWith(SpringRunner.class)
public class ApplicationTest {

    @Autowired
    private HelloController helloController;

    @Test
    public void testJunit(){
        System.out.println(1);
        System.out.println(helloController);
    }
}
~~~~





## 3.整合mybatis

### 3.1 准备工作

①数据准备

~~~~mysql
/*Table structure for table `user` */
DROP TABLE IF EXISTS `user`;
CREATE TABLE `user` (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `username` varchar(50) DEFAULT NULL,
  `age` int(11) DEFAULT NULL,
  `address` varchar(50) DEFAULT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB AUTO_INCREMENT=10 DEFAULT CHARSET=utf8;

/*Data for the table `user` */

insert  into `user`(`id`,`username`,`age`,`address`) values (2,'pdd',25,'上海'),(3,'UZI',19,'上海11'),(4,'RF',19,NULL),(6,'三更',14,'请问2'),(8,'test1',11,'cc'),(9,'test2',12,'cc2');

/*!40101 SET SQL_MODE=@OLD_SQL_MODE */;
/*!40014 SET FOREIGN_KEY_CHECKS=@OLD_FOREIGN_KEY_CHECKS */;
/*!40014 SET UNIQUE_CHECKS=@OLD_UNIQUE_CHECKS */;
/*!40111 SET SQL_NOTES=@OLD_SQL_NOTES */;

~~~~

②实体类

~~~~java
import lombok.AllArgsConstructor;
import lombok.Data;
import lombok.NoArgsConstructor;

@Data
@AllArgsConstructor
@NoArgsConstructor
public class User {
    private Integer id;
    private String username;
    private Integer age;
    private String address;
}

~~~~





### 3.2 整合步骤

​	查看Mybatis与SpringBoot对应的版本

​	github: https://github.com/mybatis/spring-boot-starter/

#### ①依赖

~~~~xml
        <!--mybatis启动器-->
        <dependency>
            <groupId>org.mybatis.spring.boot</groupId>
            <artifactId>mybatis-spring-boot-starter</artifactId>
            <version>2.2.0</version>
        </dependency>
        <!--mysql驱动-->
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <scope>runtime</scope>
        </dependency>
~~~~

#### ②配置数据库信息

~~~~yml
spring:
  datasource:
    url: jdbc:mysql://localhost:3306/test?characterEncoding=utf-8&serverTimezone=UTC
    username: root
    password: root
    driver-class-name: com.mysql.cj.jdbc.Driver
~~~~

#### ③配置mybatis相关配置

~~~~yml
mybatis:
  mapper-locations: classpath:mapper/*Mapper.xml # mapper映射文件路径
  type-aliases-package: com.sangeng.domain   # 配置哪个包下的类有默认的别名

~~~~

#### ④编写Mapper接口   

**注意:在接口上加上@Mapper 和@Repository 注解**(值得一提的是，最简洁的写法是只写@Mapper注解而不写@Repository，因为运行时Mybatis会自动把这个Bean注入容器中，不需要我们手动的进行注入，但是不写的话，idea编译时会显示红色波浪线，表示在容器中找不到这个类型的Bean)

**也可以在启动类上使用@MapperScan("com.kana.mapper")注解，指定扫描的mapper包**

~~~~java
@Repository
@Mapper // 告诉Mybatis这是一个mapper接口，否则Mybatis识别不出来
public interface UserMapper {
    public List<User> findAll();
}

~~~~

#### ⑤编写mapper接口对应的xml文件

~~~~xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd" >
<mapper namespace="com.sangeng.mapper.UserMapper">
    <select id="findAll" resultType="com.sangeng.domain.User">
        select * from user
    </select>
</mapper>
~~~~

#### ⑥测试

~~~~java
@SpringBootTest(classes = HelloApplication.class)
public class SpringMyTest {

    @Autowired
    UserMapper userMapper;


    @Test
    public void tesMapper(){
        System.out.println(userMapper.findAll());
    }
}
~~~~



## 4.Web开发

### 4.1 静态资源访问

​	由于SpringBoot的项目是**打成jar包而不是war包**的所以没有之前web项目的那些web资源目录(webapps)。

​	那么我们的静态资源要放到哪里呢？

​	从SpringBoot官方文档中我们可以知道，我们可以把静态资源放到 `resources/static`   (或者 `resources/public` 或者`resources/resources` 或者 `resources/META-INF/resources`) 中即可。

​	静态资源放完后，**/ 相当于 resources/static/**

​	例如我们想访问文件：resources/static/index.html  只需要在访问时资源路径写成/index.html即可。  

​	例如我们想访问文件：resources/static/pages/login.html  访问的资源路径写成： /pages/login.html



#### 4.1.1 修改静态资源访问路径

**静态资源访问路径，即：（想要访问静态资源时应该访问的路径，访问路径后会跳转到静态资源存放目录中）**

​	**注意：**

​	SpringBoot默认的静态资源路径匹配为/**,意味着请求/index.html时会访问resources/static/index.html 。

​	如果想要修改可以通过 `spring.mvc.static-path-pattern` 这个配置进行修改。

​	例如想让访问静态资源的url必须前缀有/res。例如/res/index.html 才能访问到static目录中的。我们可以修改如下：

在application.yml中

~~~~yml
spring:
  mvc:
    static-path-pattern: /res/** #修改静态资源访问路径，设置为访问res下的目录及其子目录，表示请求/res/index.html时才可以访问resources/static/index.html
~~~~



#### 4.1.2 修改静态资源存放目录

**静态资源存放目录，即：存放静态资源的目录(可以有多个)**

​	我们可以修改 spring.web.resources.static-locations 这个配置来修改静态资源的存放目录。

​	例如:

~~~~yml
spring:
  web:
    resources:
      static-locations:
        - classpath:/sgstatic/ 
        - classpath:/static/
~~~~



### 4.4 响应体响应数据

​	无论是RestFul风格还是我们之前web阶段接触过的异步请求，都需要把数据转换成Json放入响应体中。



#### 4.4.1 数据放到响应体

​	我们的SpringMVC为我们提供了**@ResponseBody**来非常方便的把Json放到响应体中。

​	**@ResponseBody**可以加在哪些东西上面？类上和方法上



#### 4.4.2 数据转换成Json

##### 4.4.2.1 配置

​	SpringBoot项目中使用了web的start后，不需要进行额外的依赖和配置



##### 4.4.2.2 使用

​	只要把要转换的数据直接作为方法的返回值返回即可。SpringMVC会帮我们把返回值转换成json。具体代码请参考范例。



#### 4.4.3 范例

~~~~java
@Controller
@RequestMapping("/response")
public class ResponseController {

    @RequestMapping("/user/{id}")
    @ResponseBody
    public User findById(@PathVariable("id") Integer id){
        User user = new User(id, "三更草堂", 15, null);
        return user;
    }
}
~~~~



### 4.5 跨域请求

#### 4.5.1 什么是跨域

​	浏览器出于安全的考虑（移动端发起请求并不会发生这种错误），使用 XMLHttpRequest对象(AJAX)发起 HTTP请求时必须遵守同源策略，否则就是跨域的HTTP请求，默认情况下是被禁止的。 同源策略要求源相同才能正常进行通信，即**协议、域名、端口号都完全一致(主要是端口不同导致的)**。 

为什么移动端发起请求并不会发生这种错误？

​	gpt4回答：

​	对于移动端（如手机或平板电脑）的非浏览器应用（通常被称作“**原生应用**”），它们不受同源策略的约束。原生应用可以使用各自平台（如iOS中的`NSURLSession`，Android中的`HttpURLConnection`或`OkHttpClient`）的网络API向任何服务器发送HTTP请求。由于它们不执行在浏览器的安全上下文中，因此可以自由地发起跨域请求。

要注意的是，虽然原生应用可以绕过同源策略，但它们仍需要考虑服务器在CORS（跨源资源共享）策略上的设定。如果服务器端没有正确配置CORS响应头来允许来自其他源的请求，这些请求可能会由于无法满足CORS预检要求而失败。



#### 4.5.2 CORS解决跨域

​	CORS是一个W3C标准，全称是”跨域资源共享”（Cross-origin resource sharing），允许浏览器向跨源服务器，发出XMLHttpRequest请求，从而克服了AJAX只能同源使用的限制。

​	它通过使服务器增加一个特殊的Header[Access-Control-Allow-Origin]来告诉客户端跨域的限制（客户端需要看服务器是否同意），如果浏览器支持CORS、并且判断Origin通过的话，就会允许XMLHttpRequest发起跨域请求。

​	**大致流程：**

​	发起跨域请求时，**请求头**中会有**Origin:**http: //localhost:63379属性，表示当前浏览器发起了一个跨域请求，发起请求的地址为http: //localhost:63379。如果服务器中配置了允许这个地址进行跨域访问，则**响应头**中会有**Access-Control-Allow-Origin**：http: //localhost:63379属性，表示允许http: //localhost:63379进行跨域访问。

​	

#### 4.5.3 SpringBoot使用CORS解决跨域

##### 1.使用@CrossOrigin

可以在支持跨域的方法上或者是Controller上加上@CrossOrigin注解。

~~~~java
@RestController
@RequestMapping("/user")
@CrossOrigin
public class UserController {

    @Autowired
    private UserServcie userServcie;

    @RequestMapping("/findAll")
    public ResponseResult findAll(){
        //调用service查询数据 ，进行返回
        List<User> users = userServcie.findAll();

        return new ResponseResult(200,users);
    }
}

~~~~



##### 2. 实现WebMvcConfigurer 接口

重写addCorsMappings 方法，**配置CorsInterceptor拦截器**

在CorsInterceptor拦截器中会读取并按照下面配置的信息进行跨域的处理:

~~~~java
@Configuration
public class CorsConfig implements WebMvcConfigurer {

    @Override
    public void addCorsMappings(CorsRegistry registry) {
      // 设置允许跨域的路径
        registry.addMapping("/**")
                // 设置允许跨域请求的域名
                .allowedOriginPatterns("*")
                // 是否允许cookie
                .allowCredentials(true)
                // 设置允许的请求方式
                .allowedMethods("GET", "POST", "DELETE", "PUT")
                // 设置允许的header属性
                .allowedHeaders("*")
                // 跨域允许持续的时间 3600秒，有的复杂请求例如put请求，会在发起跨域put请求之前会先发送一个请求来询问服务器是否允许跨域，服务器允许跨域后，在接下来的3600秒内发起put的跨域请求后，就不需要再次向服务器进行确认了。
                .maxAge(3600);
    }
}
~~~~

发起一个跨域的post请求时，会先发送一个**(OPTIONS方法)Request Method:OPTIONS**的请求来询问是否允许跨域。

### 4.6 登录案例

#### 4.6.0 登录案例



##### 4.6.0.1 思路分析

​		在前后端分离的场景中，很多时候会采用token的方案进行登录校验。

​		**大致流程：**

​		登录成功时，后端会根据一些用户信息(id...)生成一个token字符串返回给前端。

​		前端会存储这个token。以后前端发起请求时如果有token就会把token放在请求头中发送给后端。

​		后端接口就可以获取请求头中的token信息进行解析，如果解析不成功说明token超时了或者不是正确的token，相当于是未登录状态。解析成功则登录成功，可以访问一些需要权限的资源。

​		如果解析成功，说明前端是已经登录过的。

##### 4.6.0.2 Token生成方案-JWT

​		本案例采用目前企业中运用比较多的JWT来生成token。

​		**token方案的优点：**采用JWT方案来生成token可以统一实现前端与移动端的登录校验流程，这是之前的Session方案不能做到的，因为**移动端没有Session的概念。**

​		**使用UUID类来生成唯一的id，UUID.randomUUID().toString().**

​		使用时先引入相关依赖

~~~~xml
        <dependency>
            <groupId>io.jsonwebtoken</groupId>
            <artifactId>jjwt</artifactId>
            <version>0.9.0</version>
        </dependency>
~~~~

​		然后可以使用下面的工具类来生成和解析token

~~~~java
import io.jsonwebtoken.Claims;
import io.jsonwebtoken.JwtBuilder;
import io.jsonwebtoken.Jwts;
import io.jsonwebtoken.SignatureAlgorithm;

import javax.crypto.SecretKey;
import javax.crypto.spec.SecretKeySpec;
import java.util.Base64;
import java.util.Date;
import java.util.UUID;

/**
 * JWT工具类
 */
public class JwtUtil {

    //有效期为
    public static final Long JWT_TTL = 60 * 60 *1000L;// 60 * 60 *1000  一个小时
    //设置秘钥明文
    public static final String JWT_KEY = "sangeng";

    /**
     * 创建token
     * @param id  使用UUID类生成一个不重复的id
     * @param subject 传入要加密的内容(用户的相关信息,如id)
     * @param ttlMillis
     * @return
     */
    public static String createJWT(String id, String subject, Long ttlMillis) {

        SignatureAlgorithm signatureAlgorithm = SignatureAlgorithm.HS256;
        long nowMillis = System.currentTimeMillis();
        Date now = new Date(nowMillis);
        if(ttlMillis==null){
            ttlMillis=JwtUtil.JWT_TTL;
        }
        long expMillis = nowMillis + ttlMillis;
        Date expDate = new Date(expMillis);
        SecretKey secretKey = generalKey();

        JwtBuilder builder = Jwts.builder()
                .setId(id)              //唯一的ID
                .setSubject(subject)   // 主题  可以是JSON数据
                .setIssuer("sg")     // 签发者
                .setIssuedAt(now)      // 签发时间
                .signWith(signatureAlgorithm, secretKey) //使用HS256对称加密算法签名, 第二个参数为秘钥
                .setExpiration(expDate);// 设置过期时间
        return builder.compact();
    }

    /**
     * 生成加密后的秘钥 secretKey
     * @return
     */
    public static SecretKey generalKey() {
        byte[] encodedKey = Base64.getDecoder().decode(JwtUtil.JWT_KEY);
        SecretKey key = new SecretKeySpec(encodedKey, 0, encodedKey.length, "AES");
        return key;
    }
    
    /**
     * 解析
     *
     * @param jwt
     * @return
     * @throws Exception
     */
    public static Claims parseJWT(String jwt) throws Exception {
        SecretKey secretKey = generalKey();
        return Jwts.parser()
                .setSigningKey(secretKey)
                .parseClaimsJws(jwt)
                .getBody();
    }


}
~~~~

##### 4.6.0.3 登录接口实现


SystemUserController

~~~~java
import com.sangeng.domain.ResponseResult;
import com.sangeng.domain.SystemUser;
import com.sangeng.service.SystemUserService;
import com.sangeng.utils.JwtUtil;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.RequestBody;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

import java.util.HashMap;
import java.util.Map;
import java.util.UUID;

@RestController
@RequestMapping("/sys_user")
public class SystemUserController {
    @Autowired
    private SystemUserService userService;

    @PostMapping("/login")
    public ResponseResult login(@RequestBody SystemUser user) {
        //校验用户名密码是否正确
        SystemUser loginUser = userService.login(user);
        // 可能不止返回token这一个数据，使用map进行统一存储（顺便命名）
        Map<String, Object> map;
        if (loginUser != null) {
            //如果正确 生成token返回
            map = new HashMap<>();
            String token = JwtUtil.createJWT(UUID.randomUUID().toString(), String.valueOf(loginUser.getId()), null);
            map.put("token", token);
        } else {
            //如果不正确 给出相应的提示
            return new ResponseResult(300, "用户名或密码错误，请重新登录");
        }
        return new ResponseResult(200, "登录成功", map);
    }
}

~~~~



#### 4.6.1 拦截器的概念

​		**拦截器在Handler方法之前，过滤器在Servlet之前。**

#### 4.6.1 使用步骤

##### ①创建类实现HandlerInterceptor接口

~~~~java
public class LoginInterceptor implements HandlerInterceptor {
}
~~~~

##### ②实现方法

~~~~java
@Component
public class LoginInterceptor implements HandlerInterceptor {

    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        //获取请求头中的token
        String token = request.getHeader("token");
        //判断token是否为空，如果为空也代表未登录 提醒重新登录（401）
        if(!StringUtils.hasText(token)){
            response.sendError(HttpServletResponse.SC_UNAUTHORIZED);
            return false;
        }
        //解析token看看是否成功
        try {
            Claims claims = JwtUtil.parseJWT(token);
            String subject = claims.getSubject();
            System.out.println(subject);
        } catch (Exception e) {
            e.printStackTrace();
            //如果解析过程中没有出现异常说明是登录状态
            //如果出现了异常，说明未登录，提醒重新登录（401）
            response.sendError(HttpServletResponse.SC_UNAUTHORIZED);
            return false;
        }
        return true;
    }
}
~~~~

##### ③配置拦截器(实现WebMvcConfigurer接口重写方法代替xml配置)

**注意**：如果这个工程（模块）中有**静态资源**，则应该默认放行不拦截。


~~~~java
@Configuration
public class LoginConfig implements WebMvcConfigurer {

    @Autowired
    private LoginInterceptor loginInterceptor;

    @Override
    public void addInterceptors(InterceptorRegistry registry) {
        registry.addInterceptor(loginInterceptor)//添加拦截器
            .addPathPatterns("/**")  //配置拦截路径
            .excludePathPatterns("/sys_user/login");//配置排除路径
    }
}
~~~~



### 4.7 异常统一处理

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

    @ExceptionHandler(RuntimeException.class)
    @ResponseBody
    public ResponseResult handlerException(Exception e){
        //获取异常信息，存放如ResponseResult的msg属性
        String message = e.getMessage();
        ResponseResult result = new ResponseResult(300,message);
        //把ResponseResult作为返回值返回，要求到时候转换成json存入响应体中
        return result;
    }
}
~~~~





### 4.8 获取web原生对象

​	我们之前在web阶段我们经常要使用到request对象，response，session对象等。我们也可以通过SpringMVC获取到这些对象。（不过在MVC中我们很少获取这些对象，因为有更简便的方式，避免了我们使用这些原生对象相对繁琐的API。）

​	我们只需要在方法上添加对应类型的参数即可，但是注意数据类型不要写错了，SpringMVC会把我们需要的对象传给我们的形参。

~~~~java
@RestController
public class TestController {

    @RequestMapping("/getRequestAndResponse")
    public ResponseResult getRequestAndResponse(HttpServletRequest request, HttpServletResponse response, HttpSession session){
        System.out.println(request);
        return new ResponseResult(200,"成功");
    }
}

~~~~



### 4.9 自定义参数解析（如果有相应的注解，则执行相应的方法,把方法的返回值进行赋值）

​	如果我们想实现像获取请求体中的数据那样，在Handler方法的参数上增加一个@RepuestBody注解就可以获取到对应的数据的话。

​	可以**使用HandlerMethodArgumentResolver来实现自定义的参数解析**。

①定义用来标识的注解

~~~~java
@Target(ElementType.PARAMETER) // 指定注解可以作用于方法的参数上
@Retention(RetentionPolicy.RUNTIME)
public @interface CurrentUserId {

}
~~~~

②创建类实现HandlerMethodArgumentResolver接口并重写其中的方法

**注意加上@Component注解注入Spring容器**

~~~~java
@Component
public class UserIdArgumentResolver implements HandlerMethodArgumentResolver {

    //判断方法参数是否能使用当前的参数解析器进行解析
    @Override
    public boolean supportsParameter(MethodParameter parameter) {
        //如果方法参数有加上CurrentUserId注解，就能把被我们的解析器解析
        return parameter.hasParameterAnnotation(CurrentUserId.class);
    }
    //进行参数解析的方法，可以在方法中获取对应的数据，然后把数据作为返回值返回。方法的返回值就会赋值给对应的方法参数
    @Override
    public Object resolveArgument(MethodParameter parameter, ModelAndViewContainer mavContainer, NativeWebRequest webRequest, WebDataBinderFactory binderFactory) throws Exception {
        //获取请求头中的token
        String token = webRequest.getHeader("token");
        if(StringUtils.hasText(token)){
            //解析token，获取userId
            Claims claims = JwtUtil.parseJWT(token);
            String userId = claims.getSubject();
            //返回结果
            return userId;
        }
        return null;
    }
}
~~~~

③配置参数解析器

~~~~java
@Configuration
public class ArgumentResolverConfig implements WebMvcConfigurer {

    @Autowired //注意：这里需要使用子类UserIdArgumentResolver来获取对象，不能使用父类
    private UserIdArgumentResolver userIdArgumentResolver;

    @Override
    public void addArgumentResolvers(List<HandlerMethodArgumentResolver> resolvers) {
        // 在参数解析器列表中直接添加即可
        resolvers.add(userIdArgumentResolver);
    }
}
~~~~



④测试

在需要获取UserId的方法中增加对应的方法参数然后使用@CurrentUserId进行标识即可获取到数据

~~~~java
@RestController
@RequestMapping("/user")
public class UserController {

    @Autowired
    private UserServcie userServcie;

    @RequestMapping("/findAll")
    public ResponseResult findAll(@CurrentUserId String userId) throws Exception {
        System.out.println(userId);

        return new ResponseResult(200,users);
    }
}
~~~~



### 4.10 声明式事务

​	**保证一组数据库的操作，要么同时成功，要么同时失败**

​	对数据库的操作通过mapper层的方法来实现，而service则是通过操作mapper的方法来进行对数据库的操作，使用**@Transactional注解放在Service层中。**

​	直接在需要事务控制的方法上加上对应的注解**@Transactional**

~~~~java
@Service
public class UserServiceImpl implements UserServcie {

    @Autowired
    private UserMapper userMapper;

    @Override
    public List<User> findAll() {
        return userMapper.findAll();
    }

    @Override
    @Transactional
    public void insertUser() {
        //添加2个用户到数据库
        User user = new User(null,"sg666",15,"上海");
        User user2 = new User(null,"sg777",16,"北京");
        userMapper.insertUser(user);
        System.out.println(1/0);
        userMapper.insertUser(user2);
    }


}
~~~~



### 4.11 AOP

​		在SpringBoot中**默认是开启AOP功能的**。如果不想开启AOP功能可以使用如下配置设置为false(一般使用不到)

~~~~yml
spring:
  aop:
    auto: false
~~~~



#### 4.11.1 使用步骤

①添加依赖

~~~~xml
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-aop</artifactId>
        </dependency>
~~~~

②自定义注解

~~~~java
@Target(ElementType.METHOD)
@Retention(RetentionPolicy.RUNTIME)
public @interface InvokeLog {
}

~~~~

③定义切面类

~~~~java
@Aspect  //标识这是一个切面类
@Component
public class InvokeLogAspect {

    //确定切点
    @Pointcut("@annotation(com.sangeng.aop.InvokeLog)")
    public void pt(){
    }

    @Around("pt()")
    public Object printInvokeLog(ProceedingJoinPoint joinPoint){
        //目标方法调用前
        Object proceed = null;
        MethodSignature signature = (MethodSignature) joinPoint.getSignature();
        String methodName = signature.getMethod().getName();
        System.out.println(methodName+"即将被调用");
        try {
            proceed = joinPoint.proceed();
            //目标方法调用后
            System.out.println(methodName+"被调用完了");
        } catch (Throwable throwable) {
            throwable.printStackTrace();
            //目标方法出现异常了
            System.out.println(methodName+"出现了异常");
        }
        return proceed;
    }
}

~~~~

④在需要正确的地方增加对应的注解

~~~~Java
@Service
public class UserServiceImpl implements UserServcie {

    @Autowired
    private UserMapper userMapper;

    @Override
    @InvokeLog  //需要被增强方法需要加上对应的注解
    public List<User> findAll() {
        return userMapper.findAll();
    }
}
~~~~





#### 4.11.2 切换动态代理

​	有的时候我们需要修改AOP的代理方式。

​	我们可以使用以下方式修改：

​	在配置文件中配置spring.aop.proxy-target-class为false这为使用jdk动态代理。该配置默认值为true，代表**默认使用cglib动态代理。**

~~~~java
@SpringBootApplication
@EnableAspectJAutoProxy(proxyTargetClass = false)//修改代理方式
public class WebApplication {
    public static void main(String[] args) {
        ConfigurableApplicationContext context = SpringApplication.run(WebApplication.class, args);
    }
}
~~~~

​	如果想生效还需要在配置文件中做如下配置

~~~~yml
spring:
  aop:
    proxy-target-class: false #切换动态代理的方式
~~~~



### 4.12 模板引擎相关-Thymeleaf（Jsp）

#### 4.12.1 快速入门

##### 4.12.1.1 导入依赖

~~~~xml
        <!--thymeleaf依赖-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-thymeleaf</artifactId>
        </dependency>
~~~~

##### 4.12.1.2 定义Controller

在controller中往域中存数据，并且跳转

~~~~java
@Controller
public class ThymeleafController {

    @Autowired
    private UserServcie userServcie;

    @RequestMapping("/thymeleaf/users")
    public String users(Model model){
        //获取数据
        List<User> users = userServcie.findAll();
        //望域中存入数据
        model.addAttribute("users",users);
        model.addAttribute("msg","hello thymeleaf");
        //页面跳转 不需要添加前缀与后缀，会自动拼接前缀/resources/与后缀.html,然后跳转到resources\templates\下面寻找table-standard.html文件
        return "table-standard";
    }
}
~~~~

##### 4.12.1.3 书写htmL

springBoot中的thymeleaf默认在**resources\templates**下存放模板页面，静态资源(css,js)存放在resources\static文件夹下。

在html标签中加上 xmlns:th="http://www.thymeleaf.org",引入命名空间。

获取域中的name属性的值可以使用： ${name}    注意要在th开头的属性中使用

~~~~html
<html lang="en" class="no-ie" xmlns:th="http://www.thymeleaf.org">
 .....
 <div class="panel-heading" th:text="${msg}">Kitchen Sink</div>
~~~~

如果需要**引入静态资源**，需要使用如下写法。**语法： th: ... ="@{/ ... }"**

~~~~html
   <link rel="stylesheet" th:href="@{/app/css/bootstrap.css}">
   <link rel="stylesheet" th:href="@{/vendor/fontawesome/css/font-awesome.min.css}">
   <link rel="stylesheet" th:href="@{/vendor/animo/animate+animo.css}">
   <link rel="stylesheet" th:href="@{/vendor/csspinner/csspinner.min.css}">
   <link rel="stylesheet" th:href="@{/app/css/app.css}">

   <script th:src="@{/vendor/modernizr/modernizr.js}" type="application/javascript"></script>
   <script th:src="@{/vendor/fastclick/fastclick.js}" type="application/javascript"></script>
~~~~

遍历语法：遍历的语法  th:each="自定义的元素变量名称 : ${集合变量名称}" 

~~~~html
<tr th:each="user:${users}"><!-- 遍历users集合，把遍历到的每个元素(user对象)命名为user并且存放到域中-->
    <!-- 从域中获取user对象数据进行渲染-->
    <td th:text="${user.id}"></td>
    <td th:text="${user.username}"></td>
    <td th:text="${user.age}"></td>
    <td th:text="${user.address}"></td>
</tr>
~~~~



## 5.整合Redis

### 启动Redis

在Redis目录下面打开cmd

运行下面的命令，读取redis.windows.conf配置文件运行Redis服务端

~~~sh
redis-server.exe redis.windows.conf
~~~

### ①依赖

~~~~xml
        <!--redis-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-data-redis</artifactId>
        </dependency>
~~~~

### ②配置Redis地址和端口号

~~~~yml
spring:
  redis:
    host: 127.0.0.1 #redis服务器ip地址
    port: 6379  #redis端口号
~~~~

### ③注入RedisTemplate使用

~~~~java
    @Autowired
    private StringRedisTemplate redisTemplate;

    @Test
    public void testRedis(){
        redisTemplate.opsForValue().set("name","三更");
    }
~~~~

## 6.环境切换

### 6.1 为什么要使用profile

​	 在实际开发环境中，我们存在开发环境的配置，部署环境的配置，测试环境的配置等等，里面的配置信息很多时，例如：端口、上下文路径、数据库配置等等，若每次切换环境时，我们都需要进行修改这些配置信息时，会比较麻烦，profile的出现就是为了解决这个问题。它可以让我们针对不同的环境进行不同的配置，然后可以通过激活、指定参数等方式快速切换环境。

### 6.2 使用

#### 6.2.1 创建profile配置文件

​	 我们可以用**application-xxx.yml**的命名方式 创建配置文件，其中xxx可以根据自己的需求来定义。

​	例如

​			我们需要一个测试环境的配置文件，则可以命名为：**application-test.yml**

​			需要一个生产环境的配置文件，可以命名为：**application-prod.yml**

​	我们可以不同环境下**不同的配置**放到对应的profile文件中进行配置。然后把不同环境下都**相同的配置**放到application.yml文件中配置。



#### 6.2.2 激活环境

​	1.我们可以再**application.yml**文件中使用**spring.profiles.active**属性来配置激活哪个环境。 

~~~yml
spring:
  profiles:
    active: test # 使用application-test.yml+公共的application.yml作为配置文件
~~~



​	2.也可以使用**虚拟机参数**来指定激活环境。例如 ： **-Dspring.profiles.active=test**

​	![https://image.itbaima.net/images/173/image-20231118221852948.png](https://image.itbaima.net/images/173/image-20231118221852948.png)

​	3.也可以使用命令行参数来激活环境。例如： **--spring.profiles.active=test**

把项目打包为jar包后，在jar包目录下面的cmd中执行即可：

~~~sh
java -jar xxx.jar  --spring.profiles.active=test
~~~



​	

## 7.日志

​	开启日志：开启后，别人请求了什么接口，传输的参数是什么都会进行打印日志，mybatis日志也会打印

~~~~yml
debug: true #开启日志(除了自己写的代码之外的日志都会打印)
logging:
  level:
    com.sangeng: debug #设置com.sangeng包的日志级别为debug,debug级别的日志都会打印
    
~~~~





## 8.指标监控

​	我们在日常开发中需要对程序内部的运行情况进行监控， 比如：健康度、运行指标、日志信息、线程状况等等 。而SpringBoot的监控Actuator就可以帮我们解决这些问题。



### 8.1 使用

①添加依赖

~~~~xml
<dependency>
 	<groupId>org.springframework.boot</groupId>
 	<artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
~~~~

②访问监控接口

根据springBoot打印的日志信息来判断使用哪一个端口，访问哪一个路径

http://localhost:81/actuator

③配置启用监控端点

**注意：**需要添加双引号，因为在yml中*有特殊的含义

~~~~yml
management:
  endpoints:
    enabled-by-default: true # 配置启用所有端点
	web:
      exposure:
        include: "*" # web端暴露所有端点 
~~~~





### 8.2 常用端点

直接在yml文件的最左边书写，根据idea的提示进行选择即可。

| 端点名称         | 描述                                      |
| :--------------- | :---------------------------------------- |
| `beans`          | 显示应用程序中所有Spring Bean的完整列表。 |
| `health`         | 显示应用程序运行状况信息。                |
| `info`           | 显示应用程序信息。                        |
| `loggers`        | 显示和修改应用程序中日志的配置。          |
| `metrics`        | 显示当前应用程序的“指标”信息。            |
| `mappings`       | 显示所有`@RequestMapping`路径列表。       |
| `scheduledtasks` | 显示应用程序中的计划任务。                |



### 8.3 图形化界面 SpringBoot Admin

解析返回的JSON信息，使用图形化界面进行展示。

①创建SpringBoot Admin Server应用

创建一个新的springBoot模块，引入spring-boot-admin-starter-server依赖

~~~~xml
        <dependency>
            <groupId>de.codecentric</groupId>
            <artifactId>spring-boot-admin-starter-server</artifactId>
        </dependency>
~~~~

然后在启动类上加上@EnableAdminServer注解

~~~java
@EnableAdminServer
@SpringBootApplication
public class HelloApplication {
    public static void main(String[] args) {
        SpringApplication.run(HelloApplication.class, args);
    }
}
~~~

并且配置服务启动的端口：

~~~yml
server.port=8888
~~~

启动即可。

②配置SpringBoot Admin client应用

在需要监控的应用中加上spring-boot-admin-starter-client依赖

~~~~xml
        <dependency>
            <groupId>de.codecentric</groupId>
            <artifactId>spring-boot-admin-starter-client</artifactId>
            <version>2.3.1</version>
        </dependency>
~~~~

然后配置SpringBoot Admin Server的地址

~~~~yml
spring:
  boot:
    admin:
      client:
        url: http://localhost:8888 #配置 Admin Server的地址
~~~~







## 9:特殊功能

### 9.1开跑自启：启动项目时自动执行一些操作：

~~~java
/**
 * 自定义一个类implements CommandLineRunner接口来指示springBoot程序启动时要做的一些事情
 */
@Component
public class ViewCountRunner implements CommandLineRunner {
    @Autowired
    private ArticleMapper articleMapper;

    @Autowired
    private RedisCache redisCache;

    /**
     * 程序启动时将数据库中每篇文章的访问量的数据存储到map中最后存储到Redis中
     * @param args
     * @throws Exception
     */
    @Override
    public void run(String... args) throws Exception {
        //获取所有的文章列表
        List<Article> articles = articleMapper.selectList(null);
        //存储在map集合中
        Map<String,Integer> map = articles.stream()
                .collect(Collectors.toMap(article -> article.getId().toString()
                        //value值转换为Integer类型而不是Long，后面才能进行递增
                        , article -> article.getViewCount().intValue()));
        //存储到Redis中
        redisCache.setCacheMap(SystemConstants.VIEWCOUNT_MAP_KEY,map);

    }

}

~~~

### 9.2 定时任务：使用cron表达式实现定时任务

 在线Cron表达式生成器：

[https://www.bejson.com/ot](https://www.bejson.com/ot)



~~~java
@Component
public class UpdateViewCountJob {
    @Autowired
    //继承了IService,提供了许多批量操作的方法
    private ArticleService articleService;

    @Autowired
    private RedisCache redisCache;

    /**
     * 
     * cron表达式的使用  详细版本可参考sanGeng blog.md
     * 1.允许的特殊字符：, - * /    四个字符
     * 2.日期和星期两个部分如果其中一个部分设置了值，则另一个必须设置为 “ ? ”。 即：
     * * 例如：
     *      *
     *      *        日期  星期
     *      * 0\* * * 2 * ?
     *      *  和
     *      * 0\* * * ? * 2
     *      *
     * 3.cron表达式由七部分组成（一般只写6部分），中间由空格分隔，其中最后一个部分(年)一般该项不设置，直接忽略掉，即可为空值

     每隔5分钟的第1秒把Redis中的访问量数据写入数据库中
     */
    @Scheduled(cron = "1 0/5 * * * ?")
    public void updateViewCount() {
        Map<String, Integer> viewCountMap = redisCache.getCacheMap(SystemConstants.VIEWCOUNT_MAP_KEY);
        List<Article> articles = viewCountMap.entrySet()
                .stream()
                .map(entry -> {
                            return new Article(Long.valueOf(entry.getKey()), entry.getValue().longValue());
                        }
                )
                .collect(Collectors.toList());
        //对于updateBatchById方法，如果批量更新的实体对象属性为空，
        // 则不会对数据库中对应的字段进行更新，即数据库中原有的值会保持不变。
        // 使用updateBatchById(articles);或updateById()都会报错：java.lang.NullPointerException
        //articleService.updateBatchById(articles);
        articles.forEach(article -> articleService.updateViewCountToMysql(article.getId(),article.getViewCount()));

    }
}

~~~



