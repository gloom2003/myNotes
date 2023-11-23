# Mybatis的使用 xml方式

## 1. 开始使用Mybatis

1.导入依赖

~~~~xml
        <!--mybatis依赖-->
        <dependency>
            <groupId>org.mybatis</groupId>
            <artifactId>mybatis</artifactId>
            <version>3.5.4</version>
        </dependency>
        <!--mysql驱动,底层仍然是jdbc-->
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>5.1.47</version>
        </dependency>
~~~~

2.编写核心配置

在资源目录下创建：mybatis-config.xml 内容如下：

~~~~xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
    <environments default="development">
        <environment id="development">
            <transactionManager type="JDBC"/>
            <dataSource type="POOLED">
                <property name="driver" value="com.mysql.jdbc.Driver"/>
                <property name="url" value="jdbc:mysql://localhost:3306/test"/>
                <property name="username" value="root"/>
                <property name="password" value="root"/>
            </dataSource>
        </environment>
    </environments>
        <!--方式1：声明要使用来创建实现类的xml文件的位置-->
    <mappers>
        <mapper resource="com/kana/dao/CourseDao.xml"/>
    </mappers>
    <!--方式2：声明要使用来创建实现类的xml文件所在包的位置-->
    <mappers>
        <package name="com.mybatis.dao"/>
    </mappers>
</configuration>
~~~~

3.定义实体类

~~~java
public class Course {
    private String c_id;
    private String c_name;

    @Override
    public String toString() {
        return "Course{" +
                "c_id='" + c_id + '\'' +
                ", c_name='" + c_name + '\'' +
                ", t_id='" + t_id + '\'' +
                '}';
    }

    private String t_id;

    public String getC_id() {
        return c_id;
    }

    public void setC_id(String c_id) {
        this.c_id = c_id;
    }

    public String getC_name() {
        return c_name;
    }

    public void setC_name(String c_name) {
        this.c_name = c_name;
    }

    public String getT_id() {
        return t_id;
    }

    public void setT_id(String t_id) {
        this.t_id = t_id;
    }
}

~~~

4.定义接口及对应的xml映射文件

~~~~java
public interface CourseDao {
    List<Course> findAll();
}
~~~~

资源目录下：com/sangeng/dao/UserDao.xml

~~~~xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="mybatis.dao.CourseDao">
    <!--id指定方法名，resultType表示把查询到的数据封装为什么类型-->
    <select id="findAll" resultType="mybatis.entiry.Course">
      select * from course
    </select>
</mapper>
~~~~

5.编写测试类

获取SqlSession,通过SqlSession获取UserDao调用对应的方法

~~~~java
    public static void main(String[] args) throws IOException {
        public static void main(String[] args) {
        //定义mybatis配置文件的路径,根目录为resources
        String resource = "mybatis-config.xml";
        InputStream inputStream = null;
        try {
            inputStream = Resources.getResourceAsStream(resource);
        } catch (IOException e) {
            e.printStackTrace();
        }
        SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
        //获取Sqlsession对象
        SqlSession sqlSession = sqlSessionFactory.openSession();
        //获取实现类对象
        CourseDao courseDao = sqlSession.getMapper(CourseDao.class);
        //调用方法测试
        List<Course> userList = courseDao.findAll();
        System.out.println(userList);
        //释放资源
        sqlSession.close();
    }
    }
~~~~

## 

## 2 高效编程

### 3.9.1 配置文件的代码模板

设置以后，当想要创建文件时，可以直接点击mybatis-cfg.xml文件，会自动生成含有模板的文件，达到快速创建文件的目的

![image-20210218211942452](C:\Users\GLOOM\Desktop\for zip\not system\sangGeng files\普通配套资料\Mybatis\img\image-1.png)

### 3.9.2 Mybatis插件

​		下载安装Free Mybatis plugin，安装完后重启IDEA

## 3 参数获取

### 3.1 单个参数 使用#{}

在xml的sql中获取基本数据类型、POJO对象(不继承类、实现接口的普通java类对象)、Map类型的参数、：都是直接使用#{}即可

1. 基本数据类型(如int)：#{变量名} 自动返回变量中的值,只有一个参数时，变量名可以随便写
2. POJO对象：#{对象的属性名}
3. Map对象: #{key},会自动返回value

### 3.2 多个参数 使用@Param

Mybatis会把多个参数放入一个Map集合中，默认的key是argx和paramx这种格式。

例如：

接口中方法定义如下

~~~~java
User findByCondition(Integer id,String username);
~~~~

最终map中的键值对如下：

~~~~java
{arg1=PDD, arg0=2, param1=2, param2=PDD}
~~~~

接口中方法定义       使用@Param设置存入map中的key的名称,替换掉argx

~~~~java
User findByCondition(@Param("id") Integer id,@Param("username") String username);
~~~~

最终map中的键值对如下：

~~~~
{id=2, param1=2, username=PDD, param2=PDD}
~~~~

所以我们就可以使用如下方式来获取参数

~~~~xml
    <select id="findByCondition" resultType="com.sangeng.pojo.User">
         select * from user where id = #{id} and username = #{username}
    </select>
~~~~

## 4. 核心类

### 5.1 SqlSessionFactory

