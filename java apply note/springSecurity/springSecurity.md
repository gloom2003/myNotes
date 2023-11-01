

# springSecurity实战登录验证jWT方案

## 1登录验证 (从数据库中查询用户信息进行登录验证的流程)

### 1.1:登录验证，登录成功则生成token并把用户信息存入Redis，最后返回token与用户信息给前端

~~~java
@Override
    public ResponseResult login(User user) {
        UsernamePasswordAuthenticationToken authenticationToken =
                new UsernamePasswordAuthenticationToken(user.getUserName(),user.getPassword());
        //authenticationManager.authenticate方法执行流程图中的方法，
        // 自定义后查询数据库中用户信息并且与传入的信息进行加密对比，验证成功则写入Authentication对象返回，否则返回null
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

### 1.2 让SpringSecurity从数据库中查询用户信息进行登录验证

~~~java
@Service
public class UserDetailServiceImpl implements UserDetailsService {
    @Autowired
    private UserMapper userMapper;
    @Autowired
    private MenuService menuService;

    @Override
    public UserDetails loadUserByUsername(String s) throws UsernameNotFoundException {
        //使用mp的mapper查询用户对象
        LambdaQueryWrapper<User> queryWrapper = new LambdaQueryWrapper<>();
        queryWrapper.eq(User::getUserName,s);
        User user = userMapper.selectOne(queryWrapper);
        if(user==null){
            throw new RuntimeException("userMapper.selectOne()结果：用户不存在！");
        }
        //如果是后台用户，才封装用户权限信息
        if(user.getType().equals(SystemConstants.ADMIN)){
            List<String> perms = menuService.selectPermsByUserId(user.getId());
            return new LoginUser(user,perms);
        }

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

## 2添加过滤器



### 2.1:继承OncePerRequestFilter类重写方法实现前端携带token发起请求时后端的验证流程



 自定义过滤器后添加到SpringSecurity中

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
            //没有则说明此接口不需要登录,如果访问了需要登录的接口会被后面的过滤器拒绝，所以直接放行并return即可
            filterChain.doFilter(httpServletRequest,httpServletResponse);
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
            //Redis查询不到信息，过期了
            //返回响应给前端告诉他需要重新登录并return
            ResponseResult result = ResponseResult.errorResult(AppHttpCodeEnum.NEED_LOGIN);
            WebUtils.renderString(httpServletResponse, JSON.toJSONString(result));
            return;
        }
        //把用户信息存储到SecurityContextHolder的authentication中
        //这里需要使用3个参数的重载形式才有super.setAuthenticated(true); 设置已经完成了认证
        UsernamePasswordAuthenticationToken authenticationToken = new
                UsernamePasswordAuthenticationToken(loginUser,null,null);
        SecurityContextHolder.getContext().setAuthentication(authenticationToken);
        //放行
        filterChain.doFilter(httpServletRequest,httpServletResponse);

    }
}

```



### 2.1在SecurityConfigure中把过滤器添加到springSecurity的过滤器链中

~~~java
	@Autowired
    private JwtAuthenticationTokenFilter jwtAuthenticationTokenFilter;
    
		//添加自定义的过滤器到UsernamePasswordAuthenticationFilter之前
        http.addFilterBefore(jwtAuthenticationTokenFilter, UsernamePasswordAuthenticationFilter.class);
        
~~~

## 3退出登录



### 3.1 退出登录接口的实现

~~~java
/***
     *  退出登录功能的实现, 需要请求头中必须携带token才能正常访问 为什么呢？？
     *  回答：因为没有携带token的话根本通过不了JwtAuthenticationTokenFilter过滤器，相当于没有登录，正常功能不能被访问，
     *  包括退出登录接口
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

### 3.2 在SecurityConfigure中关闭默认的退出登录接口

~~~java
//关闭默认的退出登录接口，请求地址也是"/logout",防止与自定义的注销接口冲突
        http.logout().disable();
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
                // 对于登录接口 只能匿名访问
                .antMatchers("/user/login").anonymous()
                // 除上面外的所有请求全部都需要认证才可访问
                .anyRequest().authenticated();
        //关闭默认的退出登录接口，请求地址也是"/logout",防止与自定义的注销接口冲突
        http.logout().disable();
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

## 6全局异常处理器Controller,统一处理各种各样的异常

### 6.1全局异常处理器

~~~java
//@RestBody与@ControllerAdvice的合体
@Slf4j
@RestControllerAdvice
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
     * 处理其他异常? Exception,设置返回给前端的数据
     * @param e
     * @return
     */
    @ExceptionHandler(Exception.class)
    public ResponseResult exceptionHandler(Exception e){
        return ResponseResult.errorResult(AppHttpCodeEnum.SYSTEM_ERROR.getCode(),e.getMessage());
    }
}

~~~

### 6.2自定义一个异常，进行统一异常处理

~~~java
public class SystemException extends RuntimeException{

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



## 7springSecurity的权限控制

### 7.1 SecurityConfig类中添加注解

~~~java
//开启权限管理的支持
@EnableGlobalMethodSecurity(prePostEnabled = true)
public class SecurityConfigure extends WebSecurityConfigurerAdapter
~~~

### 7.2定义权限判断类

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

### 7.3给相应的方法设置需要的权限

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



