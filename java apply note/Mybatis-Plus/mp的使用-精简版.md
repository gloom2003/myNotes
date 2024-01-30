# mp的使用-精简版

**好处**：提供了单表的crud方法，不需要写单表的sql，还可以使用这些单表的方法进行分步查询，达到了多表查询的效果。

# 核心

## 1 BaseMapper接口的使用

默认情况下MP操作的表名就是实体类的类名，根据实体类的属性名去映射表的列名

创建Mapper接口继承BaseMapper接口，	**继承BaseMapper，可以使用单表操作，多表操作则使用Mybatis配合MP。**

~~~~java
public interface UserMapper extends BaseMapper<User> {
}
~~~~

BaseMapper接口中已经**提供了很多单表的crud常用方法**。所以我们只需要直接从容器中获取Mapper就可以进行操作了，不需要自己去编写Sql语句。

###  增

​	我们可以使用insert方法来实现数据的插入。

示例：

~~~~java
    @Test
    public void testInsert(){
        User user = new User();
        user.setUserName("三更草堂333");
        user.setPassword("7777888");
        int r = userMapper.insert(user);
        System.out.println(r);
    }
~~~~



### 删

​	我们可以使用**deleteXXX**方法来实现数据的删除。

- deleteById(id),传入一个id，删除这个id,sql: dehete from table where id = ?
- deleteBatchIds(ids),传入一个集合ids,删除这些id,sql: dehete from table where id in (?,?,?)
- deleteByMap(map),传入一个map集合，根据map的键值对进行删除，sql:dehete from table where key1 = value1 **and** key2 = value2

示例：

~~~~java
    @Test
    public void testDelete(){
        List<Integer> ids = new ArrayList<>();
        ids.add(5);
        ids.add(6);
        ids.add(7);
        int i = userMapper.deleteBatchIds(ids);
        System.out.println(i);
    }
    @Test
    public void testDeleteById(){
        int i = userMapper.deleteById(8);
        System.out.println(i);
    }
    @Test
    public void testDeleteByMap(){
        Map<String, Object> map = new HashMap<>();
        map.put("name","提姆");
        map.put("age",22);
        int i = userMapper.deleteByMap(map);
        System.out.println(i);
    }
~~~~





### 改

​	我们可以使用**updateXXX**方法来实现数据的删除。

示例：

~~~~java
    @Test
    public void testUpdate(){
        //把id为2的用户的年龄改为14
        User user = new User();
        user.setId(2L);
        user.setAge(14);
        int i = userMapper.updateById(user);
        System.out.println(i);
    }
~~~~

我们想把id大于1的用户的年龄修改为99，则可以使用如下写法：set()方法操作的是sql语句中set语句的字段

~~~~java
    @Test
    public void testUpdateWrapper(){
        UpdateWrapper<User> updateWrapper = new UpdateWrapper<>();
        updateWrapper.gt("id",1);
        updateWrapper.set("age",99);
        userMapper.update(null,updateWrapper);
    }
~~~~



### 查

直接使用：

~~~java
    @Test
    public void testQueryList(){
        List<User> users = userMapper.selectList(null);
    }

	@Test
    public User test() {
        User user = userMapper.selectById(3);
        Orders orders = ordersMapper.selectOne(queryWrapper);
        return user;
    }
~~~



用Wrapper写法如下：

~~~~java
    @Test
    public void testWrapper01(){
        QueryWrapper wrapper = new QueryWrapper();
        wrapper.gt("age",18);
        wrapper.eq("address","狐山");
        List<User> users = userMapper.selectList(wrapper);
        System.out.println(users);
    }
~~~~

进行分页操作：

~~~java
    @Test
    public void testPage(){
        IPage<User> page = new Page<>();
        //设置每页条数
        page.setSize(2);
        //设置查询第几页
        page.setCurrent(1);
        userMapper.selectPage(page, null);// 返回值其实就是page对象
        System.out.println(page.getRecords());//获取当前页的数据
        System.out.println(page.getTotal());//获取总记录数
        System.out.println(page.getCurrent());//当前页码
    }
~~~



## 2 各种wrapper的使用



MP为我们提供了一个功能强大的**条件构造器 `Wrapper`**

​	我们在实际操作数据库的时候会涉及到很多的条件。所以MP为我们提供了一个功能强大的**条件构造器 `Wrapper`** 。使用它可以让我们非常方便的构造条件。

​	其继承体系如下：

​	![image-20210823105447044](C:\Users\GLOOM\Desktop\for zip\not system\sangGeng files\需要三连资料\MybatisPlus\img\wrapper继承体系图.png)

​	在其子类**AbstractWrapper**中提供了**很多用于构造Where条件**的方法。

### AbstractWrapper

`eq：equals，等于`
`gt：greater than ，大于 >`
`ge：greater than or equals，大于等于≥`
`lt：less than，小于<`
`le：less than or equals，小于等于≤`
`between：相当于SQL中的BETWEEN`
`like：模糊匹配。like("name","黄")，相当于SQL的name like '%黄%'`
`likeRight：模糊匹配右半边。likeRight("name","黄")，相当于SQL的name like '黄%'`
`likeLeft：模糊匹配左半边。likeLeft("name","黄")，相当于SQL的name like '%黄'`
`notLike：notLike("name","黄")，相当于SQL的name not like '%黄%'`
`isNull`
`isNotNull`
`and：SQL连接符AND`
`or：SQL连接符OR`

