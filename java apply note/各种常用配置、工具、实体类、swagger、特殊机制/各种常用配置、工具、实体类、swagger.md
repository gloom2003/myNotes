# 各种常用配置、工具、实体类、swagger



## 8 实用工具类

### 8.1BeanCopyUtil

~~~java
public class BeanCopyUtil {
    private BeanCopyUtil() {
    }

    //方法上的泛型<V>,设置返回的类型为V类型，由传入Class参数的泛型决定V的类型
    //类似于List<String>,Class<String>表示传入的是String的字节码对象
    public static <V> V beanCopy(Object source, Class<V> clazz) {
        V v = null;
        try {
            v = clazz.newInstance();
            //使用org.springframework.beans.BeanUtils中的copyProperties方法
            BeanUtils.copyProperties(source, v);
        } catch (Exception e) {
            e.printStackTrace();
        }
        return v;
    }

    public static <V,O> List<V> beanListCopy(List<O> sourceLists, Class<V> clazz) {
        //1.0写法:
//        List<V> result = new ArrayList<>();
//        for (O o : sourceLists) {
//            result.add(beanCopy(o,clazz));
//        }
//        return result;
        //2.0写法 stream操作
        return sourceLists.stream()
                .map(o->beanCopy(o,clazz))
                .collect(Collectors.toList());

    }
}

~~~

### 8.2JWT工具类 根据id生成对应的token

~~~java
public class JwtUtil {

    //有效期为
    public static final Long JWT_TTL = 24*60 * 60 *1000L;// 60 * 60 *1000  一个小时
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
~~~

### 8.3生成一个不重复的文件名的工具

~~~java
public class PathUtils {
    /**
     * 生成一个不重复的文件名
     * 传入文件名后根据日期和uuid拼接一个文件名并返回
     * @param fileName
     * @return
     */
    public static String generateFilePath(String fileName){
        //根据日期生成路径   2022/1/15/
        SimpleDateFormat sdf = new SimpleDateFormat("yyyy/MM/dd/");
        String datePath = sdf.format(new Date());
        //uuid作为文件名，生成不重复的id
        String uuid = UUID.randomUUID().toString().replaceAll("-", "");
        //后缀和文件后缀一致
        int index = fileName.lastIndexOf(".");
        // test.jpg -> .jpg
        String fileType = fileName.substring(index);
        return new StringBuilder().append(datePath).append(uuid).append(fileType).toString();
    }
}
~~~

### 8.4Redis简化操作工具类

~~~java
@Component
public class RedisCache
{
    @Autowired
    public RedisTemplate redisTemplate;

    /**
     * 对map中的value进行递增操作
     * @param mapKey
     * @param viewCountKey
     * @param v
     */
    public void incrementCacheMapValue(String mapKey,String viewCountKey,int v){
        redisTemplate.opsForHash().increment(mapKey,viewCountKey,v);
    }

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
~~~



### 8.5 SecurityUtils 快速获取用户信息

~~~java
public class SecurityUtils
{

    /**
     * 获取用户
     **/
    public static LoginUser getLoginUser()
    {
        return (LoginUser) getAuthentication().getPrincipal();
    }

    /**
     * 获取Authentication
     */
    public static Authentication getAuthentication() {
        return SecurityContextHolder.getContext().getAuthentication();
    }


    public static Long getUserId() {
        return getLoginUser().getUser().getId();
    }
}
~~~

### 8.6 Http响应工具类

~~~java
public class WebUtils
{
    /**
     * 将字符串渲染到客户端
     * 
     * @param response 渲染对象
     * @param string 待渲染的字符串
     * @return null
     */
    public static void renderString(HttpServletResponse response, String string) {
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
    }

    /**
     * 设置用户下载、导出文件时接收到文件的响应格式
     * @param filename
     * @param response
     * @throws UnsupportedEncodingException
     */
    public static void setDownLoadHeader(String filename, HttpServletResponse response) throws UnsupportedEncodingException {
        response.setContentType("application/vnd.openxmlformats-officedocument.spreadsheetml.sheet");
        response.setCharacterEncoding("utf-8");
        String fname= URLEncoder.encode(filename,"UTF-8").replaceAll("\\+", "%20");
        response.setHeader("Content-disposition","attachment; filename="+fname);
    }
}
~~~

## 9 实体类、ResponseResult、AppHttpCodeEnum类

### 9.1 ResponseResult类

~~~java
// 在将该对象转换为JSON字符串时，只有属性值不为null的属性才会被包含在生成的JSON字符串中，而属性值为null的属性则会被忽略。
@JsonInclude(JsonInclude.Include.NON_NULL)
public class ResponseResult<T> implements Serializable {
    private Integer code;
    private String msg;
    private T data;

