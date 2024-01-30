

# springSecurity实战登录验证jWT方案

## 思路

#### 入门案例的认证流程详解

默认的认证流程，使用了UsernamePasswordAuthenticationFilter来进行：

![image-20211214151515385](D:\alwaysUse\notes\myNotes\java apply note\springSecurity\img\image-20211214151515385.png)

概念速查:

Authentication接口: 它的实现类，表示当前访问系统的用户，封装了用户相关信息。

AuthenticationManager接口：定义了认证Authentication的方法 

UserDetailsService接口：加载用户特定数据的核心接口。里面定义了一个根据用户名查询用户信息的方法。

UserDetails接口：提供核心用户信息。通过UserDetailsService根据用户名获取处理的用户信息要封装成UserDetails对象返回。然后将这些信息封装到Authentication对象中。

#### jWT方案实战的核心：

1. 自定义接口来模拟UsernamePasswordAuthenticationFilter的登录流程，并且使用UsernamePasswordAuthenticationFilter中的方法。
2. 添加自定义过滤器来实现token的验证。



## 准备工作

### ①添加依赖

~~~~xml
        <!--redis依赖-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-data-redis</artifactId>
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
~~~~

### ② 添加Redis相关配置

~~~~java
import com.alibaba.fastjson.JSON;
import com.alibaba.fastjson.serializer.SerializerFeature;
import com.fasterxml.jackson.databind.JavaType;
import com.fasterxml.jackson.databind.ObjectMapper;
import com.fasterxml.jackson.databind.type.TypeFactory;
import org.springframework.data.redis.serializer.RedisSerializer;
import org.springframework.data.redis.serializer.SerializationException;
import com.alibaba.fastjson.parser.ParserConfig;
import org.springframework.util.Assert;
import java.nio.charset.Charset;

/**
 * Redis使用FastJson序列化
 * 
 * @author sg
 */
public class FastJsonRedisSerializer<T> implements RedisSerializer<T>
{

    public static final Charset DEFAULT_CHARSET = Charset.forName("UTF-8");

    private Class<T> clazz;

    static
    {
        ParserConfig.getGlobalInstance().setAutoTypeSupport(true);
    }

    public FastJsonRedisSerializer(Class<T> clazz)
    {
        super();
        this.clazz = clazz;
    }

    @Override
    public byte[] serialize(T t) throws SerializationException
    {
        if (t == null)
        {
            return new byte[0];
        }
        return JSON.toJSONString(t, SerializerFeature.WriteClassName).getBytes(DEFAULT_CHARSET);
    }

    @Override
    public T deserialize(byte[] bytes) throws SerializationException
    {
        if (bytes == null || bytes.length <= 0)
        {
            return null;
        }
        String str = new String(bytes, DEFAULT_CHARSET);

        return JSON.parseObject(str, clazz);
    }


    protected JavaType getJavaType(Class<?> clazz)
    {
        return TypeFactory.defaultInstance().constructType(clazz);
    }
}
~~~~

~~~~java
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.data.redis.connection.RedisConnectionFactory;
import org.springframework.data.redis.core.RedisTemplate;
import org.springframework.data.redis.serializer.StringRedisSerializer;

@Configuration
public class RedisConfig {

    @Bean
    @SuppressWarnings(value = { "unchecked", "rawtypes" })
    public RedisTemplate<Object, Object> redisTemplate(RedisConnectionFactory connectionFactory)
    {
        RedisTemplate<Object, Object> template = new RedisTemplate<>();
        template.setConnectionFactory(connectionFactory);

        FastJsonRedisSerializer serializer = new FastJsonRedisSerializer(Object.class);

        // 使用StringRedisSerializer来序列化和反序列化redis的key值
        template.setKeySerializer(new StringRedisSerializer());
        template.setValueSerializer(serializer);

        // Hash的key也采用StringRedisSerializer的序列化方式
        template.setHashKeySerializer(new StringRedisSerializer());
        template.setHashValueSerializer(serializer);

        template.afterPropertiesSet();
        return template;
    }
}
~~~~

### ③ 响应类

~~~~java
import com.fasterxml.jackson.annotation.JsonInclude;

/**
 * @Author 三更  B站： https://space.bilibili.com/663528522
 */
@JsonInclude(JsonInclude.Include.NON_NULL)
public class ResponseResult<T> {
    /**
     * 状态码
     */
    private Integer code;
    /**
     * 提示信息，如果有错误时，前端可以获取该字段进行提示
     */
    private String msg;
    /**
     * 查询到的结果数据，
     */
    private T data;

    public ResponseResult(Integer code, String msg) {
        this.code = code;
        this.msg = msg;
    }

    public ResponseResult(Integer code, T data) {
        this.code = code;
        this.data = data;
    }

    public Integer getCode() {
        return code;
    }

    public void setCode(Integer code) {
        this.code = code;
    }

    public String getMsg() {
        return msg;
    }

    public void setMsg(String msg) {
        this.msg = msg;
    }

    public T getData() {
        return data;
    }

    public void setData(T data) {
        this.data = data;
    }

    public ResponseResult(Integer code, String msg, T data) {
        this.code = code;
        this.msg = msg;
        this.data = data;
    }
}
~~~~

