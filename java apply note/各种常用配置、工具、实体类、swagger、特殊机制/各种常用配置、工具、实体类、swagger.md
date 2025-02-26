# 各种常用配置、工具、实体类、swagger



## 8 实用工具类

### 8.1BeanCopyUtil

**注意：** 结果实体类要有get,set方法并且属性的类型与名称要与源类相同才能够拷贝成功，Boolean类型拷贝不了，要boolean类型

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



注意：

参考：https://blog.csdn.net/weixin_34043301/article/details/91753467

![image-20240929150142120](E:\alwaysUse\notes\myNotes\java apply note\各种常用配置、工具、实体类、swagger、特殊机制\各种常用配置、工具、实体类、swagger.assets\image-20240929150142120.png)

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

#### 配置跨域处理

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
}
~~~

坑：

参考：https://blog.csdn.net/qq_39136661/article/details/119580081

现像：post请求不能够通过跨域，但是get请求可以

原因：没有配置允许携带所有的请求头进行跨域

~~~java
			// 设置允许的header属性
                .allowedHeaders("*")
~~~

#### FastJson:

~~~java
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
~~~







#### Jackson的使用

**转换日期格式**

~~~java
public class UpdateDeviceDTO {

    private Integer deviceType;
    private String deviceModel;
    private String deviceNumber;
    private Integer status;
    private String bedNumber;
    private Long facilityId;

    @NotNull(message = "設備id不能為空")
    private Integer deviceId;
    @JsonFormat(shape = JsonFormat.Shape.STRING, pattern = "yyyy-MM-dd", timezone = "UTC")
    private Date activationDate;
}
~~~

解释：

~~~
在Java开发中，@JsonFormat注解是Jackson库提供的一个功能，用于控制日期和时间值如何序列化（转换成JSON格式）和反序列化（从JSON格式解析回日期或时间对象）。当你在一个数据传输对象（DTO）类中使用此注解时，它可以帮助确保日期和时间数据在前后端之间的传输是一致且符合预期的格式。

@JsonFormat注解的属性如下所述：

shape: 指定如何处理日期值。例如，JsonFormat.Shape.STRING表明日期值应被处理为文本格式（即字符串）。
pattern: 定义日期和时间的具体格式。在你的例子中，"yyyy-MM-dd"表示日期应该以年-月-日的格式显示。这是一种常见的国际化日期格式，符合ISO 8601标准。
timezone: 指定用于日期和时间计算的时区。在你的例子中使用了"UTC"，这意味着所有日期时间都会基于协调世界时（UTC）来处理，无论服务器或客户端的本地时区如何。
~~~

上面这个例子中，前端直接这样发起请求即可

~~~json
{
    "activationDate": "2024-04-30"
}
~~~

或者直接：

如何接受字符串的日期参数，并转换为日期Date类：

~~~java
@Data
public class DeviceExceptionDTO extends PageQuery{
    
    @ApiModelProperty(value = "异常发生时间", example = "2024年2月4日10:01:56",dataType = "Date")
    @JsonFormat(pattern = DateUtils.DATE_TIME_PATTERN_CHINESE)
    private Date occurTime;

    @ApiModelProperty(value = "异常处理时间", example = "2024年2月4日12:01:56",dataType = "Date")
    @JsonFormat(pattern = DateUtils.DATE_TIME_PATTERN_CHINESE)
    private Date handlingTime;

}
~~~

其中：

~~~java
    /**
     * 中文日期格式 2024年2月4日10:01:56
     */
    public final static String DATE_TIME_PATTERN_CHINESE = "yyyy年MM月dd日HH:mm:ss";
~~~





**忽略非空字段**：

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
    @ApiModelProperty(value = "验证码", example = "999818",dataType = "String",notes = "评论类型（0代表文章评论，1代表友链评论）", required = true)
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

### 12.3导出excel文件(EasyExcel)

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



### 12.4 poi导出Excel

#### 设置行宽

https://blog.csdn.net/lipinganq/article/details/78090553

![image-20241019095139805](各种常用配置、工具、实体类、swagger.assets/image-20241019095139805.png)

代码设置为25个字符的宽度，在导出的Excel中显示的宽度为24.50(201像素)



#### 设置单元格的样式

参考：https://blog.csdn.net/java_ying/article/details/83546479



#### 例子：