    /***
     * 无参构造器表示成功状态
     */
    public ResponseResult() {
        this.code = AppHttpCodeEnum.SUCCESS.getCode();
        this.msg = AppHttpCodeEnum.SUCCESS.getMsg();
    }

    public ResponseResult(Integer code, T data) {
        this.code = code;
        this.data = data;
    }

    public ResponseResult(Integer code, String msg, T data) {
        this.code = code;
        this.msg = msg;
        this.data = data;
    }

    public ResponseResult(Integer code, String msg) {
        this.code = code;
        this.msg = msg;
    }

    public static ResponseResult errorResult(int code, String msg) {
        ResponseResult result = new ResponseResult();
        return result.error(code, msg);
    }
    public static ResponseResult okResult() {
        ResponseResult result = new ResponseResult();
        return result;
    }
    public static ResponseResult okResult(int code, String msg) {
        ResponseResult result = new ResponseResult();
        return result.ok(code, null, msg);
    }

    public static ResponseResult okResult(Object data) {
        ResponseResult result = setAppHttpCodeEnum(AppHttpCodeEnum.SUCCESS, AppHttpCodeEnum.SUCCESS.getMsg());
        if(data!=null) {
            result.setData(data);
        }
        return result;
    }

    public static ResponseResult errorResult(AppHttpCodeEnum enums){
        return setAppHttpCodeEnum(enums,enums.getMsg());
    }

    public static ResponseResult errorResult(AppHttpCodeEnum enums, String msg){
        return setAppHttpCodeEnum(enums,msg);
    }

    public static ResponseResult setAppHttpCodeEnum(AppHttpCodeEnum enums){
        return okResult(enums.getCode(),enums.getMsg());
    }

    private static ResponseResult setAppHttpCodeEnum(AppHttpCodeEnum enums, String msg){
        return okResult(enums.getCode(),msg);
    }

    public ResponseResult<?> error(Integer code, String msg) {
        this.code = code;
        this.msg = msg;
        return this;
    }

    public ResponseResult<?> ok(Integer code, T data) {
        this.code = code;
        this.data = data;
        return this;
    }

    public ResponseResult<?> ok(Integer code, T data, String msg) {
        this.code = code;
        this.data = data;
        this.msg = msg;
        return this;
    }

    public ResponseResult<?> ok(T data) {
        this.data = data;
        return this;
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



}
~~~

### 9.2AppHttpCodeEnum枚举类

~~~java
public enum AppHttpCodeEnum {

    // 成功
    SUCCESS(200,"操作成功"),
    // 登录
    NEED_LOGIN(401,"需要登录后操作"),
    NO_OPERATOR_AUTH(403,"无权限操作"),
    SYSTEM_ERROR(500,"出现错误"),
    USERNAME_EXIST(501,"用户名已存在"),
     PHONENUMBER_EXIST(502,"手机号已存在"),
    EMAIL_EXIST(503, "邮箱已存在"),
    REQUIRE_USERNAME(504, "必须填写用户名"),
    LOGIN_ERROR(505,"用户名或密码错误"),
    COMMENT_IS_EMPTY(506,"评论内容不能为空"),
    FILE_TYPE_ERROR(507,"文件类型错误，请上传png格式"),
    USERNAME_ILLEGAL(508,"用户名非法"),
    PASSWORD_ILLEGAL(509,"密码非法"),
    EMAIL_ILLEGAL(510,"邮箱非法"),
    NICKNAME_ILLEGAL(511,"昵称非法"),
    NICKNAME_EXIST(512,"昵称已存在"),
    CATEGORY_EXIST(513,"此分类已存在"),
    HAVE_CHILDREN_MENU_NOT_APPLY_DELETE(513,"存在子菜单不允许删除");

    int code;
    String msg;

    AppHttpCodeEnum(int code, String errorMessage){
        this.code = code;
        this.msg = errorMessage;
    }

    public int getCode() {
        return code;
    }

