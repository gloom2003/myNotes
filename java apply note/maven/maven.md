# Maven的使用

### pom文件

那么首先，我们需要了解一下POM文件，它相当于是我们整个Maven项目的配置文件，它也是使用XML编写的：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>org.example</groupId>
    <artifactId>MavenTest</artifactId>
    <version>1.0-SNAPSHOT</version>

    <properties>
        <maven.compiler.source>8</maven.compiler.source>
        <maven.compiler.target>8</maven.compiler.target>
    </properties>

</project>
```

我们可以看到，Maven的配置文件是以`project`为根节点，而`modelVersion`定义了当前模型的版本，一般是4.0.0，我们不用去修改。

`groupId`、`artifactId`、`version`这三个元素合在一起，用于唯一区别每个项目，别人如果需要将我们编写的代码作为依赖，那么就必须通过这三个元素来定位我们的项目，我们称为一个项目的基本坐标，所有的项目一般都有自己的Maven坐标，因此我们通过Maven导入其他的依赖只需要填写这三个基本元素就可以了，无需再下载Jar文件，而是Maven自动帮助我们下载依赖并导入。

- `groupId` 一般用于指定组名称，命名规则一般和包名一致，比如我们这里使用的是`org.example`，一个组下面可以有很多个项目。
- `artifactId` 一般用于指定项目在当前组中的唯一名称，也就是说在组中用于区分于其他项目的标记。
- `version` 代表项目版本，随着我们项目的开发和改进，版本号也会不断更新，就像LOL一样，每次赛季更新都会有一个大版本更新，我们的Maven项目也是这样，我们可以手动指定当前项目的版本号，其他人使用我们的项目作为依赖时，也可以根本版本号进行选择（这里的SNAPSHOT代表快照，一般表示这是一个处于开发中的项目，正式发布项目一般只带版本号）

`properties`中一般都是一些变量和选项的配置，我们这里指定了JDK的源代码和编译版本为1.8，无需进行修改。

### Maven依赖作用域

除了三个基本的属性用于定位坐标外，依赖还可以添加以下属性：

- **type**：依赖的类型，对于项目坐标定义的packaging。大部分情况下，该元素不必声明，其默认值为jar

- **scope**：依赖的范围（作用域，着重讲解）

- **optional**：标记依赖是否可选,默认在导入依赖时，不会导入可选的依赖  

  

  ```xml
  <optional>true</optional>
  ```
例如Mybatis要支持多种类型的日志，需要用到很多种不同的日志框架，因此需要导入这些依赖来做兼容，但是我们项目中并不一定会使用这些日志框架作为Mybatis的日志打印器，因此这些日志框架仅Mybatis内部做兼容需要导入使用，而我们可以选择不使用这些框架或是选择其中一个即可。

- **exclusions**：用来排除传递性依赖（一个项目有可能依赖于其他项目，就像我们的项目，如果别人要用我们的项目作为依赖，那么就需要一起下载我们项目的依赖，如Lombok）

  可以通过排除依赖来防止添加不必要的依赖：

  ```xml
  <dependency>
      <groupId>org.junit.jupiter</groupId>
      <artifactId>junit-jupiter</artifactId>
      <version>5.8.1</version>
      <scope>test</scope>
      <exclusions>
          <exclusion>
              <groupId>org.junit.jupiter</groupId>
              <artifactId>junit-jupiter-engine</artifactId>
          </exclusion>
      </exclusions>
  </dependency>
  ```

  我们这里演示了排除JUnit的一些依赖。

我们着重来讲解一下`scope`属性，它决定了依赖的作用域范围：

- **compile** ：为默认的依赖有效范围。如果在定义依赖关系的时候，没有明确指定依赖有效范围的话，则**默认**采用该依赖有效范围。此种依赖，在**编译、运行、测试时均有效**。

- **provided** ：在编译、测试时有效，但是在运行时无效，也就是说，项目在运行时，不需要此依赖，比如我们上面的Lombok，我们只需要在编译阶段使用它，编译完成后，实际上已经转换为对应的代码了，因此Lombok不需要在项目运行时也存在。

- **runtime** ：在运行、测试时有效，但是在编译代码时无效。比如我们如果需要自己写一个JDBC实现，那么肯定要用到JDK为我们指定的接口，但是实际上在运行时是不用自带JDK的依赖，因此只保留我们自己写的内容即可。

- **test** ：只在测试时有效，例如：JUnit，我们一般只会在测试阶段使用JUnit，而实际项目运行时，我们就用不到测试了

- **system**：如果我需要的依赖没有上传的远程仓库，而是只有一个Jar怎么办呢？我们可以使用system作用域和provided是一样的，但是它不是从远程仓库获取，而是直接导入本地Jar包：

  ```xml
  <dependency>
       <groupId>javax.jntm</groupId>
       <artifactId>lbwnb</artifactId>
       <version>2.0</version>
       <scope>system</scope>
       <systemPath>C://学习资料/4K高清无码/test.jar</systemPath>
  </dependency>
  ```

  比如上面的例子，如果scope为system，那么我们需要添加一个systemPath来指定jar文件的位置

### maven原理

Maven是如何进行依赖管理呢，以致于如此便捷的导入依赖，我们来看看Maven项目的依赖管理流程：

![img](https://image.itbaima.cn/markdown/2023/03/06/XYNMU5WCrZv9cwy.jpg)



通过流程图我们得知，一个项目依赖一般是存储在中央仓库中，也有可能存储在一些其他的远程仓库（私服），几乎所有的依赖都被放到了中央仓库中，因此，Maven可以直接从中央仓库中下载大部分的依赖（Maven第一次导入依赖是需要联网的），远程仓库中下载之后 ，会暂时存储在本地仓库，我们会发现我们本地存在一个`.m2`文件夹，这就是Maven本地仓库文件夹

### maven的继承关系

新建一个模块，来创建一个子项目：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <parent>
        <artifactId>MavenTest</artifactId>
        <groupId>org.example</groupId>
        <version>1.0-SNAPSHOT</version>
    </parent>
    <modelVersion>4.0.0</modelVersion>

    <artifactId>ChildModel</artifactId>

    <properties>
        <maven.compiler.source>8</maven.compiler.source>
        <maven.compiler.target>8</maven.compiler.target>
    </properties>

</project>
```

