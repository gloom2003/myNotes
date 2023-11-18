# Mybatis面试类知识

## 8.获取参数时 #{}和${}的区别

​	**如果使用#{}.他是预编译的sql，可以防止SQL注入攻击，更加安全**
**​	如果使用${}他是直接把参数值拿来进行字符串拼接，这样会有SQL注入的危险**

如果使用的是#{}来获取参数值日志如下：
Preparing: select * from user where id = **?** and username = **?** and age = **?** and address = **?** 
Parameters: 2(Integer), 快乐风男(String), 29(Integer), 北京(String)

如果使用${}来获取参数值日志如下：
Preparing: select * from user where id = 2 and username = 快乐风男 and age = 29 and address = 北京 

并且因为“快乐风男”字符串没有使用引号进行包裹，会报错



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

## Mybatis原理(动态代理)：

1.读取mapper接口对应的xml映射文件生成代理对象 (jdk动态代理吧？生成的代理对象实现了mapper接口,因为可以使用mapper接口从spring容器中获取，这样子的话，被代理对象是什么，被代理对象实现了什么接口？)

2.执行代理对象的方法时，其实是执行xml映射文件中的sql语句，并把sql语句的结果集根据名称映射为对应的实体类,作为方法的返回值。