`in: in(“age",{1,2,3})相当于 age in(1,2,3)`

`groupBy: groupBy("id","name")相当于 group by id,name`

`orderByAsc :orderByAsc("id","name")相当于 order by id ASC,name ASC`

`orderByDesc :orderByDesc ("id","name")相当于 order by id DESC,name DESC`

一般使用两个参数where方法的

3个参数的eq()方法使用：

~~~java
		//判断categoryId是否有值， 后端也要做检查，防一手懂技术的人直接请求api而不经过前端的检查 
        LambdaQueryWrapper<Article> queryWrapper = new LambdaQueryWrapper<>();
        //Objects.nonNull(categoryId)判断categoryId不是null,返回true表示不是null
	    // 只有第一个参数返回true时，才会拼接后面的 and CategoryId = ?的sql语句
        queryWrapper.eq(Objects.nonNull(categoryId) && categoryId > 0, Article::getCategoryId, categoryId);
~~~



### QueryWrapper

​	`AbstractWrapper`的子类`QueryWrapper`则额外提供了用于针对sql中Select语法的`select`方法。可以用来设置查询哪些列。

比如：

MP写法如下：s**elect()方法操作的是sql语句中select语句的字段**，**默认查询所有字段**。

~~~~java
    @Test
    public void testSelect01(){
        QueryWrapper<User> queryWrapper = new QueryWrapper<>(); // select {} from user
        queryWrapper.select("id","user_name");// select "id","user_name" from user
        List<User> users = userMapper.selectList(queryWrapper);
        System.out.println(users);
    }
~~~~

### UpdateWrapper

​	`AbstractWrapper`的子类`UpdateWrapper`则额外提供了用于针对sql中SET语法的`set`方法。可以用来设置对哪些列进行更新。

我们想把id大于1的用户的年龄修改为99，则可以使用如下写法：**set()方法操作的是sql语句中set语句的字段**

~~~~java
    @Test
    public void testUpdateWrapper(){
        UpdateWrapper<User> updateWrapper = new UpdateWrapper<>(); // update user set {} = {}
        updateWrapper.gt("id",1);// where id > 1
        updateWrapper.set("age",99);// set age = 99
        userMapper.update(null,updateWrapper);// update user set age = 99 where id > 1
    }
~~~~

### LambdaQueryWrapper

​	我们前面在使用条件构造器时列名都是用字符串的形式去指定。这种方式**无法在编译期确定列名的合法性**。

​	所以MP提供了一个Lambda条件构造器可以让我们直接**以实体类的方法引用的形式来指定列名**。这样就可以弥补上述缺陷。



如果使用之前的条件构造器写法如下

~~~~java
    @Test
    public void testLambdaWrapper(){
        QueryWrapper<User> queryWrapper = new QueryWrapper();
        queryWrapper.gt("age",18);
        queryWrapper.eq("address","狐山");
        List<User> users = userMapper.selectList(queryWrapper);
    }
~~~~

如果使用Lambda条件构造器写法如下

~~~~java
    @Test
    public void testLambdaWrapper2(){
        LambdaQueryWrapper<User> queryWrapper = new LambdaQueryWrapper<>();
        queryWrapper.gt(User::getAge,18);
        queryWrapper.eq(User::getAddress,"狐山");
        List<User> users = userMapper.selectList(queryWrapper);
    }
~~~~



## 3 IService接口与ServiceImpl实现类

​	MP也为我们**提供了Service层的实现**。我们只需要编写一个接口，继承`IService接口`(提供了很多常用的单表操作的方法)，并创建一个接口实现类继承`ServiceImpl`(作为IService接口的实现类)，即可使用。

​	相比于Mapper接口，**Service层主要是支持了更多批量操作的方法**。

如：`articleService.updateBatchById(articles);`根据id批量更新文章的数据

### 使用

接口

~~~~java
public interface UserService extends IService<User> {

}
~~~~

实现类

~~~~java
@Service // UserMapper、UserService、UserServiceImpl 一起出现
public class UserServiceImpl extends ServiceImpl<UserMapper,User> implements UserService {
	
}

~~~~

### api的使用

测试

~~~~java
    @Autowired
    private UserService userService;


    @Test
    public void testSeervice(){
        // select * from user
        List<User> list = userService.list();
        
        // select * from category where id in (categoryIds)
        List<Category> categories = CategoryService.listByIds(categoryIds);
        
        // selece * from category where id = ?
        Category category = categoryService.getById(categoryId);
        // insert into commemt(*) values(*)
        CommentService.save(comment);
        // update table set ? = ? , ? = ?... where id = ?
        updateById(user);
    }

    private boolean isUsernameExist(String userName) {
        LambdaQueryWrapper<User> queryWrapper = new LambdaQueryWrapper<>();
        queryWrapper.eq(User::getUserName,userName);
        // select count(?) from user where username = ?
        return count(queryWrapper) > 0;
    }
~~~~