~~~java
@Test
    public void contextLoads() throws IOException {
        // 模拟导出的数据
        List<ExportTravelExcelVO> data = new ArrayList<>(Arrays.asList(
                new ExportTravelExcelVO("火车", "北京", "上海", "2024/7/22 08:02:00", "2024/7/22 09:02:00", "500.00", 2),
                new ExportTravelExcelVO("飞机", "广州", "成都", "2024/7/23 10:00:00", "2024/7/23 12:30:00", "1200.00", 1)
        ));

        // 创建工作簿
        try(XSSFWorkbook workbook = new XSSFWorkbook()){
            // 创建Sheet并命名为"交通费"
            XSSFSheet sheet = workbook.createSheet("交通费");


            // 创建表头样式，居中加粗
            XSSFCellStyle headerStyle = workbook.createCellStyle();
            Font font = workbook.createFont();
            font.setBold(true); // 加粗
            //字体 默认为等线
            font.setFontName("Calibri");
            //大小 默认为11
            font.setFontHeightInPoints((short)10);
            headerStyle.setFont(font);
            headerStyle.setAlignment(HorizontalAlignment.CENTER); // 水平居中
            headerStyle.setVerticalAlignment(VerticalAlignment.CENTER); // 垂直居中

            // 创建第一行的样式
            XSSFCellStyle firstRowStyle = workbook.createCellStyle();
            XSSFFont firstRowFont = workbook.createFont();
            firstRowFont.setBold(true);
            //字体
            firstRowFont.setFontName("Calibri");
            //大小
            firstRowFont.setFontHeightInPoints((short)14);
            firstRowStyle.setFont(firstRowFont);
            firstRowStyle.setAlignment(HorizontalAlignment.CENTER); // 水平居中
            firstRowStyle.setVerticalAlignment(VerticalAlignment.CENTER); // 垂直居中


            // 第一行：合并单元格，显示"交通费"
            Row titleRow = sheet.createRow(0);
            Cell titleCell = titleRow.createCell(0);
            titleCell.setCellValue("交通费");
            titleCell.setCellStyle(firstRowStyle);
            // 合并单元格，合并的列数等于实体类属性的数量（7个）
            String[] headers = {"交通费用明细", "出发地点", "到达地点", "出发时间", "到达时间", "交通费金额", "交通费附单据数"};
            sheet.addMergedRegion(new CellRangeAddress(0, 0, 0, headers.length - 1));

            // 第二行：表头
            Row headerRow = sheet.createRow(1);
            for (int i = 0; i < headers.length; i++) {
                Cell headerCell = headerRow.createCell(i);
                headerCell.setCellValue(headers[i]);
                headerCell.setCellStyle(headerStyle);
            }

            // 创建自定义格式的CellStyle
            CellStyle dateCellStyle = workbook.createCellStyle();
            DataFormat dataFormat = workbook.createDataFormat();
            // 设置自定义格式 yyyy/m/d h:mm
            dateCellStyle.setDataFormat(dataFormat.getFormat("yyyy/m/d h:mm"));

            // 填充数据，从第三行开始
            int rowIndex = 2;
            for (ExportTravelExcelVO vo : data) {
                Row row = sheet.createRow(rowIndex++);
                row.createCell(0).setCellValue(vo.getExpenseType());
                row.createCell(1).setCellValue(vo.getStartLocation());
                row.createCell(2).setCellValue(vo.getArrivalLocation());

                // 设置自定义格式 yyyy/m/d h:mm
                Cell cell3 = row.createCell(3);
                cell3.setCellStyle(dateCellStyle);
                cell3.setCellValue(new Date());


                Cell cell4 = row.createCell(4);
                cell4.setCellStyle(dateCellStyle);
                cell4.setCellValue(new Date());

                row.createCell(5).setCellValue(vo.getTotalMoneyDesc());
                row.createCell(6).setCellValue(vo.getInvoiceCount());
            }

            // 设置列宽为指定字符的宽度
            sheet.setColumnWidth(0, 14 * 256);
            sheet.setColumnWidth(1, 18 * 256);
            sheet.setColumnWidth(2, 10 * 256);
            // 出发时间
            sheet.setColumnWidth(3, 18 * 256);
            // 到达时间
            sheet.setColumnWidth(4, 18 * 256);
            sheet.setColumnWidth(5, 11 * 256);
            // 交通费附单据数
            sheet.setColumnWidth(6, 15 * 256);

            // 导出Excel
            try (FileOutputStream fileOut = new FileOutputStream("交通费.xlsx")) {
                workbook.write(fileOut);
                System.out.println("Excel 文件已导出！");
            } catch (IOException e) {
                e.printStackTrace();
            }
        }

    }
~~~



### 12.5 poi导出word文件



#### 基本使用：

~~~java
@RunWith(SpringRunner.class)
@SpringBootTest
public class OaexApplicationTests {

//    @Value("${bjtds.files.save-path}")
    private String filesSavePath = "D:/guangtie/sr-root/static-resources/files";

    @Test
    public void contextLoads() throws IOException, InvalidFormatException {
        // 创建一个空白的文档
        try(XWPFDocument document = new XWPFDocument()) {

            // 添加发票图片
            String invoiceImagePath = this.filesSavePath + File.separator + "1803" + File.separator + "北京到武汉.png"; // 发票图片路径
            insertImage(document, invoiceImagePath);

            // 添加发票说明
            XWPFParagraph descriptionParagraph = document.createParagraph();
            setCompactStyle(descriptionParagraph);
            XWPFRun descriptionRun = descriptionParagraph.createRun();
            descriptionRun.addBreak();
            descriptionRun.setText("说明：");
            descriptionRun.setBold(true);
            // 添加一个制表符，让文字在左侧排列而不换行
//            descriptionRun.addTab();

            XWPFParagraph invoiceDescContentParagraph = document.createParagraph();
            setCompactStyle(invoiceDescContentParagraph);
            XWPFRun invoiceDescContentText = invoiceDescContentParagraph.createRun();
            invoiceDescContentText.setText("杨宋杰报销差旅"); // 发票说明文字内容
            // 插入一个换行符 (<w:br> 标签)
            invoiceDescContentText.addBreak();

            // 添加附件图片
            String attachmentImagePath = this.filesSavePath + File.separator + "1803" + File.separator + "北京到武汉.png"; // 附件图片路径
            XWPFParagraph attachmentParagraph = document.createParagraph();
            setCompactStyle(attachmentParagraph);
            XWPFRun attachmentRun = attachmentParagraph.createRun();
            attachmentRun.setText("附件：");
            attachmentRun.setBold(true);
            attachmentRun.addTab();
            insertImage(document, attachmentImagePath);

            // 将文档写入文件
            try (FileOutputStream out = new FileOutputStream("InvoiceDetails.docx")) {
                document.write(out);
            }

        }

    }

    // 插入图片的通用方法
    private static void insertImage(XWPFDocument document, String imagePath) throws IOException, InvalidFormatException {
        // 创建一个新的段落 (<w:p> 标签),段落是 Word 文档中的基本文本单元，可以包含文字、图片、超链接等。
        XWPFParagraph paragraph = document.createParagraph();
        // 创建一个片段
        XWPFRun run = paragraph.createRun();

        try (FileInputStream fis = new FileInputStream(imagePath)) {
            // 图片大小
            run.addPicture(fis, XWPFDocument.PICTURE_TYPE_PNG, imagePath, Units.toEMU(400), Units.toEMU(300));
        }
    }

    /**
     * 给word的段落设置紧凑的段落样式
     * @param paragraph
     */
    private static void setCompactStyle(XWPFParagraph paragraph) {
        // 设置段落的上下间距为 0，减少段落之间的空隙
        paragraph.setSpacingBefore(0);           // 段前间距
        paragraph.setSpacingAfter(0);            // 段后间距
        paragraph.setSpacingBetween(1.0);        // 行间距（1.0 表示单倍行距）
        paragraph.setAlignment(ParagraphAlignment.LEFT); // 左对齐
    }

}
~~~