​	SqlSessionFactory是一个SqlSession的工厂类。主要用来获取SqlSession对象。、

成员方法如下：

~~~~java
//获取SqlSession对象，传入的参数代表创建的SqlSession是否自动提交
SqlSession openSession(boolean autoCommit);
//空参构造器默认的autoCommit为false,执行插入等操作时需要手动调用commit()方法提交事务
SqlSession openSession();
~~~~



### 5.2 SqlSession

​	 SqlSession 提供了在数据库执行 SQL 命令所需的所有方法 。它还提供了事务的相关操作。

成员方法如下：

~~~~java
T getMapper(Class<T> type);//获取mapper对象
void commit();//提交事务
void rollback();//回滚事务
void close();//释放资源
~~~~

## 5 增删改查

插入操作：第一个参数id为自增，直接传null即可

```xml
<insert id="insertUser">
    insert into user values(null,#{username},#{age},#{address})
</insert>
```

## 6. 配置文件mybatis-config.xml详解

### 6.1 properties

​	可以使用properties标签来**读取properties配置文件**。使用其中的resource属性来设置配置文件的路径。

​	然后使用${key}来获取配置文件中的值

例如：

在resources目录下有jdbc.properties文件，内容如下：

~~~~properties
jdbc.url=jdbc:mysql://localhost:3306/mybatis_db
jdbc.driver=com.mysql.jdbc.Driver
jdbc.username=root
jdbc.password=root
~~~~

在mybatis-config.xml中：

~~~~xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
    <!--设置配置文件所在的路径-->
    <properties resource="jdbc.properties"></properties>
    
    <environments default="development">
        <environment id="development">
            <transactionManager type="JDBC"/>
            <dataSource type="POOLED">
                <!--获取配置文件中配置的对应的值来设置连接相关参数-->
                <property name="driver" value="${jdbc.driver}"/>
                <property name="url" value="${jdbc.url}"/>
                <property name="username" value="${jdbc.username}"/>
                <property name="password" value="${jdbc.password}"/>
            </dataSource>
        </environment>
    </environments>
</configuration>
~~~~

### 6.2 settings

​	可以使用该标签来设置进行一些设置

例如：

~~~~xml
    <settings>
        <!--开启自动驼峰命名映射-->
        <setting name="mapUnderscoreToCamelCase" value="true"/>
    </settings>
~~~~

具体的设置参考：https://mybatis.org/mybatis-3/zh/configuration.html#settings

### 6.3 typeAliases

​	可以用来设置给类名的绝对路径设置别名，简化书写。一般设置一个包下的类全部具有默认别名。默认别名是类名的首字母改为小写。例如：com.sangeng.pojo.User别名为user

设置com.sangeng.dao这个包下面的所有类都可以使用别名：

~~~~xml
   <typeAliases>
        <package name="com.sangeng.dao"></package>
    </typeAliases>
~~~~

### 6.4 environments

​	配置数据库相关的环境，例如事务管理器，连接池相关参数等。

~~~~xml
    <!--设置默认环境-->
	<environments default="development">
        
        <!--设置该环境的唯一标识-->
        <environment id="development">
            <transactionManager type="JDBC"/>
            <dataSource type="POOLED">
                <!--获取配置文件中配置的对应的值来设置连接相关参数-->
                <property name="driver" value="${jdbc.driver}"/>
                <property name="url" value="${jdbc.url}"/>
                <property name="username" value="${jdbc.username}"/>
                <property name="password" value="${jdbc.password}"/>
            </dataSource>
        </environment>
    </environments>
~~~~

### 6.5 mappers

​	该标签的作用是加载映射的，加载方式有如下几种(**主要使用第四种**)：

​	1 普通xml方式：使用相对于类路径的资源引用，例如：

~~~~xml
<!-- 使用相对于类路径的资源引用 -->
<mappers>
  <mapper resource="org/mybatis/builder/AuthorMapper.xml"/>
  <mapper resource="org/mybatis/builder/BlogMapper.xml"/>
  <mapper resource="org/mybatis/builder/PostMapper.xml"/>
</mappers>
~~~~


​	2 注解方式：

~~~~xml
<!-- 使用映射器接口实现类的完全限定类名 -->
<mappers>
  <mapper class="org.mybatis.builder.AuthorMapper"/>
  <mapper class="org.mybatis.builder.BlogMapper"/>
  <mapper class="org.mybatis.builder.PostMapper"/>
</mappers>
~~~~

​	3 包方式：将包内的映射器接口实现全部注册为映射器，例如：

~~~~xml
<!-- 定义dao接口所在的包。要求xml文件存放的路径和dao接口的包名要对应(一个在java目录下，另一个在Resources目录下，其他的子目录必须相同) -->
<mappers>
  <package name="org.mybatis.builder"/>
</mappers>
~~~~

## 7. Mybatis配置打印日志

作用：Mybatis执行的具体细节会被打印出来，例如：执行的sql语句、sql中的参数的值等

①配置log4j的配置文件： 在resources目录下创建log4j.properties文件，内容如下：