IDEA默认给我们添加了一个**parent节点**，表示此Maven项目是父Maven项目的子项目，子项目直接继承父项目的`groupId`，子项目会直接继承父项目的所有依赖，除非依赖添加了optional标签

我们还可以让**父Maven项目统一管理所有的依赖**，包括版本号等，子项目可以选取需要的作为依赖，而版本全由父项目管理，我们可以将`dependencies`全部放入`dependencyManagement`节点，这样父项目就完全作为依赖统一管理。

```xml
<dependencyManagement>
    <dependencies>
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <version>1.18.22</version>
            <scope>provided</scope>
        </dependency>
        <dependency>
            <groupId>org.junit.jupiter</groupId>
            <artifactId>junit-jupiter</artifactId>
            <version>5.8.1</version>
            <scope>test</scope>
        </dependency>
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>8.0.27</version>
        </dependency>
        <dependency>
            <groupId>org.mybatis</groupId>
            <artifactId>mybatis</artifactId>
            <version>3.5.7</version>
        </dependency>
    </dependencies>
</dependencyManagement>
```

**注意**：父项目配置`dependencyManagement`后，子项目的依赖失效了，因为现在父项目没有依赖，而是将所有的依赖进行集中管理。

子项目需要什么再导入什么即可，同时子项目无需指定版本，所有的版本全部由父项目决定，子项目只需要使用即可：

```xml
<dependencies>
    <dependency>
        <groupId>org.projectlombok</groupId>
        <artifactId>lombok</artifactId>
        <scope>provided</scope>
    </dependency>
</dependencies>
```

类似：maven项目以springBoot项目作为父项目，使用springBoot项目中的依赖管理来防止版本问题。

### maven常用命令（插件）

每个Maven项目都有一个生命周期，实际上这些是Maven的一些插件，每个插件都有各自的功能，比如Maven的LifeCycle中的：：

- `clean`命令，执行后会**清理**整个`target`文件夹，在之后编写`Springboot`项目时可以解决一些缓存没更新的问题。

- `validate`命令可以验证项目的可用性。

- `compile`命令可以将项目**编译**为.class文件。

- `install`命令可以将当前项目**下载到本地仓库，以供其他项目导入作为依赖使用**。

- `verify`命令可以按顺序执行每个默认生命周期阶段（`validate`，`compile`，`package`等）