    public String getMsg() {
        return msg;
    }
}

~~~



## 10 配置类

### 10.1 配置Redis的序列化类

~~~java
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
~~~



### 10.2Redis配置类

~~~java
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
~~~



### 10.3设置跨域、FastJson配置解决时间的显示

配置FastJson，使用FastJson进行json转换，替换默认的json转换（可以解决从数据库查询出来的Date类型的数据经过json转换后的格式问题）

~~~java
@Configuration
public class WebConfig implements WebMvcConfigurer {

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
                // 跨域允许时间
                .maxAge(SystemConstants.CROS_ALLOW_TIME);
    }

    /***
     * FastJson配置,解决时间显示格式不正常的问题
     */
    @Bean//使用@Bean注入fastJsonHttpMessageConvert
    public HttpMessageConverter fastJsonHttpMessageConverters() {
        //1.需要定义一个Convert转换消息的对象
        FastJsonHttpMessageConverter fastConverter = new FastJsonHttpMessageConverter();
        FastJsonConfig fastJsonConfig = new FastJsonConfig();
        fastJsonConfig.setSerializerFeatures(SerializerFeature.PrettyFormat);
        fastJsonConfig.setDateFormat("yyyy-MM-dd HH:mm:ss");
        //把响应中所有Long类型转换为String类型，防止前端的long类型出现精度丢失，应该使用字符串来接收
        SerializeConfig.globalInstance.put(Long.class, ToStringSerializer.instance);

        fastJsonConfig.setSerializeConfig(SerializeConfig.globalInstance);
        fastConverter.setFastJsonConfig(fastJsonConfig);
        HttpMessageConverter<?> converter = fastConverter;
        return converter;
    }

    @Override
    public void configureMessageConverters(List<HttpMessageConverter<?>> converters) {
        // 添加自定义的fastJson消息转换器到消息转换器列表中即可
        converters.add(fastJsonHttpMessageConverters());
    }

}
~~~

使用：

~~~java
/**
 * 院舍列表VO类
 */
@Data
@AllArgsConstructor
@NoArgsConstructor
// 转换为JSON时忽略为null的字段，不进行转换
@JsonInclude(JsonInclude.Include.NON_NULL)
public class FacilityListVO implements Serializable {

    private static final long serialVersionUID = 2181673880660160143L;
    /**
     * 院舍列表的基本信息
     */
    private List<FacilityVO> lists;
    /**
     * 序号最小的院舍的基本信息
     */
    private FacilityBaseInfo facilityBaseInfo;
    /**
     * 院舍的统计信息
     */
    private FacilityCountInfo facilityCountInfo;
}

~~~



## 10.4 Swagger的配置与使用

优点：

1.代码变，文档变。只需要少量的注解，Swagger 就可以根据代码自动生成 API 文档，很好的保证了文档的时效性。
2.跨语言性，支持 40 多种语言。
3.Swagger UI 呈现出来的是一份可交互式的 API 文档，我们可以直接在文档页面尝试 API 的调用，省去了准备复杂的调用参数的过程。

#### 10.4.1 配置与查看

在启动类上或者配置类加 @EnableSwagger2 注解

~~~~java
@SpringBootApplication
@MapperScan("com.sangeng.mapper")
@EnableScheduling
@EnableSwagger2
public class SanGengBlogApplication {
    public static void main(String[] args) {
        SpringApplication.run(SanGengBlogApplication.class,args);
    }
}
~~~~

查看：

访问：http://localhost:7777/swagger-ui.html  注意其中localhost和7777要调整成实际项目的域名和端口号。

在配置类中进行文档信息配置


~~~java
/**
 * swagger文档信息配置,设置上半部分的信息
 */
@Configuration
public class SwaggerConfig {
    @Bean
    public Docket customDocket() {
        return new Docket(DocumentationType.SWAGGER_2)
                .apiInfo(apiInfo())
                .select()
                //配置swagger的包扫描路径
                .apis(RequestHandlerSelectors.basePackage("com.kana.Controller"))
                .build();
    }

    /**
     * 配置swagger文档的介绍信息
     * @return
     */
    private ApiInfo apiInfo() {
        Contact contact = new Contact("kana", "http://www.kana.com", "13432241185@163.com");
        return new ApiInfoBuilder()
                .title("前后端分离的博客系统")
                .description("后端基于SpringBoot2.5.0 + mybatis-plus + Redis + SpringSecurity,前端基于vue进行开发")
                .contact(contact)   // 联系方式
                .version("1.1.0")  // 版本
                .build();
    }
}
~~~



#### 10.4.2 swagger注解的使用

#### 1 Controller类配置

##### 1.1 @Api 注解 

属性介绍：

tags  设置标签

description 设置描述信息

~~~~java
@RestController
@RequestMapping("/comment")
@Api(tags = "评论",description = "评论相关接口")
public class CommentController {
}
~~~~





#### 2 Controller接口配置

##### 2.1 Controller接口描述配置@ApiOperation

~~~~java
    @GetMapping("/linkCommentList")
    @ApiOperation(value = "友链评论列表",notes = "获取一页友链评论") // Operation 活动、行动
    public ResponseResult linkCommentList(Integer pageNum,Integer pageSize){
        return commentService.commentList(SystemConstants.LINK_COMMENT,null,pageNum,pageSize);
    }
~~~~



##### 2.2 Controller层接口参数描述