~~~~properties
### direct log messages to stdout ###
log4j.appender.stdout=org.apache.log4j.ConsoleAppender
log4j.appender.stdout.Target=System.out
log4j.appender.stdout.layout=org.apache.log4j.PatternLayout
log4j.appender.stdout.layout.ConversionPattern=%d{ABSOLUTE} %5p %c{1}:%L - %m%n

### direct messages to file mylog.log ###
log4j.appender.file=org.apache.log4j.FileAppender
log4j.appender.file.File=c:/mylog.log
log4j.appender.file.layout=org.apache.log4j.PatternLayout
log4j.appender.file.layout.ConversionPattern=%d{ABSOLUTE} %5p %c{1}:%L - %m%n

### set log levels - for more verbose logging change 'info' to 'debug' ###

log4j.rootLogger=debug, stdout

~~~~

②引入依赖

~~~~xml
        <dependency>
            <groupId>log4j</groupId>
            <artifactId>log4j</artifactId>
            <version>1.2.17</version>
        </dependency>
~~~~

## 9. 注解开发

​	我们也可以使用注解的形式来进行开发，用注解来替换掉xml。 使用注解来映射简单语句会使代码显得更加简洁，但对于稍微复杂一点的语句，Java 注解不仅力不从心，还会让你本就复杂的 SQL 语句更加混乱不堪。 所以我们在实际企业开发中一般都是使用XML的形式。

### 9.1 步骤

①在核心配置文件(mybatis-config.xml)中**配置mapper接口（不是xml文件）**所在的包名

~~~~xml
    <mappers>
        <package name="com.sangeng.dao"></package>
    </mappers>
~~~~

②在接口对应方法上使用注解来配置需要执行的sql

~~~~java
public interface UserDao {

    @Select("select * from user")
    List<User> findAll();

    @Insert("insert into user values(null,#{username},#{age},#{address})")
    void insertUser(User user);

    @Update("UPDATE USER SET age = #{age} , username = #{username},address = #{address} WHERE id = #{id}")
    void updateUser(User user);

    @Delete("delete from user where id = #{id}")
    void deleteById(Integer id);
}

~~~~

③和之前的一样获取Mapper调用方法即可

~~~~java
    public static void main(String[] args) throws IOException {
        //定义mybatis配置文件的路径
        String resource = "mybatis-config.xml";
        InputStream inputStream = Resources.getResourceAsStream(resource);
        SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
        //获取Sqlsession对象
        SqlSession sqlSession = sqlSessionFactory.openSession();
        //获取UserDao实现类对象
        UserDao userDao = sqlSession.getMapper(UserDao.class);
        //调用方法测试
        List<User> userList = userDao.findAll();
        System.out.println(userList);
        //释放资源
        sqlSession.close();
    }
~~~~



## 10.动态SQL

​	在实际开发中的SQL语句没有之前的这么简单，很多时候需要根据传入的参数情况动态的生成SQL语句。Mybatis提供了动态SQL相关的标签让我们使用。

### 10.1  if标签

​	可以使用if标签进行条件判断，条件成立才会把if标签中的内容拼接进sql语句中，其中test属性是必须的。

例如：

~~~~xml
    <select id="findByCondition" resultType="com.sangeng.pojo.User">
         select * from user
         where  id = #{id}
        <if test="username!=null">
           and username = #{username}
        </if>
    </select>
~~~~

如果参数username为null则执行的sql为：**select * from user where id = ?** 

如果参数username不为null则执行的sql为：**select * from user where id = ? and username = ?** 



**注意：在test属性中表示参数的时候不需要写#{}，写了会出问题。**

### 10.2 trim标签

​	可以使用该标签动态的添加前缀或后缀，也可以使用该标签动态的消除前缀。



#### 10.2.1 prefixOverrides属性

​	用来设置需要被清除的前缀,多个值可以用|分隔，注意|前后不要有空格，否则空格也会被匹配。例如： and|or

例如：

~~~~xml
    <select id="findByCondition" resultType="com.sangeng.pojo.User">
        select * from user
        <trim prefixOverrides="and|or" >
            and
        </trim>
    </select>
~~~~

最终执行的sql为： select * from user

#### 10.2.2 suffixOverrides属性

​	用来设置需要被清除的后缀,多个值可以用|分隔，注意|前后不要有空格。例如： and|or

例如：

~~~~xml
    <select id="findByCondition" resultType="com.sangeng.pojo.User">
        select * from user
        <trim suffixOverrides="like|and" >
            where 1=1 like
        </trim>
    </select>
~~~~

最终执行的sql为： select * from user  去掉了后缀like

#### 10.2.3 prefix属性 添加前缀

​	用来设置动态添加的前缀，如果标签中有内容就会添加上设置的前缀

例如：

~~~~xml
    <select id="findByCondition" resultType="com.sangeng.pojo.User">
        select * from user
        <trim prefix="where" >
           1=1
        </trim>
    </select>
~~~~

最终执行的sql为：select * from user where 1=1   动态增加了前缀where

#### 10.2.4 suffix属性 添加后缀

​	用来设置动态添加的后缀，如果标签中有内容就会添加上设置的后缀

~~~~xml
    <select id="findByCondition" resultType="com.sangeng.pojo.User">
        select * from user
        <trim suffix="1=1" >
           where
        </trim>
    </select>
~~~~

最终执行的sql为：select * from user where 1=1   动态增加了后缀1=1