#### 工具类：

~~~java
import org.apache.poi.xwpf.usermodel.XWPFDocument;
import org.apache.poi.xwpf.usermodel.XWPFStyle;
import org.apache.poi.xwpf.usermodel.XWPFStyles;
import org.openxmlformats.schemas.wordprocessingml.x2006.main.*;

import javax.xml.bind.annotation.adapters.HexBinaryAdapter;
import java.math.BigInteger;

public class DocUtils {

    /**
     *
     * @param doc
     * @param styles
     * @param strStyleId	标题id
     * @param headingLevel	标题级别
     * @param pointSize	字体大小（相当于word中的字体大小除于2）
     * @param hexColor	字体颜色
     * @param typefaceName	字体名称（默认宋体）
     */
    public static void createHeadingStyle(XWPFDocument doc, XWPFStyles styles, String strStyleId,
                                          int headingLevel, int pointSize, String hexColor, String typefaceName) {
        //创建样式
        CTStyle ctStyle = CTStyle.Factory.newInstance();
        //设置id
        ctStyle.setStyleId(strStyleId);

        CTString styleName = CTString.Factory.newInstance();
        styleName.setVal(strStyleId);
        ctStyle.setName(styleName);

        CTDecimalNumber indentNumber = CTDecimalNumber.Factory.newInstance();
        indentNumber.setVal(BigInteger.valueOf(headingLevel));

        // 数字越低在格式栏中越突出
        ctStyle.setUiPriority(indentNumber);

        CTOnOff onoffnull = CTOnOff.Factory.newInstance();
        ctStyle.setUnhideWhenUsed(onoffnull);

        // 样式将显示在“格式”栏中
        ctStyle.setQFormat(onoffnull);

        // 样式定义给定级别的标题
        CTPPr ppr = CTPPr.Factory.newInstance();
        ppr.setOutlineLvl(indentNumber);
        ctStyle.setPPr(ppr);

        XWPFStyle style = new XWPFStyle(ctStyle);

        CTHpsMeasure size = CTHpsMeasure.Factory.newInstance();
        size.setVal(new BigInteger(String.valueOf(pointSize)));
        CTHpsMeasure size2 = CTHpsMeasure.Factory.newInstance();
        size2.setVal(new BigInteger(String.valueOf(pointSize)));

        CTFonts fonts = CTFonts.Factory.newInstance();
        if(typefaceName == null || typefaceName.equals("")) typefaceName = "宋体";
        fonts.setAscii(typefaceName);	//字体

        CTRPr rpr = CTRPr.Factory.newInstance();
        rpr.setRFonts(fonts);
        rpr.setSz(size);
        rpr.setSzCs(size2);	//字体大小

        CTColor color=CTColor.Factory.newInstance();
        color.setVal(hexToBytes(hexColor));
        rpr.setColor(color);	//字体颜色
        style.getCTStyle().setRPr(rpr);
        // is a null op if already defined

        style.setType(STStyleType.PARAGRAPH);
        styles.addStyle(style);
    }

    public static byte[] hexToBytes(String hexString) {
        HexBinaryAdapter adapter = new HexBinaryAdapter();
        byte[] bytes = adapter.unmarshal(hexString);
        return bytes;
    }

}
~~~





#### 设置标题

参考：https://blog.csdn.net/qq_37945565/article/details/121518330

~~~java
import com.bjtds.oaex.util.DocUtils;
import org.apache.poi.xwpf.usermodel.XWPFDocument;
import org.apache.poi.xwpf.usermodel.XWPFParagraph;
import org.apache.poi.xwpf.usermodel.XWPFRun;
import org.apache.poi.xwpf.usermodel.XWPFStyles;
import org.apache.xmlbeans.XmlException;
import org.junit.Test;
import org.junit.runner.RunWith;
import org.openxmlformats.schemas.wordprocessingml.x2006.main.CTStyles;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.test.context.junit4.SpringRunner;

import java.io.FileInputStream;
import java.io.FileOutputStream;
import java.io.IOException;

@RunWith(SpringRunner.class)
@SpringBootTest
public class OaexApplicationTests {

    private String filesSavePath = "D:/guangtie/sr-root/static-resources/files";


    @Test
    public void contextLoads() throws IOException, XmlException {
        // 新建的word文档对象
        try (XWPFDocument doc = new XWPFDocument()) {
            // 获取新建文档对象的样式
            XWPFStyles newStyles = doc.createStyles();
            String heading2Id = "My Heading 2";
            DocUtils.createHeadingStyle(doc, newStyles, heading2Id, 2, 32, "000000", "宋体");
            XWPFParagraph para1 = doc.createParagraph();
            para1.setStyle("My Heading 2");
            XWPFRun run1 = para1.createRun();
            run1.setText("发票说明2");
            // word写入到文件
            try (FileOutputStream fos = new FileOutputStream("./twoLevelTitleRes.docx")) {
                doc.write(fos);
            }
        }


    }

    public void getTempleDocStyle() throws IOException, XmlException {
        // 新建的word文档对象
        try (XWPFDocument doc = new XWPFDocument()) {
            // word整体样式
            // 读取模板文档
            XWPFDocument template = new XWPFDocument(new FileInputStream("C:\\Users\\Gloom\\alwaysUse\\BTS\\demo\\oaex-sys\\oaex-server\\testTitle.docx"));
            // 获得模板文档的整体样式
            CTStyles templateStyle = template.getStyle();
            // 获取新建文档对象的样式
            XWPFStyles newStyles = doc.createStyles();
            // 关键行// 修改设置文档样式为静态块中读取到的样式
            newStyles.setStyles(templateStyle);

            // 标题1，1级大纲
            XWPFParagraph para1 = doc.createParagraph();
            // 关键行// 1级大纲
            para1.setStyle("1");
            XWPFRun run1 = para1.createRun();
            // 标题内容
            run1.setText("标题1");

            // 标题2，2级大纲
            XWPFParagraph para2 = doc.createParagraph();
            // 关键行// 2级大纲
            para2.setStyle("2");
            XWPFRun run2 = para2.createRun();
            // 标题内容
            run2.setText("标题2");

            // 正文
            XWPFParagraph paraX = doc.createParagraph();
            XWPFRun runX = paraX.createRun();
            for (int i = 0; i < 100; i++) {
                // 正文内容
                runX.setText("正文\r\n");
            }

            // word写入到文件
            try (FileOutputStream fos = new FileOutputStream("./titleRes.docx")) {
                doc.write(fos);
            }
        }
    }


}
~~~