- `test` 命令 

- 可以一键测试所有位于test目录下的测试案例，请注意有以下要求：

  - 测试类的名称必须是以`Test`结尾，比如`MainTest`
  - 测试方法上必须标注`@Test`注解，实测`@RepeatedTest`无效

  这是由于JUnit5比较新，我们需要重新配置插件升级到高版本，才能完美的兼容Junit5：

  ```xml
  <build>
      <plugins>
          <plugin>
              <groupId>org.apache.maven.plugins</groupId>
              <artifactId>maven-surefire-plugin</artifactId>
              <!-- JUnit 5 requires Surefire version 2.22.0 or higher -->
              <version>2.22.0</version>
          </plugin>
      </plugins>
  </build>
  ```

  创建一下测试用例，顺便带大家了解一下Junit5的一些比较方便的地方：

  ```java
  public class MainTest {
  
      //因为配置文件位于内部，我们需要使用Resources类的getResourceAsStream来获取内部的资源文件
      private static SqlSessionFactory factory;
  
      //在JUnit5中@Before被废弃，它被细分了：
      @BeforeAll // 所有测试案例执行之前都会执行一次这个方法，只会执行一次 (方法必须是static)
      // @BeforeEach 所有测试案例执行之前都会执行一次这个方法，每个案例开始之前都会执行一次
      @SneakyThrows
      public static void before(){
          factory = new SqlSessionFactoryBuilder()
                  .build(Resources.getResourceAsStream("mybatis.xml"));
      }
  
  
      @DisplayName("Mybatis数据库测试")  //自定义测试名称
      @RepeatedTest(3)  //自动执行多次测试
      public void test(){
          try (SqlSession sqlSession = factory.openSession(true)){
              TestMapper testMapper = sqlSession.getMapper(TestMapper.class);
              System.out.println(testMapper.getStudentBySid(1));
          }
      }
  }
  ```

  现在`@RepeatedTest`、`@BeforeAll`也能使用了。

- `package`命令 对项目的代码进行打包，生成jar文件。直接点击`packge`仅适用于打包自己编写的代码为Jar依赖的情况。如果我们需要打包一个可执行文件，那么我不仅需要将自己编写的类打包到Jar中，同时还需要**将使用到的别人的依赖也一并打包到Jar中**，因为我们使用了别人为我们提供的框架，自然也需要运行别人的代码，我们需要使用另一个插件来实现一起打包(springBoot项目下使用另外的插件，参考`springBoot的配置相关.md`)：

  ```xml
  <plugin>
      <artifactId>maven-assembly-plugin</artifactId>
      <version>3.1.0</version>
      <configuration>
          <descriptorRefs>
              <descriptorRef>jar-with-dependencies</descriptorRef>
          </descriptorRefs>
          <archive>
              <manifest>
                  <addClasspath>true</addClasspath>
                  <mainClass>com.test.Main</mainClass>
              </manifest>
          </archive>
      </configuration>
      <executions>
          <execution>
              <id>make-assembly</id>
              <phase>package</phase>
              <goals>
                  <goal>single</goal>
              </goals>
          </execution>
      </executions>
  </plugin>
  ```

  **在打包之前也会执行一次test命令**，来保证项目能够正常运行，当测试出现问题时，打包将无法完成，我们也可以手动跳过，选择`执行Maven目标`来手动执行Maven命令，输入`mvn package -Dmaven.test.skip=true`来以跳过测试的方式进行打包。

  最后得到我们的Jar文件，在cmd中打开同级目录下输入`java -jar xxxx.jar`来运行我们打包好的Jar可执行程序（xxx代表文件名称）

  - `deploy`命令用于发布项目到本地仓库和远程仓库，一般情况下用不到，这里就不做讲解了。
  - `site`命令用于生成当前项目的发布站点，暂时不需要了解。



关于多模块项目，多模块下父项目存在一个`packing`打包类型标签，**所有的父级项目的packing都为pom，packing默认是jar类型**，如果不作配置，maven会将该项目打成jar包。作为父级项目，还有一个重要的属性，那就是modules，通过modules标签将项目的所有子项目引用进来，在build父级项目时，会根据子模块的相互依赖关系整理一个build顺序，然后依次build。

参考`springBoot的配置相关.md`中的多模块项目相关