#### 10.2.5 使用案例：根据id和username进行查询的sql编写
接口：
~~~~java
User findByCondition(@Param("id") Integer id,@Param("username") String username);
~~~~

动态添加前缀where 并且消除前缀and或者or:
~~~~xml
    <select id="findByCondition" resultType="com.sangeng.pojo.User">
        select * from user
        <trim prefix="where" prefixOverrides="and|or" >
            <if test="id!=null">
                id = #{id}
            </if>
            <if test="username!=null">
                and username = #{username}
            </if>
        </trim>
    </select>
~~~~

调用方法时如果传入的id和username为null则执行的SQL为：select * from user

调用方法时如果传入的id为null，username不为null，则执行的SQL为：select * from user where username = ?

### 10.3 where标签

​	where标签等价于：

~~~~xml
<trim prefix="where" prefixOverrides="and|or" ></trim>
~~~~

​	可以使用where标签动态的拼接where并且去除前缀的and或者or。

例如：

~~~~xml
    <select id="findByCondition" resultType="com.sangeng.pojo.User">
        select * from user
        <where>
            <if test="id!=null">
                id = #{id}
            </if>
            <if test="username!=null">
                and username = #{username}
            </if>
        </where>
    </select>
~~~~

如果id和username都为null，则执行的sql为：**select * from user **

如果id为null，username不为null，则执行的sql为：**select * from user  where username = ? **

### 10.4 set标签

​	set标签等价于

~~~~xml
<trim prefix="set" suffixOverrides="," ></trim>
~~~~

​	可以使用set标签动态的拼接set并且去除后缀的逗号。

例如：

~~~~xml
    <update id="updateUser">
        UPDATE USER
        <set>
            <if test="username!=null">
                username = #{username},
            </if>
            <if test="age!=null">
                age = #{age},
            </if>
            <if test="address!=null">
                address = #{address},
            </if>
        </set>
        where id = #{id}
    </update>
~~~~

如果调用方法时传入的User对象的id为2，username不为null，其他属性都为null则最终执行的sql为：UPDATE USER SET username = ? where id = ? 

### 10.5 foreach标签

​	可以使用foreach标签遍历集合或者数组类型的参数，获取其中的元素拿来动态的拼接SQL语句。

例如：

方法定义如下

~~~~java
List<User> findByIds(@Param("ids") Integer[] ids);
~~~~

如果期望动态的根据实际传入的数组的长度拼接SQL语句。例如传入长度为5的数组，最终执行的SQL为：

~~~~sql
select * from User WHERE id in( ? , ? , ? , ?, ? ) 
~~~~

则在xml映射文件中可以使用以下写法

~~~~xml
	<select id="findByIds" resultType="com.sangeng.pojo.User">
        select * from User
        <where>
            <foreach collection="ids" open="id in(" close=")" item="id" separator=",">
                #{id}
            </foreach>
        </where>
    </select>
~~~~

**collection：表示要遍历的参数。**

**open:表示遍历开始时拼接的语句**

**item：表示给当前遍历到的元素的取的名字**

**separator：表示每遍历完一次拼接的分隔符**

**close：表示最后一次遍历完拼接的语句**



**注意：如果方法参数是数组类型，map中默认的参数名是array，如果方法参数是list集合默认的参数名是list。建议遇到数组或者集合类型的参数统一使用@Param注解进行命名。**

### 10.6 choose、when、otherwise标签

​	当我们不想使用所有的条件，而只是想从多个条件中选择一个使用时。可以使用choose系列标签。类似于java中的switch。

例如:

接口中方法定义如下

~~~~java
List<User> selectChose(User user);
~~~~

期望：

​		如果user对象的id不为空时就通过id查询。

​		如果id为null,username不为null就通过username查询。

​		如果id和username都会null就查询id为3的用户

xml映射文件如下

~~~~xml
 	<select id="selectChose" resultType="com.sangeng.pojo.User">
        select * from user
        <where>
            <choose>
                <when test="id!=null">
                    id = #{id}
                </when>
                <when test="username!=null">
                    username = #{username}
                </when>
                <otherwise>
                    id = 3
                </otherwise>
            </choose>
        </where>
    </select>
~~~~



- **choose类似于java中的switch**

- **when类似于java中的case**

- **otherwise类似于java中的dufault**

  

​	一个choose标签中最多只会有一个when中的判断成立(相当于switch中自带了break)。从上到下去进行判断。如果成立了就把标签体的内容拼接到sql中，并且不会进行其它when的判断和拼接。如果所有的when都不成立则拼接otherwise中的语句。

## 11. SQL片段抽取 include标签，替换sql中的*符号 

​	我们在xml映射文件中编写SQL语句的时候可能会遇到重复的SQL片段。这种SQL片段我们可以使用sql标签来进行抽取。然后在需要使用的时候使用include标签进行使用。

例如：

~~~~xml
    <sql id="baseSelect" >id,username,age,address</sql>

    <select id="findAll" resultType="com.sangeng.pojo.User">
        select <include refid="baseSelect"/>  from user
    </select>
~~~~

最终执行的sql为： **select id,username,age,address from user** 



## 12 数据库表的设计

