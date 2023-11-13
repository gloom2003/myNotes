# springBoot的使用

## 1:特殊功能

### 1.1开跑自启：启动项目时自动执行一些操作：

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

### 1.2 定时任务：使用cron表达式实现定时任务

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


### 2.3创建启动类

创建一个类在其实加上@SpringBootApplication注解标识为启动类。

~~~~java
@SpringBootApplication
public class HelloApplication {

    public static void main(String[] args) {
        SpringApplication.run(HelloApplication.class, args);
    }
}
~~~~



## 3 多模块项目的创建

1.创建一个父工程（一个project），包含多个子模块，子模块直接有依赖关系

2.只需要父工程的pom.xml文件，不需要src文件夹（直接删除即可）



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

	<!-- 在dependencyManagement标签下面的dependencies标签并没有真正的导入依赖，只是对子模块的依赖版本进行了锁定-->
    <dependencies>
        <!-- SpringBoot的依赖配置-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-dependencies</artifactId>
            <version>2.5.0</version>
            <!-- 这里指定了很多其他依赖的版本，ctrl进入spring-boot-dependencies.xml即可查看-->
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

    <build>
        <plugins>
            <plugin>
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

其他子模块（非公共模块）的创建：

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







## 4 多模块项目打包

参考kana-blog项目(依赖了kana-framework)，配置好pom.xml文件后(如kana-blog的):

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



最开始在root模块中进行clean，先在kana-framework的LifeCycle中打包kana-framework(因为kana-blog依赖于kana-framework),然后在kana-blog的LifeCycle中打包kana-blog，最后使用kana-blog中的jar包上传到服务器即可