## 2级 使用MapStruct完成Bean拷贝

参考：https://juejin.cn/post/6956190395319451679

#### 基本使用

基础知识：

1. 与使用BeanCopyUtils类似，但是BeanCopyUtils是**对属性的数据类型、名称相同时才会自动进行映射**，而MapStruct对**即使数据类型不一致的如：(source为String类型,target为Integer类型)，也会进行拷贝**，会默认把字符串转换为数字，如果这个字符串的内容不是数字则会报错。
2. MapStruct想要进行映射的可以手动进行指定，MapStruct底层就是调用了set方法进行赋值，效率比BeanCopyUtils使用反射快。

数据类型、名称相同时：

~~~java
    @Override
    public FacilityDO updateFacilityDetailsContext2FacilityDO(UpdateFacilityDetailsContext updateFacilityDetailsContext) {
        if ( updateFacilityDetailsContext == null ) {
            return null;
        }

        FacilityDO facilityDO = new FacilityDO();
		// 数据类型、名称相同时
        facilityDO.setFacilityId( updateFacilityDetailsContext.getFacilityId() );
        facilityDO.setFloorCount( updateFacilityDetailsContext.getFloorCount() );
        // 数据类型不同，名称相同时
        if ( updateFacilityDetailsContext.getZoneExample() != null ) {
            facilityDO.setZoneExample( Integer.parseInt( updateFacilityDetailsContext.getZoneExample() ) );
        }
        facilityDO.setZoneCount( updateFacilityDetailsContext.getZoneCount() );
        facilityDO.setFacilityArea( updateFacilityDetailsContext.getFacilityArea() );
        facilityDO.setFloorAreaUniform( updateFacilityDetailsContext.getFloorAreaUniform() );
        if ( updateFacilityDetailsContext.getUsageRules() != null ) {
            facilityDO.setUsageRules( Integer.parseInt( updateFacilityDetailsContext.getUsageRules() ) );
        }

        return facilityDO;
    }
~~~



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

编译相关配置：

~~~xml
   <build>
        <plugins>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <configuration>
                    <source>16</source>
                    <target>16</target>
                    <annotationProcessorPaths>
                        <path>
                            <groupId>org.mapstruct</groupId>
                            <artifactId>mapstruct-processor</artifactId>
                            <version>${org.mapstruct.version}</version>
                        </path>
                    </annotationProcessorPaths>
                </configuration>
            </plugin>
        </plugins>
    </build>

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

后面使用时就用下面的方式来调用：

~~~java
@Autowired
private MsSampleMapper msSampleMapper
    
    msSampleMapper.sampleToSampleDto();
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

后面使用时就用下面的方式来调用

~~~java
FacilityDOConverter.INSTANCE.doMapToDTO()
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

#### 忽略某个属性

~~~java
   /**
     * UserRegisterContext转RPanUser,忽略password字段
     * @param userRegisterContext
     * @return
     */
    @Mapping(target = "password",ignore = true)
    RPanUser UserRegisterContext2RPanUser(UserRegisterContext userRegisterContext);
~~~



#### 执行Java代码：

~~~java
@Mapper(nullValuePropertyMappingStrategy = NullValuePropertyMappingStrategy.IGNORE) // 忽略null值
public interface BedDOConverter extends BaseConverter<BedVO, BedsDO> {

    BedDOConverter INSTANCE = Mappers.getMapper(BedDOConverter.class);

    @Mapping(target = "bedTypeDesc", expression = "java(" +
            "com.fa.common.utils.EnumUtils.getEnumDescByValue(" +
            "bed.getBedType(), " +
            "com.fa.modules.common.constant.bed.BedTypeEnum.class))")
    @Mapping(target = "statusDesc", expression = "java(" +
            "com.fa.common.utils.EnumUtils.getEnumDescByValue(" +
            "bed.getStatus(), " +
            "com.fa.modules.common.constant.bed.BedStatusEnum.class))")
    @Mapping(target = "floorNumber", expression = "java(" +
            "bed.getFloorId().toString())")
    BedVO doMapToDTO(BedsDO bed);
}
~~~



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

注意：在继承的父类中使用会失效

DTO:

~~~java
public class DeviceExceptionDTO extends PageQuery{
	// s...
}

~~~



父类：

~~~java
@Data
@ApiModel("通用分页查询")
public class PageQuery extends BaseQuery {

    @ApiModelProperty(value = "每页数量， 默认10", dataType = "Integer")
    @Min(value = 1,message = "每頁數量最少爲1")
    private Integer pageSize = 10;// 失效

    @ApiModelProperty(value = "页码，默认1", dataType = "Integer")
    @Min(value = 1,message = "頁碼最少爲1")
    private Integer pageNum = 1;
}
~~~



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

##### @Pattern 使用正则表达式

~~~java
    @NotBlank(message = "用户名不能为空")
    @Pattern(regexp = "^[0-9a-zA-Z]{6,16}$",message = "用户名必须由6-16位数字与字母组成") // 使用正则表达式
    private String username;
~~~

或

~~~java
@Data
@ApiModel("创建院舍用户请求")
public class AddFacilityUserRequestDTO extends BaseDTO {
    @ApiModelProperty(value = "E-mail", dataType = "String", required = true)
    @NotBlank(message = "E-mail不能爲空")
    @Email(message = "請輸入正確的E-mail")
    private String email;
}
~~~

自己使用正则表达式：

