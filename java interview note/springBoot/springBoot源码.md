# springBoot源码

## 1 源码相关内容

### 1.1 SpringBoot项目中的Spring容器是在什么时候创建和刷新的？

**结论**1：在springBoot的启动类中调用SpringApplication的run方法时会根据容器的类型(响应式开发？)创建不同的容器对象，并且调用容器的refresh方法(刷新容器后就能够获取到Bean了)

**口述**：

### 1.2 探究Spring Boot项目中我们没有设置组件扫描的包为什么它会默认扫描启动类所在的包

**结论**1：在容器刷新时会调用BeanFactoryPostProcessor(Bean工厂后置处理器)进行处理。其中就有一个ConfigurationClassPostProcessor(配置类处理器)。在这个处理器中使用ConfigurationClassParser(配置类解析器)的parse方法去解析处理我们的配置类，其中就有对ComponentScan注解的解析处理。会去使用ComponentScanAnnotationParser的parse方法进行解析。解析时如果发现没有配置basePackage,它会去获取我们加了注解的
这个类所在的包，作为我们的basepackage进行组件扫描。



**口述**：其实就是有一个BeanFactoryPostProcessor(一个Bean工厂的后置处理器)，里面有一个

配置类处理器，它里面会去处理一个配置类，会去获取对应的ComponentScan注解,然后获取里面的一个basePackage属性，如果我们没有设置这个属性的话，它就会把我们这个启动类所在的包作为我们的basepackage进行组件扫描。



### 1.3 @Import源码解析 

**结论**：ConfigurationClassParser的doProcessConfigurationClass方法在处理启动类的时候会先递归扫描其上所有的@Impot注解，获取其中的的value属性值添加到集合中。然后获取遍历这个集合判断他们是ImportSelector的子类还是ImportBeanDefinitionRegistrar的子类还是加了@Configuration的配置类。

(1)如果是ImportSelector的子类则会通过反射去创建其对象，调用其方法获取字符串数组，进行过滤后封装成SourceClass对象。然后递归调用这些处理import的方法，对这些期望导入的配置类调用processConfigurationClass方法来进行解析。

(2)如果是ImportBeanDefinitionRegistrar的子类则...

(3)实际上源码没有对是否添加了@Configuration注解进行判断而是直接使用了else语句，如果前面两种情况都不满足就按照配置类的来(所以最好只用@Import来注入配置类)。会继续调用processConfigurationClass方法来解析这个导入进来的配置类。

