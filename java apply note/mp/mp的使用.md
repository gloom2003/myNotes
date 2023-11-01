# mp的使用

## 1:mp的配置

### 1.1在application.yml中的相关配置

~~~xml
mybatis-plus:
  configuration:
    # 配置日志实现类
    log-impl: org.apache.ibatis.logging.stdout.StdOutImpl
  global-config:
    db-config:
		#逻辑删除相关的设置
      logic-delete-field: delFlag
      logic-delete-value: 1
      logic-not-delete-value: 0
		#设置id增长类型为自动递增
      id-type: auto
#
~~~

### 1.2导入mp的依赖

~~~xml
        <!--mybatisPlus依赖-->
        <dependency>
            <groupId>com.baomidou</groupId>
            <artifactId>mybatis-plus-boot-starter</artifactId>
            <version>3.4.3</version>
        </dependency>

~~~

### 1.3mp开启分页查询的支持

~~~java
@Configuration
public class MbatisPlusConfig {

    /**
     * 3.4.0之后版本
     * MP支持分页配置
     * @return
     */
    @Bean
    public MybatisPlusInterceptor mybatisPlusInterceptor() {
        MybatisPlusInterceptor mybatisPlusInterceptor = new MybatisPlusInterceptor();
        mybatisPlusInterceptor.addInnerInterceptor(new PaginationInnerInterceptor());
        return mybatisPlusInterceptor;
    }
}
~~~



## 2:配置MP字段update与insert时自动填充fill

		### 2.1 自定义一个类实现MetaObjectHandler接口重写对应的方法即可



~~~java

/***
 * 配置MP字段自动填充,自定义一个类实现MetaObjectHandler接口重写对应的方法即可
 */
@Component
public class MyMetaObjectHandler implements MetaObjectHandler {
    /***
     * 配置执行插入语句时自动更新一些公共的字段
       当执行插入语句时就会调用此方法
     * @param metaObject
     */
    @Override
    public void insertFill(MetaObject metaObject) {
        Long userId = null;
        try {
            userId = SecurityUtils.getUserId();
        } catch (Exception e) {
            //处理注册时执行insert用户语句时，还没有登录导致获取不到userId时发生的异常
            e.printStackTrace();
            userId = -1L;//表示是自己创建
        }
        this.setFieldValByName("createTime", new Date(), metaObject);
        this.setFieldValByName("createBy",userId , metaObject);
        this.setFieldValByName("updateTime", new Date(), metaObject);
        this.setFieldValByName("updateBy", userId, metaObject);
    }

    /***
     * 配置执行更新语句时自动更新一些公共的字段
     * @param metaObject
     */
    @Override
    public void updateFill(MetaObject metaObject) {
        this.setFieldValByName("updateTime", new Date(), metaObject);
        this.setFieldValByName("updateBy", SecurityUtils.getUserId(), metaObject);
    }
}
~~~

### 2.2在对应的实体类上打上相应的注解

~~~java
    @TableField(fill = FieldFill.INSERT)
    private Long createBy;

    @TableField(fill = FieldFill.INSERT)
    private Date createTime;

    @TableField(fill = FieldFill.INSERT_UPDATE)
    private Long updateBy;

    @TableField(fill = FieldFill.INSERT_UPDATE)
    private Date updateTime;
~~~

## 3:使用xml映射文件和对应的mapper接口实现使用自定义的sql

例如：MenuMapper与MenuMapper.xml

**注意：MenuMapper.xml映射文件的位置在application.yml中默认是resources/mapper/MenuMapper.xml，如果需要修改，在application.yml中重新进行指定即可**



~~~xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd" >
<!--指定类路径    mapper接口名与Mybatis的映射文件名一定要一模一样。-->
<mapper namespace="com.kana.mapper.MenuMapper">
    <!--指定执行sql的类型、方法名与返回类型-->
    <select id="selectPermsByUserId" resultType="java.lang.String">
        SELECT DISTINCT perms
        FROM sys_user_role ur LEFT JOIN sys_role_menu rm
        ON ur.`role_id` = rm.`role_id`
        LEFT JOIN sys_menu m
        ON m.`id` = rm.`menu_id`
        WHERE ur.`user_id` = #{userId} AND
        m.`status` = 0 AND
        m.`menu_type` IN ('C','F') AND
        m.`del_flag` = 0
    </select>

    <select id="selectMenuByUserId" resultType="com.kana.domain.entity.Menu">
    SELECT m.*
    FROM sys_user_role ur LEFT JOIN sys_role r
    ON ur.`role_id` = r.`id`
    LEFT JOIN sys_role_menu rm
    ON rm.`role_id` = r.`id`
    LEFT JOIN sys_menu m
    ON m.`id` = rm.`menu_id`
    WHERE m.`del_flag` = 0 AND
    m.`status` = 0 AND
    m.`menu_type` IN ('C','M') AND
    ur.`user_id` = #{userId}
    ORDER BY
    m.parent_id,m.order_num
    </select>
</mapper>

~~~

## 4:其他使用可见sanGengBlogBugs中的mp相关的bug



## 5实体类相关注解的使用

~~~java
@Data
@AllArgsConstructor
@NoArgsConstructor
@Accessors(chain = true)//开启链式调用
@TableName("sg_article")//对应数据库中名字为sg_article的表
public class Article{
    @TableId//主键id,只能有一个，但是数据库表可能有两个主键
    private Long id;
    //标题
    private String title;
    //文章内容
    private String content;
    //文章摘要
    private String summary;
    //所属分类id
    private Long categoryId;
    //缩略图
    private String thumbnail;
    //是否置顶（0否，1是）
    private String isTop;
    //状态（0已发布，1草稿）
    private String status;
    //访问量
    private Long viewCount;
    //是否允许评论 1是，0否
    private String isComment;
	//使用mp自动填充
    @TableField(fill = FieldFill.INSERT)
    private Long createBy;

    @TableField(fill = FieldFill.INSERT)
    private Date createTime;

    @TableField(fill = FieldFill.INSERT_UPDATE)
    private Long updateBy;

    @TableField(fill = FieldFill.INSERT_UPDATE)
    private Date updateTime;

    //删除标志（0代表未删除，1代表已删除）
    private Integer delFlag;
    //注解表示String categoryName不在数据库表中
    @TableField(exist = false)
    private String categoryName;

    public Article(Long id, Long viewCount) {
        this.id = id;
        this.viewCount = viewCount;
    }
}


~~~