 @ApiImplicitParam 用于描述接口的参数，但是一个接口可能有多个参数，所以一般与 @ApiImplicitParams 组合使用。

~~~~java
    @GetMapping("/linkCommentList")
    @ApiOperation(value = "友链评论列表",notes = "获取一页友链评论")
    @ApiImplicitParams({
           @ApiImplicitParam(name = "pageNum",value = "页号"),
           @ApiImplicitParam(name = "pageSize",value = "每页大小")
    }
    )
    public ResponseResult linkCommentList(Integer pageNum,Integer pageSize){
        return commentService.commentList(SystemConstants.LINK_COMMENT,null,pageNum,pageSize);
    }
~~~~

或者：

~~~java
    @PostMapping(value = "/auth/facility",headers = Constant.LOGIN_TOKEN_KEY)
										// 设置登录token请求的Header Key：Authorization
    @ApiOperation("添加院舍")										// 不显示这个参数
    public R addFacility(@RequestBody AddFacilityDTO addFacilityDTO, @ApiParam(hidden = true) Long current_userId){
        return R.ok();
    }
~~~



#### 3 实体类配置

##### 3.1 实体的描述配置@ApiModel 

@ApiModel用于描述实体类。

~~~~java
@Data
@AllArgsConstructor
@NoArgsConstructor
@ApiModel(description = "添加评论dto")
public class AddCommentDto{
    //..
}
~~~~

 

##### 3.2 实体的属性的描述配置@ApiModelProperty

@ApiModelProperty用于描述实体的属性

~~~~java
    @ApiModelProperty(value = "验证码", example = "999818",notes = "评论类型（0代表文章评论，1代表友链评论）")
    private String type;
~~~~

健康系统例子：

代码：

~~~java
@ApiModel("登录上传数据对象")
@Data
@AllArgsConstructor
@NoArgsConstructor
public class LoginDTO {
    /**
     * 用户名
     */
    @ApiModelProperty(value = "用户名", example = "admin", required = true)
    private String username;

    /**
     * 密码
     */
    @ApiModelProperty(value = "密码", example = "123456", required = true)
    private String password;

    /**
     * 验证码
     */
    @ApiModelProperty(value = "验证码", example = "999818")
    private String code;

    /**
     * 登录客户端ID
     */
    @ApiModelProperty(value = "登录客户端ID：healthy-app或healthy-manager", example = "healthy-manager", required = true)
    private String clientId;
}

~~~





#### 4 博客例子：

~~~java
@RestController
@RequestMapping("/comment")
//swagger的标签注释
@Api(tags = "评论标签",description = "评论相关接口")
public class CommentController {
    @Autowired
    private CommentService commentService;