一对多的关系：一个用户有多个订单，只需要在订单表中添加用户id字段即可表示。

多对多的关系：一个用户可以有多个角色，一个角色可以有多个用户。需要一张中间表，记录用户id与角色id之间的关系。



## 13 使用JUnit的相关注解进行测试

~~~java
public class MainTest {
    private SqlSession sqlSession = null;
	//在每个test方法运行之前执行，给sqlSession对象赋值
    @Before
    public void before(){
        String resource = "mybatis-config.xml";
        InputStream inputStream = null;
        try {
            inputStream = Resources.getResourceAsStream(resource);
        } catch (IOException e) {
            e.printStackTrace();
        }
        SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
        //获取Sqlsession对象
        sqlSession = sqlSessionFactory.openSession();
    }

    @Test
    public void test1(){
        OrderDao orderDao = sqlSession.getMapper(OrderDao.class);
        List<Order> orders = orderDao.findAll();
        System.out.println(orders);
    }
	
    //在每个test方法运行之后运行，关闭数据库连接
    @After
    public void after(){
        sqlSession.commit();
        sqlSession.close();
    }



}

~~~

## 14. ResultMap 自定义结果集和实体类的映射规则

### 14.1 基本使用

​	我们可以使用resultMap标签自定义结果集和实体类属性的映射规则。

mybatis是根据sql语句中的结果集的**列名**与实体类的**属性名**进行映射的。**相同则能够完成自动映射**，解决方法：

1.可以修改sql中的列名（起别名）

2.可以使用mybatis主配置中的setting标签开启驼峰命名的转换

3.使用ResultMap设置自定义映射规则

~~~~xml
    <!--
        resultMap 用来自定义结果集和实体类的映射
            属性：
                id 相当于这个resultMap的唯一标识
                type 用来指定映射到哪个实体类
        id标签  用来指定主键列的映射规则
            属性：
                property 要映射的属性名
                column  对应的列名
        result标签 用来指定普通列的映射规则
            属性：
                property 要映射的属性名
                column 对应的列名
    -->
    <resultMap id="orderMap" type="com.sangeng.pojo.Order" >
        <id column="id" property="id"></id>
        <result column="createtime" property="createtime"></result>
        <result column="price" property="price"></result>
        <result column="remark" property="remark"></result>
        <result column="user_id" property="userId"></result>
    </resultMap>

	<!--使用我们自定义的映射规则，不使用resultType-->
    <select id="findAll" resultMap="orderMap">
        SELECT id,createtime,price,remark,user_id  FROM ORDERS
    </select>
	
~~~~

### 14.2 自动映射 autoMapping

​	我们定义resultMap时**默认情况下自动映射是开启状态**的。也就是如果结果集的列名和我们的属性名相同是会自动映射的，我们**只需要写特殊情况的映射关系即可**。

例如：

下面这种写法和上面的写法会有相同的效果，因为其他属性的属性名和结果集的列名都是相同的会自动映射。

~~~~xml
    <resultMap id="orderMap" type="com.sangeng.pojo.Order" >
        <result column="user_id" property="userId"></result>
    </resultMap>
	<!--使用我们自定义的映射规则-->
    <select id="findAll" resultMap="orderMap">
        SELECT id,createtime,price,remark,user_id  FROM ORDERS
    </select>
~~~~

​	如有需要可以选择**关闭自动映射**可以把resultMap的autoMapping属性设置为false。	

例如：	

~~~~xml
    <resultMap id="orderMap" type="com.sangeng.pojo.Order" autoMapping="false">
        <id column="id" property="id"></id>
        <result column="createtime" property="createtime"></result>
        <result column="price" property="price"></result>
        <result column="remark" property="remark"></result>
        <result column="user_id" property="userId"></result>
    </resultMap>
~~~~

### 14.3 继承映射关系 extends

​	我们可以使用resultMap 的extends属性来指定一个resultMap，从而复用重复的映射关系配置。

例如：

~~~~xml
  	<!--定义个父映射，供其他resultMap继承-->
	<resultMap id="baseOrderMap" type="com.sangeng.pojo.Order" >
        <id column="id" property="id"></id>
        <result column="createtime" property="createtime"></result>
        <result column="price" property="price"></result>
        <result column="remark" property="remark"></result>
    </resultMap>
	<!--继承baseOrderMap，然后只需要写自己特有的映射关系即可-->
    <resultMap id="orderMap" type="com.sangeng.pojo.Order" autoMapping="false" extends="baseOrderMap">
        <result column="user_id" property="userId"></result>
    </resultMap>
~~~~

## 15. 多表查询

​	有的时候我们需要查询多张表的数据才可以得到我们要的结果。

​	我们可以直接写一个多表关联的SQL进行查询。也可以分步进行多次的查询来拿到我们需要的结果。		    

​	Mybatis就提供了对应的配置，可以让我们去更方便的进行相应的查询和对应的结果集处理。



### 15.1 多表关联查询

#### 15.1.1 一对一关系

​	两个实体之间是一对一的关系。(例如我们需要查询订单，要求还需要下单用户的数据。这里的订单相对于用户是一对一。)

例如：

方法定义如下

~~~~java
    //根据订单id查询订单，要求把下单用户的信息也查询出来
    Order findById(Integer id);
