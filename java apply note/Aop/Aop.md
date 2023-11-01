



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



## 1:切点表达式execution

​		可以使用切点表达式来表示要对哪些方法进行增强。



### 1.1写法：**execution([修饰符] 返回值类型 包名.类名.方法名(参数))**

- 访问修饰符可以省略，大部分情况下省略
- 返回值类型、包名、类名、方法名可以使用星号*  代表任意
- 包名与类名之间一个点 . 代表当前包下的类，两个点 .. 表示当前包及其子包下的类
- 参数列表可以使用两个点 .. 表示任意个数，任意类型的参数列表



**execution(返回值类型 包名.类名.方法名(参数))** 例如：

```java
execution(* com.sangeng.service.*.*(..))   表示com.sangeng.service包下任意类，方法名任意，参数列表任意，返回值类型任意
   
execution(* com.sangeng.service..*.*(..))   表示com.sangeng.service包及其子包下任意类，方法名任意，参数列表任意，返回值类型任意
    
execution(* com.sangeng.service.*.*())     表示com.sangeng.service包下任意类，方法名任意，要求方法不能有参数，返回值类型任意
    
execution(* com.sangeng.service.*.delete*(..))     表示com.sangeng.service包下任意类，要求方法不能有参数，返回值类型任意,方法名要求已delete开头
```

### 1.2使用：

~~~java
	//对哪些方法增强
    @Pointcut("execution(* com.sangeng.service..*.*(..))")
    public void pt(){}
~~~



## 2.切点函数@annotation+注解方式 (环绕通知)



### 2.1自定义一个注解

~~~java
/**
 * 自定义注解用来标识实现aop增强
 */
@Target(ElementType.METHOD)//只能作用于方法上面
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



## 