    //swagger方法注释
    @ApiOperation(value = "友链评论接口",notes = "获取一页友链评论")
    @GetMapping("/linkCommentList")
    //swagger方法参数注释
    @ApiImplicitParams({
            @ApiImplicitParam(name = "pageNum", value = "获取第几页的评论"),
            @ApiImplicitParam(name = "pageSize", value = "一页获取多少条评论")
    }
    )
    public ResponseResult linkCommentList(Integer pageNum,Integer pageSize){
        return commentService.commentList(SystemConstants.LINK_COMMENT,null,pageNum,pageSize);
    }
}

~~~



## 12 下载导出文件(excel)

### 12.1设置读取application.yml文章中的信息

读取类中的配置：属性+set方法

~~~java
@Data
//设置读取前缀,类中的属性会从application.yml中进行读取并使用set方法进行赋值
@ConfigurationProperties(prefix = "oss")
public class OssUploadServiceImpl implements UploadService {
    private String accessKey;
    private String secretKey;
    private String bucket;
}
~~~

application.yml中的配置：

~~~yaml
#oss为前缀，用于记录信息给其他类进行读取
oss:
  accessKey: -11111-m-hi41222YljfYbCO3PgceqmpPA54
  secretKey: _ns22222RMUaiDOz243AOe4IdNZZ6xxsOCsRAW
  bucket: cxk-1

~~~

或使用@Value注解

### 12.3实现下载文件例如：导出excel文件

查看EasyExcel的文档，使用EasyExcel相关的api进行实现。

~~~java
public void export(HttpServletResponse response){
        try {
            //设置下载文件时的响应头中的字段
            WebUtils.setDownLoadHeader("文章分类数据.xlsx",response);
            //获取分类数据
            List<Category> categories = categoryService.list();
            List<ExcelCategoryVo> excelCategoryVos = BeanCopyUtil.beanListCopy(categories, ExcelCategoryVo.class);
            //写入数据到Excel中，响应中返回文件数据而不是json数据
            //设置写入的实体对象
            EasyExcel.write(response.getOutputStream(), ExcelCategoryVo.class)
                    //设置自动关闭流失效
                    .autoCloseStream(Boolean.FALSE)
                    //设置工作簿名称
                    .sheet("分类导出")
                    //设置写入的具体数据
                    .doWrite(excelCategoryVos);
        } catch (Exception e) {
            //出现异常则返回json数据
            e.printStackTrace();
            ResponseResult result = ResponseResult.errorResult(AppHttpCodeEnum.SYSTEM_ERROR);
            //设置写入响应的数据
            WebUtils.renderString(response, JSON.toJSONString(result));
        }

    }

~~~

~~~java
/**
     * 设置用户下载、导出文件时接收到文件的响应格式
     * @param filename
     * @param response
     * @throws UnsupportedEncodingException
     */
    public static void setDownLoadHeader(String filename, HttpServletResponse response) throws UnsupportedEncodingException {
        response.setContentType("application/vnd.openxmlformats-officedocument.spreadsheetml.sheet");
        response.setCharacterEncoding("utf-8");
        String fname= URLEncoder.encode(filename,"UTF-8").replaceAll("\\+", "%20");
        response.setHeader("Content-disposition","attachment; filename="+fname);
    }
~~~



## 2级 使用MapStruct完成Bean拷贝

#### 基本使用

导入依赖：

~~~xml
    <properties>
        <org.mapstruct.version>1.5.2.Final</org.mapstruct.version>
    </properties>

		<!-- MapStruct -->
        <dependency>
            <groupId>org.mapstruct</groupId>
            <artifactId>mapstruct</artifactId>
            <version>${org.mapstruct.version}</version>
        </dependency>
~~~



使用@Mapper注解（org.mapstruct.Mapper这个包）来标识一个接口，在接口中定义进行Bean拷贝的方法。

~~~java
import org.mapstruct.Mapper;

@Mapper(componentModel = "spring") // 配置后就不需要在接口中添加 `INSTANCE` 字段，并且使@Mapper有@Component的作用
interface MsSampleMapper {
    /**
     * 将SampleDO 转换成 SampleDTO
     * @param sample 待转换的DO
     * @return 转换结果
     */
    SampleDTO sampleToSampleDto(Sample sample);
}
~~~

或者：

~~~java
import org.mapstruct.Mapper;
import org.mapstruct.Mapping;
import org.mapstruct.factory.Mappers;

@Mapper
public interface FacilityDOConverter{

    FacilityDOConverter INSTANCE = Mappers.getMapper(FacilityDOConverter.class);

    @Mapping(source = "id", target = "facilityId")
    FacilityDTO doMapToDTO(FacilityDO facilityDO);