~~~~

因为期望Order中还能包含下单用户的数据，所以可以再Order中增加一个属性，用来映射查询出来的用户结果集

~~~~java
private User user;
~~~~



SQL语句如下

~~~~mysql
SELECT 
	o.id,o.`createtime`,o.`price`,o.`remark`,o.`user_id`,u.`id` uid,u.`username`,u.`age`,u.`address`
FROM 
	orders o,USER u
WHERE
	o.`user_id` = u.`id`
	AND o.id = 2
~~~~

我们可以使用如下两种方式封装结果集。



##### 15.1.1.1 使用ResultMap对所有字段进行映射

​	可以使用ResultMap设置user对象的属性的映射规则。

①resultMap定义，主要是对user对象的属性设置映射规则

~~~~xml
 	<resultMap id="baseOrderMap" type="com.sangeng.pojo.Order" >
        <id column="id" property="id"></id>
        <result column="createtime" property="createtime"></result>
        <result column="price" property="price"></result>
        <result column="remark" property="remark"></result>
    </resultMap>

    <resultMap id="orderMap" type="com.sangeng.pojo.Order" autoMapping="false" extends="baseOrderMap">
        <result column="user_id" property="userId"></result>
    </resultMap>

    <!--Order和User关联的映射-->
    <resultMap id="orderUserMap" type="com.sangeng.pojo.Order" autoMapping="false" extends="orderMap">
        <!--对user对象中的属性进行映射-->
        <result property="user.id" column="uid"></result>
        <result property="user.username" column="username"></result>
        <result property="user.age" column="age"></result>
        <result property="user.address" column="address"></result>
    </resultMap>
~~~~

②使用定义好的resultMap

~~~~xml
<!--根据订单id查询订单，要求把下单用户的信息也查询出来-->
    <select id="findById" resultMap="orderUserMap">
        SELECT
            o.`id`,o.`createtime`,o.`price`,o.`remark`,o.`user_id`,u.`id` uid,u.`username`,u.`age`,u.`address`
        FROM
            orders o,`user` u
        WHERE
            o.id = #{id} AND
            o.`user_id`=u.`id`
    </select>
~~~~

##### 15.1.1.2 使用ResultMap中的association进行映射

​	可以使用ResultMap中的子标签association 来设置关联实体类的映射规则.(常用)

①定义resultMap

~~~~xml
 	<resultMap id="baseOrderMap" type="com.sangeng.pojo.Order" >
        <id column="id" property="id"></id>
        <result column="createtime" property="createtime"></result>
        <result column="price" property="price"></result>
        <result column="remark" property="remark"></result>
    </resultMap>

    <resultMap id="orderMap" type="com.sangeng.pojo.Order" autoMapping="false" extends="baseOrderMap">
        <result column="user_id" property="userId"></result>
    </resultMap>

    <!--Order和User关联的映射（使用association）-->
    <resultMap id="orderUserMapUseAssociation" type="com.sangeng.pojo.Order" autoMapping="false" extends="orderMap">
        <!--property指定对user属性进行映射，javaType类型为com.sangeng.pojo.User-->
        <association property="user" javaType="com.sangeng.pojo.User">
            <id property="id" column="uid"></id>
            <result property="username" column="username"></result>
            <result property="age" column="age"></result>
            <result property="address" column="address"></result>
        </association>
    </resultMap>
~~~~

②使用resultMap

~~~~xml
<!--根据订单id查询订单，要求把下单用户的信息也查询出来-->
    <select id="findById" resultMap="orderUserMapUseAssociation">
        SELECT
            o.`id`,o.`createtime`,o.`price`,o.`remark`,o.`user_id`,u.`id` uid,u.`username`,u.`age`,u.`address`
        FROM
            orders o,`user` u
        WHERE
            o.id = #{id} AND
            o.`user_id`=u.`id`
    </select>
~~~~



#### 15.1.2 一对多关系

​	两个实体之间是一对多的关系。(例如我们需要查询用户，要求还需要该用户所具有的角色信息。这里的用户相对于角色是一对多的。)



例如：

方法定义如下

~~~~java
    //根据id查询用户，并且要求把该用户所具有的角色信息也查询出来
    User findById(Integer id);
~~~~

因为期望User中还能包含该用户所具有的角色信息，所以可以在User中增加一个属性

~~~~java
//    该用户所具有的角色
    private List<Role> roles;
~~~~



SQL语句如下

~~~~mysql
SELECT 
	u.`id`,u.`username`,u.`age`,u.`address`,r.id rid,r.name,r.desc
FROM 
	USER u,user_role ur,role r
WHERE 
	u.id=ur.user_id AND ur.role_id = r.id
	AND u.id = 2
~~~~

结果集

![image-20210224164927461](C:\Users\GLOOM\Desktop\for zip\not system\sangGeng files\普通配套资料\Mybatis\img\image-4.png)

我们可以使用如下的方式封装结果集。



##### 15.1.2.1 使用ResultMap中的collection

​	可以使用ResultMap中的子标签association 来设置关联实体类的映射规则.



①定义ResultMap