### ④工具类

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

    public static String getUUID(){
        String token = UUID.randomUUID().toString().replaceAll("-", "");
        return token;
    }
    
    /**
     * 生成jtw
     * @param subject token中要存放的数据（json格式）
     * @return
     */
    public static String createJWT(String subject) {
        JwtBuilder builder = getJwtBuilder(subject, null, getUUID());// 设置过期时间
        return builder.compact();
    }

    /**
     * 生成jtw
     * @param subject token中要存放的数据（json格式）
     * @param ttlMillis token超时时间
     * @return
     */
    public static String createJWT(String subject, Long ttlMillis) {
        JwtBuilder builder = getJwtBuilder(subject, ttlMillis, getUUID());// 设置过期时间
        return builder.compact();
    }

    private static JwtBuilder getJwtBuilder(String subject, Long ttlMillis, String uuid) {
        SignatureAlgorithm signatureAlgorithm = SignatureAlgorithm.HS256;
        SecretKey secretKey = generalKey();
        long nowMillis = System.currentTimeMillis();
        Date now = new Date(nowMillis);
        if(ttlMillis==null){
            ttlMillis=JwtUtil.JWT_TTL;
        }
        long expMillis = nowMillis + ttlMillis;
        Date expDate = new Date(expMillis);
        return Jwts.builder()
                .setId(uuid)              //唯一的ID
                .setSubject(subject)   // 主题  可以是JSON数据
                .setIssuer("sg")     // 签发者
                .setIssuedAt(now)      // 签发时间
                .signWith(signatureAlgorithm, secretKey) //使用HS256对称加密算法签名, 第二个参数为秘钥
                .setExpiration(expDate);
    }

    /**
     * 创建token
     * @param id
     * @param subject
     * @param ttlMillis
     * @return
     */
    public static String createJWT(String id, String subject, Long ttlMillis) {
        JwtBuilder builder = getJwtBuilder(subject, ttlMillis, id);// 设置过期时间
        return builder.compact();
    }

    public static void main(String[] args) throws Exception {
        String token = "eyJhbGciOiJIUzI1NiJ9.eyJqdGkiOiJjYWM2ZDVhZi1mNjVlLTQ0MDAtYjcxMi0zYWEwOGIyOTIwYjQiLCJzdWIiOiJzZyIsImlzcyI6InNnIiwiaWF0IjoxNjM4MTA2NzEyLCJleHAiOjE2MzgxMTAzMTJ9.JVsSbkP94wuczb4QryQbAke3ysBDIL5ou8fWsbt_ebg";
        Claims claims = parseJWT(token);
        System.out.println(claims);
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

~~~~java
import java.util.*;
import java.util.concurrent.TimeUnit;

@SuppressWarnings(value = { "unchecked", "rawtypes" })
@Component
public class RedisCache
{
    @Autowired
    public RedisTemplate redisTemplate;

    /**
     * 缓存基本的对象，Integer、String、实体类等
     *
     * @param key 缓存的键值
     * @param value 缓存的值
     */
    public <T> void setCacheObject(final String key, final T value)
    {
        redisTemplate.opsForValue().set(key, value);
    }

    /**
     * 缓存基本的对象，Integer、String、实体类等
     *
     * @param key 缓存的键值
     * @param value 缓存的值
     * @param timeout 时间
     * @param timeUnit 时间颗粒度
     */
    public <T> void setCacheObject(final String key, final T value, final Integer timeout, final TimeUnit timeUnit)
    {
        redisTemplate.opsForValue().set(key, value, timeout, timeUnit);
    }

    /**
     * 设置有效时间
     *
     * @param key Redis键
     * @param timeout 超时时间
     * @return true=设置成功；false=设置失败
     */
    public boolean expire(final String key, final long timeout)
    {
        return expire(key, timeout, TimeUnit.SECONDS);
    }

    /**
     * 设置有效时间
     *
     * @param key Redis键
     * @param timeout 超时时间
     * @param unit 时间单位
     * @return true=设置成功；false=设置失败
     */
    public boolean expire(final String key, final long timeout, final TimeUnit unit)
    {
        return redisTemplate.expire(key, timeout, unit);
    }

    /**
     * 获得缓存的基本对象。
     *
     * @param key 缓存键值
     * @return 缓存键值对应的数据
     */
    public <T> T getCacheObject(final String key)
    {
        ValueOperations<String, T> operation = redisTemplate.opsForValue();
        return operation.get(key);
    }

    /**
     * 删除单个对象
     *
     * @param key
     */
    public boolean deleteObject(final String key)
    {
        return redisTemplate.delete(key);
    }

    /**
     * 删除集合对象
     *
     * @param collection 多个对象
     * @return
     */
    public long deleteObject(final Collection collection)
    {
        return redisTemplate.delete(collection);
    }

    /**
     * 缓存List数据
     *
     * @param key 缓存的键值
     * @param dataList 待缓存的List数据
     * @return 缓存的对象
     */
    public <T> long setCacheList(final String key, final List<T> dataList)
    {
        Long count = redisTemplate.opsForList().rightPushAll(key, dataList);
        return count == null ? 0 : count;
    }

    /**
     * 获得缓存的list对象
     *
     * @param key 缓存的键值
     * @return 缓存键值对应的数据
     */
    public <T> List<T> getCacheList(final String key)
    {
        return redisTemplate.opsForList().range(key, 0, -1);
    }

    /**
     * 缓存Set
     *
     * @param key 缓存键值
     * @param dataSet 缓存的数据
     * @return 缓存数据的对象
     */
    public <T> BoundSetOperations<String, T> setCacheSet(final String key, final Set<T> dataSet)
    {
        BoundSetOperations<String, T> setOperation = redisTemplate.boundSetOps(key);
        Iterator<T> it = dataSet.iterator();
        while (it.hasNext())
        {
            setOperation.add(it.next());
        }
        return setOperation;
    }

    /**
     * 获得缓存的set
     *
     * @param key
     * @return
     */
    public <T> Set<T> getCacheSet(final String key)
    {
        return redisTemplate.opsForSet().members(key);
    }

    /**
     * 缓存Map
     *
     * @param key
     * @param dataMap
     */
    public <T> void setCacheMap(final String key, final Map<String, T> dataMap)
    {
        if (dataMap != null) {
            redisTemplate.opsForHash().putAll(key, dataMap);
        }
    }

    /**
     * 获得缓存的Map
     *
     * @param key
     * @return
     */
    public <T> Map<String, T> getCacheMap(final String key)
    {
        return redisTemplate.opsForHash().entries(key);
    }

    /**
     * 往Hash中存入数据
     *
     * @param key Redis键
     * @param hKey Hash键
     * @param value 值
     */
    public <T> void setCacheMapValue(final String key, final String hKey, final T value)
    {
        redisTemplate.opsForHash().put(key, hKey, value);
    }

    /**
     * 获取Hash中的数据
     *
     * @param key Redis键
     * @param hKey Hash键
     * @return Hash中的对象
     */
    public <T> T getCacheMapValue(final String key, final String hKey)
    {
        HashOperations<String, String, T> opsForHash = redisTemplate.opsForHash();
        return opsForHash.get(key, hKey);
    }

    /**
     * 删除Hash中的数据
     * 
     * @param key
     * @param hkey
     */
    public void delCacheMapValue(final String key, final String hkey)
    {
        HashOperations hashOperations = redisTemplate.opsForHash();
        hashOperations.delete(key, hkey);
    }

    /**
     * 获取多个Hash中的数据
     *
     * @param key Redis键
     * @param hKeys Hash键集合
     * @return Hash对象集合
     */
    public <T> List<T> getMultiCacheMapValue(final String key, final Collection<Object> hKeys)
    {
        return redisTemplate.opsForHash().multiGet(key, hKeys);
    }

    /**
     * 获得缓存的基本对象列表
     *
     * @param pattern 字符串前缀
     * @return 对象列表
     */
    public Collection<String> keys(final String pattern)
    {
        return redisTemplate.keys(pattern);
    }
}
~~~~

~~~~java
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;

public class WebUtils
{
    /**
     * 将字符串渲染到客户端
     * 
     * @param response 渲染对象
     * @param string 待渲染的字符串
     * @return null
     */
    public static String renderString(HttpServletResponse response, String string) {
        try
        {
            response.setStatus(200);
            response.setContentType("application/json");
            response.setCharacterEncoding("utf-8");
            response.getWriter().print(string);
        }
        catch (IOException e)
        {
            e.printStackTrace();
        }
        return null;
    }
}
~~~~

### ⑤实体类

~~~~java
import java.io.Serializable;
import java.util.Date;


/**
 * 用户表(User)实体类
 *
 * @author 三更
 */
@Data
@AllArgsConstructor
@NoArgsConstructor
public class User implements Serializable {
    private static final long serialVersionUID = -40356785423868312L;
    
    /**
    * 主键
    */
    private Long id;
    /**
    * 用户名
    */
    private String userName;
    /**
    * 昵称
    */
    private String nickName;
    /**
    * 密码
    */
    private String password;
    /**
    * 账号状态（0正常 1停用）
    */
    private String status;
    /**
    * 邮箱
    */
    private String email;
    /**
    * 手机号
    */
    private String phonenumber;
    /**
    * 用户性别（0男，1女，2未知）
    */
    private String sex;
    /**
    * 头像
    */
    private String avatar;
    /**
    * 用户类型（0管理员，1普通用户）
    */
    private String userType;
    /**
    * 创建人的用户id
    */
    private Long createBy;
    /**
    * 创建时间
    */
    private Date createTime;
    /**
    * 更新人
    */
    private Long updateBy;
    /**
    * 更新时间
    */
    private Date updateTime;
    /**
    * 删除标志（0代表未删除，1代表已删除）
    */
    private Integer delFlag;
}
~~~~

​		引入MybatisPuls和mysql驱动的依赖

~~~~xml
        <dependency>
            <groupId>com.baomidou</groupId>
            <artifactId>mybatis-plus-boot-starter</artifactId>
            <version>3.4.3</version>
        </dependency>
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
        </dependency>
~~~~

​		配置数据库信息

~~~~yml
spring:
  datasource:
    url: jdbc:mysql://localhost:3306/sg_security?characterEncoding=utf-8&serverTimezone=UTC
    username: root
    password: root
    driver-class-name: com.mysql.cj.jdbc.Driver
~~~~

​		定义Mapper接口

~~~~java
public interface UserMapper extends BaseMapper<User> {
}
~~~~

​		修改User实体类

~~~~java
类名上加@TableName(value = "sys_user") ,id字段上加 @TableId
~~~~

### 测试

配置Mapper扫描

~~~~java
@SpringBootApplication
@MapperScan("com.sangeng.mapper")
public class SimpleSecurityApplication {
    public static void main(String[] args) {
        ConfigurableApplicationContext run = SpringApplication.run(SimpleSecurityApplication.class);
        System.out.println(run);
    }
}
~~~~

​		添加junit依赖

~~~~java
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
        </dependency>
~~~~

​	   测试MP是否能正常使用

~~~~java
/**
 * @Author 三更  B站： https://space.bilibili.com/663528522
 */
@SpringBootTest
public class MapperTest {

    @Autowired
    private UserMapper userMapper;

    @Test
    public void testUserMapper(){
        List<User> users = userMapper.selectList(null);
        System.out.println(users);
    }
}
~~~~



# (1) 1级 用户认证的实现（请求token）

## ① 2级 用户请求token

输入依赖、工具类 详情见 sanGeng SpringSecurity.md

### 流程

登录

​	①放行登录接口，不直接使用UsernamePasswordAuthenticationFilter来进行认证，而是模仿UsernamePasswordAuthenticationFilter的方式，自定义登录接口来进行认证

​				调用ProviderManager的方法进行认证 如果认证通过生成jwt并且返回

​				把用户信息存入redis中

​	②自定义UserDetailsService 

​				在这个实现类中去查询数据库

​	注意配置passwordEncoder为BCryptPasswordEncoder

获取token的流程:

![](img\获取token的流程.png)

### 核心代码：


#### 设置从数据库中查询用户信息进行登录验证

~~~java
@Service
public class UserDetailServiceImpl implements UserDetailsService {
    @Autowired
    private UserMapper userMapper;
    @Autowired
    private MenuService menuService;

    @Override
    public UserDetails loadUserByUsername(String s) throws UsernameNotFoundException {
        //  根据用户名从数据库中查询用户的信息
        LambdaQueryWrapper<User> queryWrapper = new LambdaQueryWrapper<>();
        queryWrapper.eq(User::getUserName,s);
        User user = userMapper.selectOne(queryWrapper);
        // 查询不到用户则抛出异常
        if(user==null){
            throw new RuntimeException("userMapper.selectOne()结果：用户不存在！");
        }
        // 如果是后台用户，才封装用户权限信息
        if(user.getType().equals(SystemConstants.ADMIN)){
            List<String> perms = menuService.selectPermsByUserId(user.getId());
            return new LoginUser(user,perms);
        }
	    // 自定义一个UserDetails的实现类来存储查询出来的用户信息并返回
        return new LoginUser(user,null);
    }
}

~~~

其中LoginUser实现了UserDetails接口，具体为：

~~~java
/***
 * SpringSecurity框架会使用这个接口的方法获取用户名与密码、权限等等
 */
@Data
@AllArgsConstructor
@NoArgsConstructor
public class LoginUser implements UserDetails {
    private User user;
    private List<String> permissions;

    /***
     * SpringSecurity框架会使用这个接口的方法获取权限信息
     * 重写以给框架提供数据库中查询到的权限信息,也可以不写使用自定义的权限验证信息
     * @return
     */
    @Override
    public Collection<? extends GrantedAuthority> getAuthorities() {
        return null;
    }

    /***
     * SpringSecurity框架会使用这个接口的方法获取用户名与密码
     * 重写以给框架提供数据库中查询到的用户信息
     * @return
     */
    @Override
    public String getPassword() {
        return user.getPassword();
    }

    @Override
    public String getUsername() {
        return user.getUserName();
    }

    /***
     * 全部改为true方便测试
     * @return
     */
    @Override
    public boolean isAccountNonExpired() {
        return true;
    }

    @Override
    public boolean isAccountNonLocked() {
        return true;
    }

    @Override
    public boolean isCredentialsNonExpired() {
        return true;
    }

    @Override
    public boolean isEnabled() {
        return true;
    }
}

~~~



#### 设置密码加密存储

实际项目中我们不会把密码明文存储在数据库中。
	
默认使用的PasswordEncoder要求数据库中的密码格式为：{id}password 。它会根据id去判断密码的加密方式。但是我们一般不会采用这种方式。所以就需要替换PasswordEncoder。
	
我们一般使用SpringSecurity为我们提供的BCryptPasswordEncoder。
	
我们只需要使用把BCryptPasswordEncoder对象注入Spring容器中，SpringSecurity就会使用该PasswordEncoder来进行密码校验。
	
我们可以定义一个SpringSecurity的配置类，SpringSecurity要求这个配置类要继承WebSecurityConfigurerAdapter。

~~~~java
/**
 * @Author 三更  B站： https://space.bilibili.com/663528522
 */
@Configuration
public class SecurityConfig extends WebSecurityConfigurerAdapter {


    @Bean
    public PasswordEncoder passwordEncoder(){
        return new BCryptPasswordEncoder();
    }

}
~~~~

####  4级 编写登陆接口

接下我们需要自定义登陆接口，然后让SpringSecurity对这个接口放行,让用户访问这个接口的时候不用登录也能访问。

Controller层

~~~~java
@RestController
public class LoginController {

    @Autowired
    private LoginServcie loginServcie;

    @PostMapping("/user/login")
    public ResponseResult login(@RequestBody User user){
        return loginServcie.login(user);
    }
}

~~~~

#### 编写配置类：

在接口中我们通过AuthenticationManager的authenticate方法来进行用户认证,所以需要在SecurityConfig中配置把AuthenticationManager注入容器。

~~~~java
/**
 * @Author 三更  B站： https://space.bilibili.com/663528522
 */
@Configuration
public class SecurityConfig extends WebSecurityConfigurerAdapter {


    @Bean
    public PasswordEncoder passwordEncoder(){
        return new BCryptPasswordEncoder();
    }

    @Override
    protected void configure(HttpSecurity http) throws Exception {
        http
                //关闭csrf
                .csrf().disable()
                //不通过Session获取SecurityContext
                .sessionManagement().sessionCreationPolicy(SessionCreationPolicy.STATELESS)
                .and()
                .authorizeRequests()
                // 对于登录接口 允许匿名访问
                .antMatchers("/user/login").anonymous()
                // 除上面外的所有请求全部需要鉴权认证
                .anyRequest().authenticated();
    }

    @Bean
    @Override
    public AuthenticationManager authenticationManagerBean() throws Exception {
        return super.authenticationManagerBean();
    }
}
~~~~


#### service层 认证成功则返回token

认证成功的话要生成一个jwt，放入响应中返回。并且为了让用户下回请求时能通过jwt识别出具体的是哪个用户，我们需要把用户信息存入redis，可以把用户id作为key。

~~~java
	@Override
    public ResponseResult login(User user) {
        // 使用这个Authentication的实现类来封装用户信息，然后才能调用authenticate方法进行验证
        UsernamePasswordAuthenticationToken authenticationToken =
                new UsernamePasswordAuthenticationToken(user.getUserName(),user.getPassword());
        // 走流程图去查询数据库中用户信息并且与传入的信息进行对比，验证成功则写入Authentication对象返回，否则返回null
        Authentication authenticate = authenticationManager.authenticate(authenticationToken);
        if(Objects.isNull(authenticate)){
            //验证失败
            throw new RuntimeException("authenticationManager.authenticate验证出错：用户名或密码错误！");
        }
        //验证成功根据用户id生成token
        LoginUser loginUser = (LoginUser) authenticate.getPrincipal();
        String s_id = loginUser.getUser().getId().toString();
        String jwt = JwtUtil.createJWT(s_id);
        //根据用户id存储到Redis中
        redisCache.setCacheObject("bloglogin:"+s_id,loginUser);
        //封装vo user拷贝出userInfoVo对象并且封装为blogUserLoginVo对象
        UserInfoVo userInfoVo = BeanCopyUtil.beanCopy(loginUser.getUser(), UserInfoVo.class);
        BlogUserLoginVo blogUserLoginVo = new BlogUserLoginVo(jwt, userInfoVo);
        //封装为ResponseResult类返回
        return ResponseResult.okResult(blogUserLoginVo);
    }

~~~





## ② 用户携带token访问

校验：

​	①定义Jwt认证过滤器

​				获取token

​				解析token获取其中的userid

​				从redis中获取用户信息

​				存入SecurityContextHolder

用户使用token登录的流程:

![](img\用户使用token登录的流程.png)

### 1 自定义认证过滤器

​	我们需要自定义一个过滤器，这个过滤器会去获取请求头中的token，对token进行解析取出其中的userid。

​	使用userid去redis中获取对应的LoginUser对象。

​	然后封装Authentication对象存入SecurityContextHolder

```java
@Component
public class JwtAuthenticationTokenFilter extends OncePerRequestFilter {
    @Autowired
    private RedisCache redisCache;

    /***
     * 前端携带token发起请求时后端的验证流程
     * @param httpServletRequest
     * @param httpServletResponse
     * @param filterChain
     * @throws ServletException
     * @throws IOException
     */
    @Override
    protected void doFilterInternal(HttpServletRequest httpServletRequest,
                                    HttpServletResponse httpServletResponse,
                                    FilterChain filterChain) throws ServletException, IOException {
        //获取前端请求头中携带的token并解析出userId
        String token = httpServletRequest.getHeader("token");
        //检测是否含有token
        if(!StringUtils.hasText(token)){
            // 没有携带token访问了需要登录的接口的话，SecurityContextHolder中没有用户信息，会被后面的过滤器拒绝，所以直接放行即可
            filterChain.doFilter(httpServletRequest,httpServletResponse);
            // return是为了防止服务端响应时再次结果过滤器，会再次执行这个方法，防止继续执行下去
            return;
        }
        String userId = "";
        try {
            Claims claims = JwtUtil.parseJWT(token);
            userId = claims.getSubject();
        } catch (Exception e) {
            e.printStackTrace();
            //出现异常进入这里说明 token失效 或 token被篡改
            //返回响应给前端告诉他需要重新登录并return
            ResponseResult result = ResponseResult.errorResult(AppHttpCodeEnum.NEED_LOGIN);
            WebUtils.renderString(httpServletResponse, JSON.toJSONString(result));
            return;
        }
        //从Redis中查询用户信息  后台使用"login:" + userId与前台进行区分
        LoginUser loginUser = redisCache.getCacheObject(SystemConstants.BACKGROUND_REDIS_TOKEN_KEY_PREFIX + userId);
        if(Objects.isNull(loginUser)){
            //Redis查询不到信息，过期了 或者 没有登录
            //返回响应给前端告诉他需要重新登录并return
            ResponseResult result = ResponseResult.errorResult(AppHttpCodeEnum.NEED_LOGIN);
            WebUtils.renderString(httpServletResponse, JSON.toJSONString(result));
            return;
        }
        // 把用户信息存储到SecurityContextHolder的authentication中，因为后面的过滤器会根据SecurityContextHolder中是否含有用户信息来判断用户是否登录，如果没有用户信息就不会放行
        // 这里需要使用3个参数的重载形式才有super.setAuthenticated(true); 表示设置已经完成了认证，这样后面的过滤器才能够知道已经完成了认证，不需要做其他处理了
        // 参数为：用户信息、null、权限信息(使用loginUser中的权限信息封装为参数的实现类再传入即可，这里设置为null了)
        UsernamePasswordAuthenticationToken authenticationToken = new
                UsernamePasswordAuthenticationToken(loginUser,null,null);
        SecurityContextHolder.getContext().setAuthentication(authenticationToken);
        // 放行
        filterChain.doFilter(httpServletRequest,httpServletResponse);

    }
}

```



### 2 添加认证过滤器到springSecurity的过滤器链中

~~~java
/**
 * @Author 三更  B站： https://space.bilibili.com/663528522
 */
@Configuration
public class SecurityConfig extends WebSecurityConfigurerAdapter {


    @Bean
    public PasswordEncoder passwordEncoder(){
        return new BCryptPasswordEncoder();
    }


    @Autowired
    JwtAuthenticationTokenFilter jwtAuthenticationTokenFilter;

    @Override
    protected void configure(HttpSecurity http) throws Exception {
        http
                //关闭csrf
                .csrf().disable()
                //不通过Session获取SecurityContext
                .sessionManagement().sessionCreationPolicy(SessionCreationPolicy.STATELESS)
                .and()
                .authorizeRequests()
                // 对于登录接口 允许匿名访问
                .antMatchers("/user/login").anonymous()
                // 除上面外的所有请求全部需要鉴权认证
                .anyRequest().authenticated();

        // 把token校验过滤器添加到UsernamePasswordAuthenticationFilter过滤器之前
        http.addFilterBefore(jwtAuthenticationTokenFilter, UsernamePasswordAuthenticationFilter.class);
    }

    @Bean
    @Override
    public AuthenticationManager authenticationManagerBean() throws Exception {
        return super.authenticationManagerBean();
    }
}

~~~

## ③ 退出登录



### 1 退出登录接口的实现

我们只需要定义一个登陆接口，然后获取SecurityContextHolder中的认证信息，删除redis中对应的数据即可。

~~~java
/***
     *  退出登录功能的实现, 需要请求头中必须携带token才能正常访问 为什么呢？？
     *  回答：退出登录功能肯定需要登录了后才能够访问，登录后SecurityContextHolder中才存储有用户信息
     * @return
     */
    @Override
    public ResponseResult logout() {
        //获取userId并删除Redis中的用户信息
        LoginUser loginUser = (LoginUser) SecurityContextHolder.getContext().getAuthentication().getPrincipal();
        String userId = loginUser.getUser().getId().toString();
        redisCache.deleteObject(SystemConstants.RECEPTION_REDIS_TOKEN_KEY_PREFIX +userId);
        return ResponseResult.okResult();
    }
~~~

### 2 关闭默认的退出登录接口

~~~java
//关闭默认的退出登录接口，请求地址也是"/logout",防止与自定义的注销接口冲突
        http.logout().disable();
~~~

# (2) 1级 授权

## 1 授权基本流程

![image-20211214144425527](img/image-20211214144425527.png)

​	在SpringSecurity中，会使用默认的FilterSecurityInterceptor来进行权限校验。在FilterSecurityInterceptor中会从SecurityContextHolder获取其中的Authentication，然后获取其中的权限信息。判断当前用户是否拥有访问当前资源所需的权限。

​	所以我们在项目中只需要

1. ​    把当前登录用户的权限信息也存入Authentication。
2. ​	然后设置我们的资源所需要的权限即可。

## 2 2级 限制访问资源所需权限

​	SpringSecurity为我们提供了基于注解的权限控制方案，这也是我们项目中主要采用的方式。我们可以使用注解去指定访问对应的资源所需的权限。

​	但是要使用它我们需要先开启相关配置。

~~~~java
// 开启权限管理的支持
@EnableGlobalMethodSecurity(prePostEnabled = true)
public class SecurityConfigure extends WebSecurityConfigurerAdapter
~~~~

​	然后就可以使用对应的注解。@PreAuthorize

~~~~java
@RestController
public class HelloController {

    @RequestMapping("/hello")
    @PreAuthorize("hasAuthority('test')") // 表示调用内置的hasAuthority('test')方法，判断当前用户是否有test权限
    public String hello(){
        return "hello";
    }
}
~~~~

### 封装权限信息

修改LoginUser类，添加权限字段


```java
/**
 * @Author 三更  B站： https://space.bilibili.com/663528522
 */
   @Data
   @NoArgsConstructor
   public class LoginUser implements UserDetails {

       private User user;

       //存储权限信息
       private List<String> permissions;


       public LoginUser(User user,List<String> permissions) {
           this.user = user;
           this.permissions = permissions;
       }
       
    // 存储SpringSecurity所需要的权限信息的集合，设置对象序列化为JSON时忽略这个字段（因为要存储到Redis中）
    @JSONField(serialize = false)
    private List<GrantedAuthority> authorities;

    @Override
    public  Collection<? extends GrantedAuthority> getAuthorities() {
        if(authorities!=null){
            return authorities;
        }
        //把permissions中字符串类型的权限信息转换成GrantedAuthority实现类对象存入authorities中
        authorities = permissions.stream().
                map(SimpleGrantedAuthority::new)
                .collect(Collectors.toList());
        return authorities;
    }

    @Override
    public String getPassword() {
        return user.getPassword();
    }

    @Override
    public String getUsername() {
        return user.getUserName();
    }

    @Override
    public boolean isAccountNonExpired() {
        return true;
    }

    @Override
    public boolean isAccountNonLocked() {
        return true;
    }

    @Override
    public boolean isCredentialsNonExpired() {
        return true;
    }

    @Override
    public boolean isEnabled() {
        return true;
    }

}
```
LoginUser修改完后我们就可以在UserDetailsServiceImpl中去把权限信息封装到LoginUser中了。从数据库中查询权限信息。

~~~~java
package com.sangeng.service.impl;

import com.baomidou.mybatisplus.core.conditions.query.LambdaQueryWrapper;
import com.baomidou.mybatisplus.core.conditions.query.QueryWrapper;
import com.baomidou.mybatisplus.extension.conditions.query.LambdaQueryChainWrapper;
import com.sangeng.domain.LoginUser;
import com.sangeng.domain.User;
import com.sangeng.mapper.UserMapper;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.security.core.userdetails.UserDetails;
import org.springframework.security.core.userdetails.UserDetailsService;
import org.springframework.security.core.userdetails.UsernameNotFoundException;
import org.springframework.stereotype.Service;

import java.util.ArrayList;
import java.util.Arrays;
import java.util.List;
import java.util.Objects;

/**
 * @Author 三更  B站： https://space.bilibili.com/663528522
 */
@Service
public class UserDetailsServiceImpl implements UserDetailsService {

    @Autowired
    private UserMapper userMapper;

    @Override
    public UserDetails loadUserByUsername(String username) throws UsernameNotFoundException {
        LambdaQueryWrapper<User> wrapper = new LambdaQueryWrapper<>();
        wrapper.eq(User::getUserName,username);
        User user = userMapper.selectOne(wrapper);
        if(Objects.isNull(user)){
            throw new RuntimeException("用户名或密码错误");
        }
        //TODO 根据用户查询权限信息 添加到LoginUser中
        List<String> list = new ArrayList<>(Arrays.asList("test"));
        return new LoginUser(user,list);
    }
}

~~~~

### 从数据库中查询权限信息

##### RBAC权限模型

​	RBAC权限模型（Role-Based Access Control）即：基于角色的权限控制。这是目前最常被开发者使用也是相对易用、通用权限模型。

​	![image-20211222110249727](img/image-20211222110249727.png)







## 3 自定义方法实现权限判断：

### 3.1 SecurityConfig类中添加注解

~~~java
// 开启权限管理的支持
@EnableGlobalMethodSecurity(prePostEnabled = true)
public class SecurityConfigure extends WebSecurityConfigurerAdapter
~~~

### 3.2定义权限判断类

~~~java
/**
 * 权限判断类，判断当前用户是否有足够的权限进行访问
 */
@Service("ps")//命名为ps
public class PermissionService {
    @Autowired
    private RoleService roleService;
    /**
     * 判断当前用户是否拥有传入的权限
     * @return
     */
    public boolean hasPermissions(String permission) {
        //TODO 三更这里写错了 如果用户身份是管理员(RoleId==1)，直接返回true（数据库中没有记录管理员的权限数据）
        Long RoleId = roleService.getRoleIdByUserId(SecurityUtils.getUserId());
        if(RoleId.equals(SystemConstants.ADMIN_USER)){
            return true;
        }
        //否则获取当前用户身份所具有的权限进行对比
        List<String> permissions = SecurityUtils.getLoginUser().getPermissions();
        return permissions.contains(permission);
    }
}

~~~

### 3.3给相应的方法设置需要的权限

1. @PostAuthorize注解用于在方法执行后再进行权限校验，允许方法执行但在返回结果之前进行权限验证
2. @PreAuthorize注解可以应用于方法级别或者类级别。它的作用是在方法调用之前对用户的权限进行校验，只有满足条件的用户才能继续执行方法。在校验过程中，如果用户没有权限访问该方法，则会抛出AccessDeniedException异常。

~~~java
/**
     * 删除标签
     * @param id
     * @return
     */
    //设置只有具有特定的权限的用户才能执行删除标签操作 使用ps类的hasPermissions方法进行判断,参数为content:tag:remove
    @PreAuthorize("@ps.hasPermissions('content:tag:remove')")
    @Override
    public ResponseResult<Void> deleteTag(Long id) {
        removeById(id);
        return ResponseResult.okResult();
    }

~~~







## 4完整的SecurityConfigure配置类

~~~java
@Configuration
//开启权限管理的支持
@EnableGlobalMethodSecurity(prePostEnabled = true)
public class SecurityConfigure extends WebSecurityConfigurerAdapter {
    @Autowired
    private JwtAuthenticationTokenFilter jwtAuthenticationTokenFilter;
    @Autowired
    private AuthenticationEntryPoint authenticationEntryPoint;
    @Autowired
    private AccessDeniedHandler accessDeniedHandler;

    /***
     * 注入AuthenticationManager到容器中
     * @return
     * @throws Exception
     */
    @Bean
    @Override
    public AuthenticationManager authenticationManagerBean() throws Exception {
        return super.authenticationManagerBean();
    }

    /***
     * 注入BCryptPasswordEncoder对象到容器中,SpringSecurity会自动使用BCryptPasswordEncoder进行加密
     * @return
     */
    @Bean
    public PasswordEncoder passwordEncoder(){
        return new BCryptPasswordEncoder();
    }


    @Override
    protected void configure(HttpSecurity http) throws Exception {
        http
                //关闭csrf
                .csrf().disable()
                //不通过Session获取SecurityContext 关闭Session
                .sessionManagement().sessionCreationPolicy(SessionCreationPolicy.STATELESS)
                .and()
                .authorizeRequests()
                // 对于登录接口 只能匿名访问(未登录可以访问，登录了就不能访问了)
                .antMatchers("/user/login").anonymous()
                // 除上面外的所有请求全部都需要认证才可访问
                .anyRequest().authenticated();
        //关闭默认的退出登录接口，请求地址也是"/logout",防止与自定义的注销接口冲突
        http
            .logout().disable();
        //允许跨域
        http.cors();
        //添加自定义的过滤器到UsernamePasswordAuthenticationFilter之前
        http.addFilterBefore(jwtAuthenticationTokenFilter, UsernamePasswordAuthenticationFilter.class);
        //添加自定义的认证、授权异常处理器
        http
                .exceptionHandling()
                .authenticationEntryPoint(authenticationEntryPoint)
                .accessDeniedHandler(accessDeniedHandler);
    }

}

~~~

## 5自定义认证、授权失败时进行的处理(返回的信息)

### 5.1自定义授权失败时触发的的异常处理器:

~~~java
/***
 * 自定义授权失败的异常处理器  AccessDeniedHandler访问被拒绝处理器
 */
@Component
public class AccessDeniedHandlerImpl implements AccessDeniedHandler{
    /***
     * 自定义没有权限操作时返回给前端的响应数据
     * @param httpServletRequest
     * @param httpServletResponse
     * @param e
     * @throws IOException
     * @throws ServletException
     */
    @Override
    public void handle(HttpServletRequest httpServletRequest,
                       HttpServletResponse httpServletResponse,
                       AccessDeniedException e) throws IOException, ServletException {
        e.printStackTrace();
        ResponseResult result = ResponseResult.errorResult(AppHttpCodeEnum.NO_OPERATOR_AUTH);
        WebUtils.renderString(httpServletResponse, JSON.toJSONString(result));
    }
}
~~~

### 5.2 自定义认证失败时触发的异常处理器

~~~java
@Component
public class AuthenticationEntryPointImpl implements AuthenticationEntryPoint {
    /***
     * 登录失败时自定义返回给前端的信息
     * @param httpServletRequest
     * @param httpServletResponse
     * @param e
     * @throws IOException
     * @throws ServletException
     */
    @Override
    public void commence(HttpServletRequest httpServletRequest,
                         HttpServletResponse httpServletResponse,
                         AuthenticationException e) throws IOException, ServletException {
        //打印错误信息
        e.printStackTrace();
        ResponseResult result = null;
        //用户不同的错误操作导致不同的异常类型，从而返回不同的错误信息
        if(e instanceof InsufficientAuthenticationException){
            //没有携带token进行访问就会触发InsufficientAuthenticationException，在这里设置返回数据
            result = ResponseResult.errorResult(AppHttpCodeEnum.NEED_LOGIN);
        }else if(e instanceof InternalAuthenticationServiceException){
            result = ResponseResult.errorResult(AppHttpCodeEnum.LOGIN_ERROR,e.getMessage());
        }else{
            result = ResponseResult.errorResult(AppHttpCodeEnum.SYSTEM_ERROR.getCode(),"认证错误！");
        }
        //以JSON字符串的形式响应给前端
        WebUtils.renderString(httpServletResponse, JSON.toJSONString(result));
    }
}

~~~

### 5.3 配置Security异常处理器

~~~java
        //添加自定义的认证、授权异常处理器
        http
                .exceptionHandling()
                .authenticationEntryPoint(authenticationEntryPoint)
                .accessDeniedHandler(accessDeniedHandler);
~~~



## 6全局异常处理器Controller,统一处理各种各样的异常

​	实际我们在开发过程中可能需要做很多的判断校验，如果出现了非法情况我们是期望响应对应的提示的。

但是如果我们每次都自己手动去处理就会非常麻烦。我们可以选择直接抛出异常的方式，然后对异常进行统一处理。把异常中的信息封装成ResponseResult响应给前端。

### 6.1自定义一个异常，进行统一异常处理

~~~java
public class SystemException extends RuntimeException{// 继承RuntimeException来自定义一个异常，只有异常才能够被@ControllerAdvice捕获

    private int code;

    private String msg;

    public int getCode() {
        return code;
    }

    public String getMsg() {
        return msg;
    }

    public SystemException(AppHttpCodeEnum httpCodeEnum) {
        //使用父类的构造器进行初始化
        super(httpCodeEnum.getMsg());
        this.code = httpCodeEnum.getCode();
        this.msg = httpCodeEnum.getMsg();
    }
    
}
~~~



### 6.2全局异常处理器

~~~java
//@RequestBody与@ControllerAdvice的合体
@RestControllerAdvice
@Slf4j
//酸辣粉4j，lombok的日志打印
public class GlobalExceptionHander{
    /***
     * 处理SystemException异常，把异常作为参数传入，处理完毕后返回ResponseResult对象给前端
     * @param e
     * @return
     */
    @ExceptionHandler(SystemException.class)
    public ResponseResult systemExceptionHandler(SystemException e){
        //打印异常信息 使用{}作为占位符传入异常e打印异常信息 虽然报红但是还可以使用
        log.error("出现了异常!{}",e);
        return ResponseResult.errorResult(e.getCode(),e.getMsg());
    }

    /***
     * 处理其他的异常Exception,设置返回给前端的数据
     * @param e
     * @return
     */
    @ExceptionHandler(Exception.class)
    public ResponseResult exceptionHandler(Exception e){
        return ResponseResult.errorResult(AppHttpCodeEnum.SYSTEM_ERROR.getCode(),e.getMessage());
    }
}

~~~



### 6.3具体使用：

~~~java
    @PostMapping("/login")
    public ResponseResult login(@RequestBody User user){
        if(!StringUtils.hasText(user.getUserName())){
            //会被GlobalExceptionHander的systemExceptionHandler(SystemException e)方法
            //捕获并处理(类似try-catch配合Controller)
            throw new SystemException(AppHttpCodeEnum.REQUIRE_USERNAME);
        }
        return loginService.login(user);
    }

~~~