    @Mapping(source = "facilityId", target = "id")
    FacilityDO dtoMapToDO(FacilityDTO facilityDO);

}
~~~



#### 源类与终类属性名称不同时

我们先更新`Doctor`类，添加一个属性`specialty`：

```java
public class Doctor {
    private int id;
    private String name;
    private String specialty;
    // getters and setters or builder
}
```

在`DoctorDto`类中添加一个`specialization`属性：

```java
public class DoctorDto {
    private int id;
    private String name;
    private String specialization;
    // getters and setters or builder
}
```

现在，我们需要让 `DoctorMapper` 知道这里的不一致。我们可以使用 `@Mapping` 注解，并设置其内部的 `source` 和 `target` 标记分别指向不一致的两个字段。

```java
@Mapper
public interface DoctorMapper {
    DoctorMapper INSTANCE = Mappers.getMapper(DoctorMapper.class);

    @Mapping(source = "doctor.specialty", target = "specialization")
    DoctorDto toDto(Doctor doctor);
}
```

这个注解代码的含义是：`Doctor`中的`specialty`字段对应于`DoctorDto`类的 `specialization` 。

编译之后，会生成如下实现代码：

```java
public class DoctorMapperImpl implements DoctorMapper {
@Override
    public DoctorDto toDto(Doctor doctor) {
        if (doctor == null) {
            return null;
        }

        DoctorDtoBuilder doctorDto = DoctorDto.builder();

        doctorDto.specialization(doctor.getSpecialty());
        doctorDto.id(doctor.getId());
        doctorDto.name(doctor.getName());

        return doctorDto.build();
    }
}
```

#### 有多个源类时

有时，单个类不足以构建DTO，我们可能希望将多个类中的值聚合为一个DTO，供终端用户使用。这也可以通过在`@Mapping`注解中设置适当的标志来完成。

对象 `RPanUser`:

```java
public class RPanUser{
    private String username;
}
```

对象 `RPanUserFile`

```java
public class RPanUserFile {

    private Long fileId;
    
    private String filename;

}

```

对象UserInfoVo

~~~java
public class UserInfoVO{
    private String username;
    private Long rootFileId;
    private String rootFilename;

}

~~~



转换的接口如下：

```java
@Mapper(componentModel = "spring")
public interface UserConverter {

    @Mapping(source = "entity.username",target = "username")
    @Mapping(source = "rPanUserFile.fileId",target = "rootFileId")
    @Mapping(source = "rPanUserFile.filename",target = "rootFilename")
    UserInfoVO assembleUserInfoVO(RPanUser entity, RPanUserFile rPanUserFile);

}

```



如果 `RPanUser` 类和 `RPanUserFile` 类包含同名的字段，我们必须让映射器知道使用哪一个，否则它会抛出一个异常。所以我们添加了另多个`@Mapping`注解来显示的进行指定。



## 2级 项目入参数校验器的使用

#### 4级 准备工作

1 写一个配置类

~~~java
/**
 * 统一的参数校验器
 */
@SpringBootConfiguration
@Slf4j
public class WebValidatorConfig {// Validator 验证器

    private static final String FAIL_FAST_KEY = "hibernate.validator.fail_fast";

    @Bean
    public MethodValidationPostProcessor methodValidationPostProcessor() {
        MethodValidationPostProcessor postProcessor = new MethodValidationPostProcessor();
        postProcessor.setValidator(rPanValidator());
        log.info("The hibernate validator is loaded successfully!");
        return postProcessor;
    }

    /**
     * 构造项目的方法参数校验器
     *
     * @return
     */
    private Validator rPanValidator() {
        ValidatorFactory validatorFactory = Validation.byProvider(HibernateValidator.class)
                .configure()
                // 配置快速失败的验证机制，只要有一个参数不合法就抛出异常，后面的就不验证了
                .addProperty(FAIL_FAST_KEY, RPanConstants.TRUE_STR)
                .buildValidatorFactory();
        Validator validator = validatorFactory.getValidator();
        return validator;
    }


}

~~~

准备枚举类ResponseCode

~~~java
/**
 * 项目公用返回状态码
 */
@AllArgsConstructor
@Getter
public enum ResponseCode {

    /**
     * 成功
     */
    SUCCESS(0, "SUCCESS"),
    /**
     * 错误
     */
    ERROR(1, "ERROR"),
    /**
     * token过期
     */
    TOKEN_EXPIRE(2, "TOKEN_EXPIRE"),
    /**
     * 参数错误
     */
    ERROR_PARAM(3, "ERROR_PARAM"),
    /**
     * 无权限访问
     */
    ACCESS_DENIED(4, "ACCESS_DENIED"),
    /**
     * 分享的文件丢失
     */
    SHARE_FILE_MISS(5, "分享的文件丢失"),
    /**
     * 分享已经被取消
     */
    SHARE_CANCELLED(6, "分享已经被取消"),
    /**
     * 分享已过期
     */
    SHARE_EXPIRE(7, "分享已过期"),
    /**
     * 需要登录
     */
    NEED_LOGIN(10, "NEED_LOGIN"),

    TOKEN_ERROR(11,"token非法"),
    USERNAME_PASSWORD_ERROR(12,"用户名或密码错误");

    /**
     * 状态码
     */
    private Integer code;