~~~java
            // 正则表达式校验日期格式
		   Pattern compile = Pattern.compile(DateUtils.CHINESE_DATE_FORMAT_REGULAR_EXPRESSION);
            Matcher matcher = compile.matcher(occurTimeDesc);
            if(!matcher.matches()){
                throw new RRException("輸入時間的格式必須類似於：" + nowDesc);
            }
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

#### 3.18.3 铺垫知识

##### 3.18.3.1 CommandLineRunner实现项目启动时预处理

​	如果希望在SpringBoot应用启动时进行一些初始化操作可以选择使用CommandLineRunner来进行处理。

​	我们只需要实现CommandLineRunner接口，并且把对应的bean注入容器。把相关初始化的代码重新到需要重新的方法中。

​	这样就会在应用启动的时候执行对应的代码。

~~~~java
@Component
public class TestRunner implements CommandLineRunner {
    @Override
    public void run(String... args) throws Exception {
        System.out.println("程序初始化");
    }
}

~~~~



##### 3.18.3.2 定时任务

​	定时任务的实现方式有很多，比如XXL-Job等。但是其实核心功能和概念都是类似的，很多情况下只是调用的API不同而已。

​	这里就先用SpringBoot为我们提供的定时任务的API来实现一个简单的定时任务，让大家先对定时任务里面的一些核心概念有个大致的了解。

实现步骤

① 使用@EnableScheduling注解开启定时任务功能

​	我们可以在配置类上加上@EnableScheduling

~~~~java
@SpringBootApplication
@MapperScan("com.sangeng.mapper")
@EnableScheduling
public class SanGengBlogApplication {
    public static void main(String[] args) {
        SpringApplication.run(SanGengBlogApplication.class,args);
    }
}
~~~~

② 确定定时任务执行代码，并配置任务执行时间

​	使用@Scheduled注解标识需要定时执行的代码。注解的cron属性相当于是任务的执行时间。目前可以使用 0/5 * * * * ? 进行测试，代表从0秒开始，每隔5秒执行一次。 

​	注意：对应的bean要注入容器，否则不会生效。

~~~~java
@Component
public class TestJob {

    @Scheduled(cron = "0/5 * * * * ?")
    public void testJob(){
        //要执行的代码
        System.out.println("定时任务执行了");
    }
}

~~~~



###### 3.18.3.2.1 cron 表达式语法

​	cron表达式是用来设置定时任务执行时间的表达式。