~~~~xml
	<!--定义User基本属性映射规则-->
	<resultMap id="userMap" type="com.sangeng.pojo.User">
        <id property="id" column="id"></id>
        <result property="username" column="username"></result>
        <result property="age" column="age"></result>
        <result property="address" column="address"></result>
    </resultMap>
	
    <resultMap id="userRoleMap" type="com.sangeng.pojo.User"  extends="userMap">
        <!--ofType属性指定需要映射的集合中的泛型-->
        <collection property="roles" ofType="com.sangeng.pojo.Role" >
            <id property="id" column="rid"></id>
            <result property="name" column="name"></result>
            <result property="desc" column="desc"></result>
        </collection>
    </resultMap>

~~~~

②使用ResultMap

~~~~xml
    
    <select id="findById" resultMap="userRoleMap" >
        SELECT 
            u.`id`,u.`username`,u.`age`,u.`address`,r.id rid,r.name,r.desc
        FROM 
            USER u,user_role ur,role r
        WHERE 
            u.id=ur.user_id AND ur.role_id = r.id
            AND u.id = #{id}
    </select>
~~~~

最终封装完的结果如下：

![image-20210224170108202](C:\Users\GLOOM\Desktop\for zip\not system\sangGeng files\普通配套资料\Mybatis\img\image-5.png)

### 15.2 分步查询（分布式项目中常用，处理数据量大的情况）

​	如果有需要多表查询的需求我们也可以选择**用多次查询的方式来查询出我们想要的数据**。Mybatis也提供了对应的配置。

​	例如我们需要查询用户，要求还需要查询出该用户所具有的角色信息。我们可以选择先查询User表查询用户信息。然后在使用查询出来的用户信息去查询关联的角色信息。	

#### 15.2.1实现步骤

​	具体步骤如下：

##### ①定义查询方法

​	因为我们要分两步查询: 1.查询User 2.根据用户的id查询Role  所以我们需要定义下面两个方法，并且把对应的标签也先写好



1.查询User

~~~~java
    //根据用户名查询用户，并且要求把该用户所具有的角色信息也查询出来
    User findByUsername(String username);
~~~~

~~~~xml
    <!--根据用户名查询用户-->
    <select id="findByUsername" resultType="com.sangeng.pojo.User">
        select id,username,age,address from user where username = #{username}
    </select>
~~~~

2.根据user_id查询Role

~~~~java
public interface RoleDao {
	//根据userId查询所具有的角色
    List<Role> findRoleByUserId(Integer userId);
}

~~~~

~~~~xml
    <!--根据userId查询所具有的角色-->
    <select id="findRoleByUserId" resultType="com.sangeng.pojo.Role">
        select 
            r.id,r.name,r.desc
        from 
            role r,user_role ur
        where 
            ur.role_id = r.id
            and ur.user_id = #{userId}
    </select>
~~~~

##### ②配置分步查询

​	我们期望的效果是调用findByUsername方法查询出来的结果中就包含角色的信息。所以我们可以设置findByUsername方法的RestltMap，**指定分步查询**

~~~~xml
    <resultMap id="userMap" type="com.sangeng.pojo.User">
        <id property="id" column="id"></id>
        <result property="username" column="username"></result>
        <result property="age" column="age"></result>
        <result property="address" column="address"></result>
    </resultMap>
    <!--
           select属性：指定用哪个方法来查询当前属性的数据 写法：包名.接口名.方法名
           column属性：设置当前结果集中哪列的数据作为select属性指定的查询方法中需要的参数
       -->
	<resultMap id="userRoleMapBySelect" type="com.sangeng.pojo.User" extends="userMap">
        <collection property="roles"
                    ofType="com.sangeng.pojo.Role"
                    select="com.sangeng.dao.RoleDao.findRoleByUserId"
                    column="id">
        </collection>
    </resultMap>
~~~~

​	指定findByUsername使用我们刚刚创建的resultMap

~~~~xml
    <!--根据用户名查询用户-->
    <select id="findByUsername" resultMap="userRoleMapBySelect">
        select id,username,age,address from user where username = #{username}
    </select>
~~~~

#### 15.2.2 设置按需加载(懒加载)

​	我们可以设置按需加载，这样在我们代码中**需要用到关联数据的时候才会去查询(执行代码到需要这个数据时才会去查询)**关联数据。

​	有两种方式可以配置分别是全局配置和局部配置



1. 局部配置

   设置fetchType属性为lazy

   ~~~~xml
   	<resultMap id="userRoleMapBySelect" type="com.sangeng.pojo.User" extends="userMap">
           <collection property="roles"
                       ofType="com.sangeng.pojo.Role"
                       select="com.sangeng.dao.RoleDao.findRoleByUserId"
                       column="id" fetchType="lazy">
           </collection>
       </resultMap>
   ~~~~

   

2. 全局配置 

   主配置文件中：设置lazyLoadingEnabled为true

   ~~~~xml
       <settings>
          <setting name="lazyLoadingEnabled" value="true"/>
       </settings>
   ~~~~

## 16.分页查询-PageHelper

​	我们可以使用PageHelper非常方便的帮我们实现分页查询的需求。不需要自己在SQL中拼接SQL相关参数，并且能非常方便的获取的总页数总条数等分页相关数据。



### 16.1 实现步骤