    /**
     * 状态描述
     */
    private String desc;

}

~~~





2 配置全局异常处理器

~~~java
/**
 * 全局异常处理器
 */
@RestControllerAdvice
public class WebExceptionHandler {

    @ExceptionHandler(value = MethodArgumentNotValidException.class)
    public R methodArgumentNotValidExceptionHandler(MethodArgumentNotValidException e) {
        ObjectError objectError = e.getBindingResult().getAllErrors().stream().findFirst().get();
        return R.fail(ResponseCode.ERROR_PARAM.getCode(), objectError.getDefaultMessage());
    }

    @ExceptionHandler(value = ConstraintViolationException.class)
    public R constraintDeclarationExceptionHandler(ConstraintViolationException e) {
        ConstraintViolation<?> constraintViolation = e.getConstraintViolations().stream().findFirst().get();
        return R.fail(ResponseCode.ERROR_PARAM.getCode(), constraintViolation.getMessage());
    }

    @ExceptionHandler(value = MissingServletRequestParameterException.class)
    public R missingServletRequestParameterExceptionHandler(MissingServletRequestParameterException e) {
        return R.fail(ResponseCode.ERROR_PARAM);
    }

    @ExceptionHandler(value = IllegalStateException.class)
    public R illegalStateExceptionHandler(IllegalStateException e) {
        return R.fail(ResponseCode.ERROR_PARAM);
    }

    @ExceptionHandler(value = BindException.class)
    public R bindExceptionHandler(BindException e) {
        FieldError fieldError = e.getBindingResult().getFieldErrors().stream().findFirst().get();
        return R.fail(ResponseCode.ERROR_PARAM.getCode(), fieldError.getDefaultMessage());
    }

    @ExceptionHandler(value = RPanFrameworkException.class)
    public R rPanFrameworkExceptionHandler(RPanFrameworkException e) {
        return R.fail(ResponseCode.ERROR.getCode(), e.getMessage());
    }

}

~~~



#### 具体使用:

**在类上**添加@Validated注解，在需要验证的属性旁添加验证注解即可：

如果验证不通过则会抛出相应的异常。

~~~java
@RestController
@Validated
public class RPanServerLauncher {

    @GetMapping("/test")
    public R test(@NotBlank(message = "name不能为空") String name){
        return R.success(name);
    }
}
~~~

**或者** 在**参数列表**添加@Validated注解，以使参数内部的属性能够进行验证

~~~java
    @PostMapping("/register")
    public R userRegister(@Validated @RequestBody UserRegisterPO userRegisterPO){
        UserRegisterContext userRegisterContext =
                userConverter.UserRegisterContext2UserRegisterPO(userRegisterPO);
        return null;
    }
~~~

其中UserRegisterPO为：

~~~java
public class UserRegisterPO implements Serializable {

    private static final long serialVersionUID = 2655365643649022645L;

    @ApiModelProperty(value = "用户名",required = true)
    @NotBlank(message = "用户名不能为空")
    @Pattern(regexp = "^[0-9a-zA-Z]{6,16}$",message = "用户名必须由6-16位数字与字母组成")
    private String username;

    @ApiModelProperty(value = "密码",required = true)
    @NotBlank(message = "密码不能为空")
    @Length(min = 8,max = 16,message = "请输入8-16位的密码" )
    private String password;

    @ApiModelProperty(value = "密保问题",required = true)
    @Length(max = 100,message = "请输入长度小于100的密保问题")
    private String question;

    @ApiModelProperty(value = "密保答案",required = true)
    @Length(max = 100,message = "请输入长度小于100的密保答案")
    private String answer;
}

~~~



#### 根据验证注解来分类

##### @Valid 和 @Validated 

总结：

（1）@Valid 和 @Validated 两者都可以对数据进行校验，待校验字段上打的规则注解（@NotNull, @NotEmpty等）都可以对 @Valid 和 @Validated 生效；

（2）@Valid 进行校验的时候，需要用 BindingResult 来做一个校验结果接收。当校验不通过的时候，如果手动不 return ，则并不会阻止程序的执行；

（3）@Validated 进行校验的时候，当校验不通过的时候，程序会抛出400异常，阻止方法中的代码执行，这时需要再写一个全局校验异常捕获处理类，然后返回校验提示。

##### @Min

~~~java
@Getter
@Setter
@ToString
public class PageQuery {
    @Min(value = 1, message = "页码最小值为1") // 限制pageIndex的值最小为1，不满足则
    @ApiModelProperty(value = "查询页码", example = "1")
    private long pageIndex;