​	很多情况下我们可以用 ： [在线Cron表达式生成器](https://www.bejson.com/othertools/cron/) 来帮助我们理解cron表达式和书写cron表达式。

​	但是我们还是有需要学习对应的Cron语法的，这样可以更有利于我们书写Cron表达式。



如上我们用到的 0/5 * * * * ? *，cron表达式由七部分组成，中间由空格分隔，这七部分从左往右依次是：

秒（0~59），分钟（0~59），小时（	0~23），日期（1-月最后一天），月份（1-12），星期几（1-7,1表示星期日），年份（一般该项不设置，直接忽略掉，即可为空值）



通用特殊字符：, - * /  (可以在任意部分使用)

> *

星号表示任意值，例如：

```
* * * * * ?
```

表示 “ 每年每月每天每时每分每秒 ” 。



> ,   

可以用来定义列表，例如 ：  

```
1,2,3 * * * * ?
```

表示 “ 每年每月每天每时每分的每个第1秒，第2秒，第3秒 ” 。



> -

定义范围，例如：

```
1-3 * * * * ?
```

表示 “ 每年每月每天每时每分的第1秒至第3秒 ”。



> /

每隔多少，例如

```
5/10 * * * * ?
```

表示 “ 每年每月每天每时每分，从第5秒开始，每10秒一次 ” 。即 “ / ” 的左侧是开始值，右侧是间隔。如果是从 “ 0 ” 开始的话，也可以简写成 “ /10 ” 









日期部分还可允许特殊字符： ? L W

星期部分还可允许的特殊字符: ? L # 



> ?

只可用在日期和星期部分。表示没有具体的值，使用?要注意冲突。日期和星期两个部分如果其中一个部分设置了值，则另一个必须设置为 “ ? ”。

例如：

~~~~
0\* * * 2 * ?
 和
0\* * * ? * 2
~~~~

同时使用?和同时不使用?都是不对的

例如下面写法就是错的

~~~~
* * * 2 * 2
 和
* * * ? * ?

~~~~





> W

只能用在日期中，表示当月中最接近某天的工作日

```
0 0 0 31W * ?
```

表示最接近31号的工作日，如果31号是星期六，则表示30号，即星期五，如果31号是星期天，则表示29号，即星期五。如果31号是星期三，则表示31号本身，即星期三。









> L

表示最后（Last）,只能用在日期和星期中



在日期中表示每月最后一天，在一月份中表示31号，在六月份中表示30号

也可以表示每月倒是第N天。例如： L-2表示每个月的倒数第2天



 0 0 0 LW * ?
 LW可以连起来用，表示每月最后一个工作日，即每月最后一个星期五



在星期中表示7即星期六


~~~~
0 0 0 ? * L
表示每个星期六
0 0 0 ? * 6L
若前面有其他值的话，则表示最后一个星期几，即每月的最后一个星期五
~~~~






> # 

只能用在星期中，表示第几个星期几

~~~~
0 0 0 ? * 6#3
表示每个月的第三个星期五。
~~~~







## 其他

#### implements Serializable的意义

gpt4回答：

在Java中，`implements Serializable`是一个标记接口，表示类的对象可以被序列化。对象序列化是将对象的状态转换为可以被存储或传输的格式（如存储到文件中，或通过网络传输到另一台机器上）的过程。实现了`Serializable`接口的类的对象可以被`ObjectOutputStream` 类序列化，而对象的反序列化则是通过`ObjectInputStream`类完成的。

以下是一些常见场景，你可能需要让你的DO（Data Object）类实现`Serializable`接口：

1. **网络传输**：如果你需要通过网络从一个JVM传输对象到另一个JVM，那么这个对象需要是可序列化的。例如，在RMI（远程方法调用）、Socket编程或使用某些分布式对象缓存框架时。
2. **持久化到磁盘**：如果你需要将对象的状态保存到文件中，稍后再恢复这些状态，那么这个对象同样需要实现`Serializable`接口。这在实现某些持久化机制时很常见。
3. **分布式系统**：在分布式系统中，比如使用了EJB或Spring分布式/远程服务，将对象从一个应用传输到另一个应用中时，那些作为参数或返回值的对象应该是可序列化的。

DO类是否需要实现`Serializable`，主要取决于你是否需要上述某种形式的序列化。如果DO类仅仅在同一个JVM内使用，或者不需要通过文件、网络等方式持久化或传输，那么不必强制让DO类实现`Serializable`接口。然而，在企业应用中，为了保证未来可能的需求（例如分布式部署），实现`Serializable`接口是一种常见的做法。此外，在使用一些框架时（例如Hibernate），某些情况下也可能要求你的DO类实现`Serializable`接口，以支持某些特性，如分布式缓存。

总之，是否让DO类实现`Serializable`应基于具体需求做考虑，但从一开始就实现这一接口也无伤大雅，可以为将来可能的需求预留空间。



### PostMan的使用

配置全局的环境变量：

https://blog.csdn.net/qq_41187577/article/details/110222262

发起**Path请求参数**：在url路径中使用：`/:userId`即可，下面会自动出现Path Variables

**上传文件**：www-form-data中 选择file



### Date类的使用：

在JDK17中使用`new Date("2024-12-22 23:23:12")`的话，**运行时会直接报错了**，已经**被弃用了**。

实在要使用的话，可以使用SimpleDateFormat类来创建：

~~~java
        SimpleDateFormat simpleDateFormat = new SimpleDateFormat(DateUtils.TIME_DATE__PATTERN);
        // 添加默认的離床有效時間段
        BedLeaveRulesDO bedLeaveRulesDO = new BedLeaveRulesDO();
        try {
            bedLeaveRulesDO.setStartTime(simpleDateFormat.parse(RuleConstant.DEFAULT_BED_LEAVE_START_TIME));
            bedLeaveRulesDO.setEndTime(simpleDateFormat.parse(RuleConstant.DEFAULT_BED_LEAVE_END_TIME));
        } catch (ParseException e) {
            throw new RuntimeException(e);
        }
~~~



默认的日期格式化方式：如：2024-12-22 23:23:12



#### 如何进行Date比较

参考：https://blog.csdn.net/hanxiaotongtong/article/details/107588694

`java.util.Date`提供了在Java中比较两个日期的经典方法compareTo（）。

1. 如果两个日期相等，则返回值为0。
2. 如果方法调用的date本身比date参数大，则返回值大于0。
3. 如果方法调用的date本身比date参数小，则返回值小于0。

~~~java
@Test
void testDateCompare() throws ParseException {
  SimpleDateFormat sdf = new SimpleDateFormat("yyyy-MM-dd");
  Date date1 = sdf.parse("2009-12-31");
  Date date2 = sdf.parse("2019-01-31");

  System.out.println("date1 : " + sdf.format(date1));
  System.out.println("date2 : " + sdf.format(date2));

  if (date1.compareTo(date2) > 0) {
    System.out.println("Date1 时间在 Date2 之后");
  } else if (date1.compareTo(date2) < 0) {
    System.out.println("Date1 时间在 Date2 之前");
  } else if (date1.compareTo(date2) == 0) {
    System.out.println("Date1 时间与 Date2 相等");
  } else {
    System.out.println("程序怎么会运行到这里?正常应该不会");
  }
}

~~~

##### 例子：

~~~java
    public List<Map<String,Object>> getUserAttendance(String userId, String startTime, String endTime) throws Exception {
        StringBuilder sql = new StringBuilder();
        List<Map<String,Object>> attendanceList = this.findAll(sql.toString());
        attendanceList.forEach(map -> {
            // 哪一天的日志
            Date logDate = (Date) map.get("date");
            // 什么时候发的这个日志
            Date sendLogDatetime = (Date) map.get("send_log_datetime");
            // 提取年月日
            SimpleDateFormat dateFormat = new SimpleDateFormat("yyyy-MM-dd");
            Date sendLogDate = null;
            try {
                sendLogDate = dateFormat.parse(dateFormat.format(sendLogDatetime));
            } catch (ParseException e) {
                log.error("dateFormat.parse(dateFormat.format(sendLogDatetime))方法错误");
                throw new RuntimeException(e);
            }
            // 添加日志状态
            // 是同年同月同天发送的
            if(sendLogDate.compareTo(logDate) == 0){
                // 定义22:00:00的时间
                SimpleDateFormat timeFormat = new SimpleDateFormat("HH:mm:ss");
                try {
                    Date nightTenTime = timeFormat.parse("22:00:00");
                    // 提取Date对象中的时间部分
                    String timeString = timeFormat.format(sendLogDatetime);
                    Date send_log_time = timeFormat.parse(timeString);
                    // 正常发：sendLogDatetime <= 当天的22:00:00
                    if(send_log_time.compareTo(nightTenTime) <= 0){
                        // 正常发
                        map.put("send_log_state", SendLogState.NORMAL.getEnumDesc());
                    } else{
                        // 晚发
                        map.put("send_log_state", SendLogState.LATE_SEND.getEnumDesc());
                    }
                } catch (ParseException e) {
                    log.error("timeFormat.parse(timeString)方法错误");
                    throw new RuntimeException(e);
                }
            }else{
                // sendLogDate > logDate
                if(sendLogDate.compareTo(logDate) > 0){
                    // 补发
                    map.put("send_log_state",SendLogState.SUPPLEMENT_SEND.getEnumDesc());
                }else{
                    throw new RuntimeException(" send_log_state < log_date,有人提前几天写了当天的日报");
                }
            }
        }
        );
        return attendanceList;
    }
~~~



### 处理序列化与反序列化



#### Date与JSON的相互转换

参考：https://xyzghio.xyz/DateTimeFormatAndJsonFormat/



#### （1）反序列化

##### @DateTimeFormat 反序列化

@DateTimeFormat,@JsonFormat都可以

~~~java
class Pc{    
    ...        
    private String name;
    @DateTimeFormat(pattern = "yyyy-MM-dd") // 此为 Spring 框架提供的注解，将 JSON 格式的日期信息信息解析转换并绑定到 Date 对象中，该注解用于 Date 字段即可，同时指定 JSON 日期的格式 (pattern)
    private Date birthday;
    ...
}
~~~



##### @DateTimeFormat 把字符串转换为Date类 queryString请求的情况下正常

~~~java
    /**
     * 条件查询发票文件夹，参数全为null则查询全部发票文件夹
     * @param userId 用户id 一开始默认查询当前用户的发票文件夹信息时使用
     * @param departmentName 部门名称
     * @param userName 用户名
     * @param startTime 开始时间
     * @param endTime 结束时间
     * @param isOver 是否完结
     * @param reimbursementCategory 报销类别
     * @return
     * @throws Exception
     */
    @GetMapping("/getInvoiceFolder")
    public ResultVO<List<InvoiceFolderVO>> getInvoiceFolder(
            @RequestParam(value = "userId",required = false) Long  userId,
            @RequestParam(value = "departmentName",required = false) String  departmentName,
            @RequestParam(value = "userName",required = false) String userName,
            // 把指定时间格式的字符串转换为Date类，前端queryString中不传startTime这个参数时则为null
            @RequestParam(value = "startTime",required = false) @DateTimeFormat(pattern = "yyyy-MM-dd HH:mm:ss") Date startTime,
            @RequestParam(value = "endTime",required = false) @DateTimeFormat(pattern = "yyyy-MM-dd HH:mm:ss") Date endTime,
            @RequestParam(value = "isOver",required = false) Boolean isOver,
            @RequestParam(value = "reimbursementCategory",required = false) Integer reimbursementCategory
    ) throws Exception {
        return submitAccountService.getInvoiceFolder(userId,departmentName,userName,startTime,endTime,isOver,reimbursementCategory);
    }
~~~



**注意：**

使用@RequestBody的DTO类中的日期要从字符串反序列化为Date时，只能使用@JsonFormat

~~~java
@RestController
@RequestMapping("/afterService")
@Slf4j(topic = "AfterServiceController")
public class AfterServiceController {

    @Autowired
    private AfterServiceService afterServiceService;

    @PostMapping("/save")
    public ResultVO<Void> saveAfterServiceData(@RequestBody SaveAfterServiceDTO saveAfterServiceDTO){
        return afterServiceService.saveAfterServiceData(saveAfterServiceDTO);
    }
}

public class SaveAfterServiceDTO {

    /**
     * 故障报修时间
     */
    @JsonFormat(pattern = "yyyy-MM-dd HH:mm", timezone = "GMT+8")
    private Date faultReportTime;

}
~~~



1

~~~~java
@Data
// 默认json中有但是这个dto中没有的话，转换时就会报错，即：必须把整个json的全部字段全部接收，不管有没有用
// 在反序列化时会忽略相应的字段
@JsonIgnoreProperties(ignoreUnknown = true) // 
public class SaveInvoiceDTO implements Serializable {
    // 非实体类中的字段 
    private Integer isFlicker;
	@JsonProperty("passengerName") // 指定对应json的n字段名称
    private String passengerName;

    private Boolean showMergeNum;

    @JsonFormat(pattern = "yyyy-MM-dd", timezone = "GMT+8")
    private Date onlyDateStart;

    @JsonFormat(pattern = "yyyy-MM-dd", timezone = "GMT+8")
}
~~~~



##### @JsonFormat 反序列化

使用阿里的Jackson进行json转对象，用@JsonFramat指定日期格式 默认没有YYYY-MM-dd HH-mm-ss格式,需要手动指定后才可以，就能够把指定日期格式的字符串 反序列化为日期对象

~~~java
class Pc{    
  
    private String name;
    @JsonFormat(timezone = "GMT+8", pattern = "yyyy-MM-dd") // 此为 Jackson 框架提供的注解，将 Date 对象数据解析转换为 JSON 格式，该注解用于 Date 字段即可，同时指定期望 JSON 日期的格式 (pattern)
    @JsonProperty("birthday") // 指定对应json的n字段名称
    private Date birthday;

}
~~~



#### （2）序列化

##### BigDecimal转JSON 序列化

~~~java
	/**
     * 发票金额（总金额）
     * 指定将 BigDecimal 序列化为字符串格式，并保留两位小数。
     */
    @JsonFormat(shape = JsonFormat.Shape.STRING, pattern = "0.00")
    private BigDecimal totalMoney;
~~~









### JDK1.8新特性 日期类LocalDate,LocalDateTime

##### 相关的坑

1

Date类可以直接作为对象保存到数据库中的timestamp类型进行存储，但是LocalDateTime不行，直接存储的话会报错：

~~~java
Caused by: org.apache.ibatis.reflection.ReflectionException: Could not set property 'createTime' of 'class com.fa.modules.dao.OprateLogDO' with value '2024-06-14T19:41:03.156926' Cause: java.lang.IllegalArgumentException: java.lang.ClassCastException@3f9a9764
~~~

2

为什么使用？

```
1 使用LocalDateTime类校验日期格式(如："yyyy年MM月dd日HH:mm:ss")，非常严格，缺少一个前导0都不允许，相当于正则表达式，只有符合格式的才能够使用，而DateTime就不严格了，即使格式为yyyy，写成20还是能够进行转换
2 JDK 1.8 新特性...
```

参考：

https://blog.csdn.net/qq_31635851/article/details/117880835

https://blog.51cto.com/lenglingx/6589259



在Java中，如果你想得到一个表示特定时间（如09:00:00）的`LocalDateTime`对象，你可以使用`LocalDate`和`LocalTime`类结合来创建。这里有一个例子如何创建一个表示上午9点的`LocalDateTime`对象：

```java
import java.time.LocalDate;
import java.time.LocalTime;
import java.time.LocalDateTime;

public class Main {
    public static void main(String[] args) {
        // 获取当前日期
        LocalDate date = LocalDate.now();
        // 创建一个表示09:00:00的时间
        LocalTime time = LocalTime.of(9, 0, 0);
        // 将日期和时间结合，创建LocalDateTime
        LocalDateTime dateTime = LocalDateTime.of(date, time);
        
        System.out.println("LocalDateTime at 09:00:00: " + dateTime);
    }
}
```

在这个代码中，`LocalDate.now()`获取当前的日期，而`LocalTime.of(9, 0, 0)`创建一个代表上午9点整的时间。然后，这两个部分被合并成一个`LocalDateTime`对象。你可以根据需要调整日期和时间的具体值。

#### LocalDateTime、LocalDate、Date的相互转换

参考：https://www.cnblogs.com/CF1314/p/13884530.html#_label1



#### 日期转字符串

LocalDate format() 转换格式
LocalDate 的默认日期格式为 yyyy-MM-dd。

format 方法使用指定的格式化程序格式化日期。

找到它的声明。

```java
String format(DateTimeFormatter formatter) 
```


示例:

```java
import java.time.LocalDate;
import java.time.format.DateTimeFormatter;

public class LocalDateDemo {
  public static void main(String[] args) {
	LocalDate localDate = LocalDate.parse("2018-02-18");
	String formattedDate = localDate.format(DateTimeFormatter.ofPattern("MMM dd, yyyy"));
	System.out.println(formattedDate);
  }
} 
```

输出

```
二月 18, 2018
```



#### 字符串转日期
parse(CharSequence text, DateTimeFormatter formatter)



```java
LocalDate l = LocalDate.parse("2021-01-29");
System.out.println(l); //2021-01-29

LocalDate l1 = LocalDate.parse("2021-11-29", DateTimeFormatter.ofPattern("yyyy-MM-dd"));
System.out.println(l1); //2021-11-29

LocalDate localDate1 = LocalDate.parse("20211129", DateTimeFormatter.ofPattern("yyyyMMdd"));
System.out.println(localDate1); //2021-11-29

DateTimeFormatter dtf = DateTimeFormatter.ofPattern("yyyyMMdd");
System.out.println(l.format(dtf));// 2021-01-29 十位转八位 ==> 20210129

dtf = DateTimeFormatter.ofPattern("yyyy年MM月dd日");
System.out.println(l.format(dtf));// 2021-01-29  ==> 2021年01月29日

dtf = DateTimeFormatter.ofPattern("yyyy/MM/dd");
System.out.println(l.format(dtf));// 2021-01-29  ==> 2021/01/29
```



### 二：LocalDate日期比较


#### 1、boolean isBefore(ChronoLocalDate other)

> 用于检查此LocalDate值是否在给定的ChronoLocalDate(other)之前

```java
	    LocalDateTime occurTime = LocalDate.parse("2024-11-29");
        LocalDateTime handlingTime = LocalDate.parse("2021-11-29");
        LocalDateTime now = LocalDateTime.now();
		// 不能选择当前时间往后的时间
        if(occurTime.isAfter(now) || handlingTime.isAfter(now)){
            throw new RRException("不能選擇當前時間往後的時間");
        }
```

#### 2、boolean isAfter(ChronoLocalDate other)

> 用于检查此LocalDate值是否在给定的ChronoLocalDate(other)之后

```java
LocalDate l = LocalDate.parse("2021-11-29");
System.out.println(l.isAfter(LocalDate.parse("2021-11-28"))); //true
System.out.println(l.isAfter(LocalDate.parse("2021-11-30"))); //false1.2.3.
```

#### 3、boolean isEqual(ChronoLocalDate other)

> 用于检查此LocalDate值是否与给定的ChronoLocalDate(other)相等

```java
LocalDate l = LocalDate.parse("2021-11-29");
System.out.println(l.isEqual(LocalDate.parse("2021-11-28"))); //false
System.out.println(l.isEqual(LocalDate.parse("2021-11-30"))); //false
System.out.println(l.isEqual(l)); //true1.2.3.4.
```

#### 4、int compareTo(ChronoLocalDate other)

> 比较两个日期A.compareTo(B)，若日期相同则返回0；
>
> 若A>B，则返回1；
>
> 若A<B，则返回-1；



```java
LocalDate l = LocalDate.parse("2021-11-29");
System.out.println(l.compareTo(LocalDate.parse("2021-11-28"))); //1
System.out.println(l.compareTo(LocalDate.parse("2021-11-30"))); //-1
System.out.println(l.compareTo(l)); //01.2.3.4.
```





### 实际使用Date与LocalDate的转换

~~~java
    /**
     * 统计在指定时间段内相应报销类别的发票根文件夹数量
     *
     * @param dateNow              当前日期
     * @param reimbursementCategory 报销类别
     * @return 发票根文件夹数量
     */
    private Integer countRootFolderNumber(Date dateNow, Integer reimbursementCategory) {
        try {
            // 获取 dateNow 这个月的第一天 0 点时间
            LocalDateTime startTimeLocal = LocalDateTime.ofInstant(dateNow.toInstant(), ZoneId.systemDefault())
                    .withDayOfMonth(1) // 设置为当月第一天
                    .with(LocalTime.MIN); // 设置为 0 点 0 分 0 秒

            // 获取下个月的第一天 0 点时间
            LocalDateTime endTimeLocal = startTimeLocal.plusMonths(1);

            // 将 LocalDateTime 转换为 Date
            Date startTime = Date.from(startTimeLocal.atZone(ZoneId.systemDefault()).toInstant());
            Date endTime = Date.from(endTimeLocal.atZone(ZoneId.systemDefault()).toInstant());

            // 查询数据库中指定时间段和报销类别的根文件夹数量
            Integer count = invoiceOrFolderDao.countByCreateAtBetweenAndParentIdIsNullAndReimbursementCategory(
                    startTime, endTime, reimbursementCategory);

            // 返回结果，避免空指针
            return count != null ? count : 0;

        } catch (Exception e) {
            // 捕获异常并包装为运行时异常
            throw new RuntimeException("查询根文件夹数量时发生异常", e);
        }
    }
~~~