#### ①定义方法查询方法以及生成对应标签	

~~~~java
	List<User> findAll();
~~~~

~~~~xml
    <select id="findAll" resultType="com.sangeng.pojo.User">
        select id,username,age,address from user
    </select>
~~~~

#### ② 引入依赖

~~~~xml
<dependency>
    <groupId>com.github.pagehelper</groupId>
    <artifactId>pagehelper</artifactId>
    <version>4.0.0</version>
</dependency>
~~~~

#### ③ 配置Mybatis核心配置文件使用分页插件

~~~~xml
 <plugins>
        <!-- 注意：分页助手的插件  注意标签配置的顺序 -->
        <plugin interceptor="com.github.pagehelper.PageHelper">
            <!-- 指定方言(使用的是mysql数据库) -->
            <property name="dialect" value="mysql"/>
        </plugin>
    </plugins>
~~~~

#### ④ 开始分页查询

我们只需要在使用查询方法前设置分页参数即可,**会自动帮我们添加 limit 1,1的sql中**

~~~~java
	UserDao userDao = session.getMapper(UserDao.class);
    //设置分页查询参数,调用这个方法后，就会进行分页查询，会自动帮助我们拼接limit语句了
    PageHelper.startPage(1,1);
    List<User> users = userDao.findAll();
    System.out.println(users.get(0));
~~~~

如果需要获取总页数总条数等分页相关数据，只需要创建一个PageInfo对象，**把刚刚查询出的返回值做为构造方法参数传入**。然后使用pageInfo对象获取即可。

~~~~java
	PageInfo<User> pageInfo = new PageInfo<User>(users);
    System.out.println("总条数："+pageInfo.getTotal());
	System.out.println("总页数："+pageInfo.getPages());
	System.out.println("当前页："+pageInfo.getPageNum());
	System.out.println("每页显示长度："+pageInfo.getPageSize());
~~~~

### 16.2 一对多多表查询中使用PageHelper进行分页导致的问题

​	我们在进行一对多的多表查询时，如果使用了PageHelper进行分页。会出现**关联数据不全（比如：一个用户有多个身份的时候，查询一个用户的同时想要查询出它的身份信息，这里的身份信息即是关联数据）**的情况。我们可以使用分步查询的方式解决该问题。

**有坑**：在**一对多的查询**中使用PageHelper时，比如查询一个用户的同时想要查询出它的身份信息，面对一个用户有多个身份的时候，使用limit 1,1会使只能查询出一个身份来，建议**使用分步查询来解决**此问题。



## 17.Mybatis缓存

​	Mybatis的缓存其实就是把之前查到的数据存入内存（map）,下次如果还是查相同的东西，就可以直接从缓存中取，从而提高效率。

​	Mybatis有一级缓存和二级缓存之分，

**一级缓存（默认开启）是sqlSession级别的缓存**(一个sqlsession对应一个连接，一级缓存会存放在对应sqlSession的map中，与其他的sqlSession是隔离的，所以说是sqlSession级别的缓存)。

**二级缓存相当于mapper级别的缓存**(使用同一个类的字节码对象创建出来的mapper调用同一个方法，传入相同的参数时才会触发)。



### 17.1 一级缓存

几种不会使用一级缓存的情况:
	1.调用相同方法但是传入的参数不同
	2.调用相同方法参数也相同，但是使用的是另外一个SqlSession
	3.如果查询完后，对同一个表进行了增，删改的操作，都会清空这sqlSession上的缓存
	4.如果手动调用SqlSession的clearCache()方法清除缓存了，后面也使用不了缓存

### 17.2 二级缓存

#### 17.2.1 开启二级缓存

①全局开启

在Mybatis核心配置文件中配置

~~~~xml
    <settings>
        <setting name="cacheEnabled" value="true"/>
    </settings>
~~~~



②局部开启

在要开启二级缓存的mapper映射文件中设置 cache标签

~~~~xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd" >
<mapper namespace="com.sangeng.dao.RoleDao">
    <cache></cache>
</mapper>
~~~~

3.查询的出来的类需要实现序列化接口

~~~java
public class User implements Serializable
~~~

#### 17.2.2 触发案例

​	注意：只在sqlsession调用了**close()或者commit()**后的数据才会进入二级缓存。

![https://image.itbaima.net/images/173/image-20231024165155728.png](https://image.itbaima.net/images/173/image-20231024165155728.png)

代码解释：当UserDao接口开启二级缓存后，执行session.close()方法关闭session连接后，会把一级缓存中的数据（即findById(2)的查询结果）存放到二级缓存中(即UserDao字节码对象创建出来的mapper)，即使下面新建了一个新的session连接，但是只要使用的是UserDao字节码对象创建出来的mapper，就会走二级缓存，不执行sql语句。



与一级缓存相同，如果查询完后，对同一个表进行了增，删，改的操作，也会清空这UserDao上的二级缓存

#### 17.2.3 使用建议

​	二级缓存在实际开发中基本不会使用。

原因：容易造成从二级缓存获取的数据与数据库中的不一致



### 18 其他注意事项

1.sql中不要使用*，而是具体的写上需要查询的字段名

2.创建packge时,使用com.kana.mybatis,创建目录时使用com/kana/mybatis