    @Min(value = 1, message = "条数最小值为1")
    @ApiModelProperty(value = "查询条数", example = "10")
    private long pageSize;
}
~~~

##### 其他：

![](img\校验注解.png)

##### @NotBlank

~~~java
@EqualsAndHashCode(callSuper = true)
@Data
@ApiModel("示例查询对象")
public class SampleQuery extends PageQuery {
    @NotBlank(message = "用户名不能为空")
    @ApiModelProperty(value = "姓名", example = "张三")
    private String name;
}
~~~

##### @NotNull,@Size

~~~java
public class UserAccount {

    @NotNull
    @Size(min = 4, max = 15)
    private String password;

    @NotBlank
    private String name;

}
~~~

##### @Pattern

~~~java
    @NotBlank(message = "用户名不能为空")
    @Pattern(regexp = "^[0-9a-zA-Z]{6,16}$",message = "用户名必须由6-16位数字与字母组成") // 使用正则表达式
    private String username;
~~~

#### 根据数据类型分类：



##### Integer、Long 类：

~~~java
	@Min(value = 1, message = "条数最小值为1")// 或者 @Max
    @NotNull(message = "院捨id不能爲空")
    @Range(message = "年龄范围为{min}到{max}之间",min=1,max=100)
	private Integer facilityId;
~~~

##### String类：

~~~java
    @NotNull
	@NotBlank
    @Size(min = 4, max = 15)
	@Length()
	@Pattern(regexp = "^[0-9a-zA-Z]{6,16}$",message = "用户名必须由6-16位数字与字母组成") // 使用正则表达式
    private String password;
~~~



##### 集合类：

~~~java
@NotEmpty(message="兴趣爱好不能为空")
@Size(message="兴趣选择最多{max}个"，max=5)
private List<String>hobbyList;
~~~

#### @NotNull区别

1.@NotNull：不能为null，但可以为empty

```sh
(""," ","   ")     # 都可以
```

 

2.@NotEmpty：不能为null，而且长度必须大于0

```sh
("  ","   ")  # 都可以
```

3.@NotBlank：只能作用在String上，不能为null，而且调用trim()后，长度必须大于0

```sh
("test")   # 即：必须有实际字符
```

4.例如：

```java
1.String name = null;

@NotNull: false
@NotEmpty:false 
@NotBlank:false 



2.String name = "";

@NotNull:true
@NotEmpty: false
@NotBlank: false



3.String name = " ";

@NotNull: true
@NotEmpty: true
@NotBlank: false



4.String name = "Great answer!";

@NotNull: true
@NotEmpty:true
@NotBlank:true

```



#### implements Serializable的意义

gpt4回答：

在Java中，`implements Serializable`是一个标记接口，表示类的对象可以被序列化。对象序列化是将对象的状态转换为可以被存储或传输的格式（如存储到文件中，或通过网络传输到另一台机器上）的过程。实现了`Serializable`接口的类的对象可以被`ObjectOutputStream` 类序列化，而对象的反序列化则是通过`ObjectInputStream`类完成的。

以下是一些常见场景，你可能需要让你的DO（Data Object）类实现`Serializable`接口：

1. **网络传输**：如果你需要通过网络从一个JVM传输对象到另一个JVM，那么这个对象需要是可序列化的。例如，在RMI（远程方法调用）、Socket编程或使用某些分布式对象缓存框架时。
2. **持久化到磁盘**：如果你需要将对象的状态保存到文件中，稍后再恢复这些状态，那么这个对象同样需要实现`Serializable`接口。这在实现某些持久化机制时很常见。
3. **分布式系统**：在分布式系统中，比如使用了EJB或Spring分布式/远程服务，将对象从一个应用传输到另一个应用中时，那些作为参数或返回值的对象应该是可序列化的。

DO类是否需要实现`Serializable`，主要取决于你是否需要上述某种形式的序列化。如果DO类仅仅在同一个JVM内使用，或者不需要通过文件、网络等方式持久化或传输，那么不必强制让DO类实现`Serializable`接口。然而，在企业应用中，为了保证未来可能的需求（例如分布式部署），实现`Serializable`接口是一种常见的做法。此外，在使用一些框架时（例如Hibernate），某些情况下也可能要求你的DO类实现`Serializable`接口，以支持某些特性，如分布式缓存。

总之，是否让DO类实现`Serializable`应基于具体需求做考虑，但从一开始就实现这一接口也无伤大雅，可以为将来可能的需求预留空间。
