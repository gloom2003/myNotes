# java基础

## ① 基础概念与常识

### 3级 1 Java 语言有哪些特点?
1. 面向对象（封装，继承，多态）；
2. 平台无关性（ Java 虚拟机实现平台无关性）；一次编译，处处运行。
3. 支持多线程（ 旧版C++ 语言没有内置的多线程机制，因此必须调用操作系统的多线程功能来进行多线程程序设计，现在新版c++也提供了多线程支持，而 Java 语言却提供了多线程支持）；
4. 可靠性（具备异常处理和自动内存管理机制）；
5. 安全性（Java 语言本身的设计就提供了多重安全防护机制如访问权限修饰符、限制程序直接访问操作系统资源）；
6. 高效性（通过 Just In Time 编译器等技术的优化，Java 语言的运行效率还是非常不错的）；
7. 支持网络编程并且很方便；
8. 编译与解释并存；

### 2 java SE && java EE

**java SE**: Java 平台标准版，Java 编程语言的基础，它包含了支持 Java 应用程序开发和运行的核心类库以及虚拟机等核心组件，更适合开发简单的服务器应用程序

**Java EE**：Java 平台企业版，建立在 Java SE 的基础上，提供了更多的标准与规范，如:Servlet、JSP、JDBC、JPA 更适合开发复杂的Web 应用程序。


### 3 JVM vs JDK vs JRE


#### JVM
Java 虚拟机（JVM）是运行 Java 字节码的虚拟机。JVM 有针对不同系统的特定实现（Windows，Linux，macOS），目的是使用相同的字节码，它们都会给出相同的结果。字节码和不同系统的 JVM 实现是 Java 语言“一次编译，随处可以运行”的关键所在。


#### JRE

JRE（Java Runtime Environment） 是 Java 运行时环境。它是运行已编译 Java 程序所需的所有内容的集合，主要包括 Java 虚拟机（JVM）、Java 基础类库（Class Library）。

#### JDK

JDK（Java Development Kit），它是功能齐全的 Java SDK，是提供给开发者使用的，能够创建和编译 Java 程序。他包含了 JRE，同时还包含了编译 java 源码的编译器 javac 以及一些其他工具比如 javadoc（文档注释工具）、javap（反编译工具）等等。如果需要进行 Java 编程工作，比如编写和编译 Java 程序，就需要安装 JDK。

#### JDK9后的新特性
不过，从 JDK 9 开始，就不需要区分 JDK 和 JRE 的关系了，取而代之的是模块系统（JDK 被重新组织成 94 个模块）+ jlink 工具

在引入了模块系统之后，JDK 被重新组织成 94 个模块。Java 应用可以通过新增的 jlink 工具，定制出只包含所依赖的 JDK 模块的自定义运行时镜像。这样可以极大的减少 Java 运行时环境的大小。
也就是说，可以用 jlink 根据自己的需求，创建一个更小的 runtime（运行时），而不是不管什么应用，都是同样的 JRE。

### 4 Java程序转变为机器代码的过程

```
.java -＞.class -＞机器码
```
#### 编译与解释共存

我们需要格外注意的是 .class->机器码 这一步。在这一步 JVM 类加载器首先加载字节码文件，然后通过解释器逐行解释执行，这种方式的执行速度会相对比较慢。而且，有些方法和代码块是经常需要被调用的(也就是所谓的热点代码)，所以后面引进了 JIT（Just in Time Compilation） 编译器，而 JIT 属于运行时编译。当 JIT 编译器完成第一次编译后，其会将字节码对应的机器码保存下来，下次可以直接使用。而我们知道，机器码的运行效率肯定是高于 Java 解释器的。这也解释了我们为什么经常会说 Java 是编译(JIT的运行时编译)与解释(解释器逐行解释)共存的语言 。

JVM 会根据代码每次被执行的情况收集信息并相应地做出一些优化，因此执行的次数越多，它的速度就越快。

#### 编译型语言 && 解释型语言
我们可以将高级编程语言按照程序的执行方式分为两种：
- 编译型：编译型语言 会通过编译器将源代码一次性翻译成可被该平台执行的机器码。常见的编译性语言有 C、C++、Go、Rust 等等

- 解释型：解释型语言会通过解释器一句一句的将代码解释（interpret）为机器代码后再执行。常见的解释性语言有 Python、JavaScript、PHP 等等。


### 为什么说 Java 语言“编译与解释并存”？
因为 Java 程序要经过先编译，后解释两个步骤
:
编译: .java-＞.class
解释: .class-＞机器码

这是因为 Java 语言既具有编译型语言的特征，也具有解释型语言的特征。因为 Java 程序要经过先编译，后解释两个步骤，由 Java 编写的程序需要先经过编译步骤，生成字节码（.class 文件），然后再经过.class->机器码 这一步。在这一步 JVM 类加载器首先加载字节码文件，然后通过解释器逐行解释执行。

### Java 和 C++ 的区别?
虽然，Java 和 C++ 都是面向对象的语言，都支持封装、继承和多态，但是，它们还是有挺多不相同的地方：
- Java 不提供指针来直接访问内存，程序内存更加安全
- Java 的类是单继承的，C++ 支持多重继承；虽然 Java 的类不可以多继承，但是接口可以多继承。Java 有自动内存管理垃圾回收机制(GC)，不需要程序员手动释放无用内存。
- C ++同时支持方法重载和操作符重载，但是 Java 只支持方法重载（操作符重载增加了复杂性，这与 Java 最初的设计思想不符）。

### Java 的三种移位运算符
- `<<` :左移运算符，向左移若干位，高位丢弃，低位补零。x << 1,相当于 x 乘以 2(不溢出的情况下)。

- `>>` :带符号右移，向右移若干位，高位补符号位，低位丢弃。正数高位补 0,负数高位补 1。x >> 1,相当于 x 除以 2。

- `>>>` :无符号右移，忽略符号位，空位都以 0 补齐。


由于 double，float 在二进制中的表现比较特殊，因此不能来进行移位操作。移位操作符实际上支持的类型只有int和long，编译器在对short、byte、char类型进行移位前，都会将其转换为int类型再操作。

左移/右移 32 位相当于不进行移位操作（32%32=0），左移/右移 42 位相当于左移/右移 10 位（42%32=10）。当 long 类型进行左移/右移操作时，由于 long 对应的二进制是 64 位，因此求余操作的基数也变成了 64。

## ② 基本数据类型

### Java 中的几种基本数据类型了解么？

Java 中有 8 种基本数据类型，分别为：
- 6 种数字类型： 4 种整数型：byte 1字节 取值范围:-128 ~ 127、short 2字节、int 4字节、long 8字节 2 种浮点型：float 4字节、double 8字节
- 1 种字符类型：char 2字节 16位
- 1 种布尔型：boolean 1位。

注意：
Java 里使用 long 类型的数据一定要在数值后面加上 L，否则将作为整型解析。

在二进制补码表示法中，最高位是用来表示符号的（0 表示正数，1 表示负数），其余位表示数值部分。所以，如果我们要表示最大的正数，我们需要把除了最高位之外的所有位都设为 1。如果我们再加 1，就会导致溢出，变成一个负数。

这八种基本类型都有对应的包装类分别为：Byte、Short、Integer、Long、Float、Double、Character、Boolean 。

### 基本类型和包装类型的区别？
1 包装类型可用于泛型，而基本类型不可以。

2 基本数据类型的非静态成员变量与几乎所有对象实例都存在于堆中。

 基本数据类型的局部变量存放在 Java 虚拟机栈中的局部变量表中，基本数据类型的成员变量（未被 static 修饰 ）存放在 Java 虚拟机的堆中。包装类型属于对象类型，我们知道几乎所有对象实例都存在于堆中。拓展:变量、函数在栈中开辟内存，常量存储在常量池中。

3 占用空间：相比于包装类型（对象类型）， 基本数据类型占用的空间往往非常小。

4 默认值：成员变量包装类型不赋值就是 null ，而基本类型有默认值且不是 null。

5 比较方式：对于基本数据类型来说，== 比较的是值。对于包装数据类型来说，== 比较的是对象的内存地址。所有整型包装类对象之间值的比较，全部使用 equals() 方法。

### 包装类型的缓存机制了解么？

Java 基本数据类型的包装类型除了Float,Double都用到了缓存机制来提升性能。

Byte,Short,Integer,Long 这 4 种包装类默认创建了一个字节:数值 [-128，127] 的相应类型的缓存数据，Character 创建了数值在 [0,127] 范围的缓存数据(只有正数)，Boolean 直接返回 True or False。

如果超出对应范围仍然会去new 创建新的对象，缓存的范围区间的大小只是在性能和资源之间的权衡。(缓存太多数值占用内存资源就会导致性能下降)

获取同一个数值，每次都走缓存的话，获取的都是同一个缓存对象，与new 一个新的对象地址不同，如:

```
Integer i1 = 33;
Integer i2 = 33;
System.out.println(i1 == i2);// 输出 true
```

会发生自动装箱: `Integer i1=Integer.valueOf(33)`


### 自动装箱与拆箱了解吗？原理是什么？
1. 装箱：将基本类型包装起来，包装成对应的包装类型；
2. 拆箱：将包装类型的包装拆开来转换为对应的基本数据类型

装箱其实就是调用了 包装类的valueOf()方法，拆箱其实就是调用了 xxxValue()方法。

如果频繁拆装箱的话，也会严重影响系统的性能。我们应该尽量避免不必要的拆装箱操作。

#### 如何解决浮点数运算的精度丢失问题？

BigDecimal 可以实现对浮点数的运算，不会造成精度丢失。通常情况下，大部分需要浮点数精确运算结果的业务场景（比如涉及到钱的场景）都是通过 BigDecimal 来做的。

#### 静态变量有什么作用？
静态变量也就是被 static 关键字修饰的变量。它可以被类的所有实例共享，无论一个类创建了多少个对象，它们都共享同一份静态变量。也就是说，静态变量只会被分配一次内存，即使创建多个对象，这样可以节省内存。

通常情况下，静态变量会被 final 关键字修饰成为常量。

常量: static final...
#### 静态方法为什么不能调用非静态成员?

这个需要结合 JVM 的相关知识，主要原因如下：静态方法是属于类的，在类加载的时候就会分配内存，可以通过类名直接访问。
而非静态成员属于实例对象，只有在对象实例化之后才存在，需要通过类的实例对象去访问。

在对象还没有实例化时静态方法就已经存在了，此时调用在内存中还不存在的非静态成员，属于非法操作。

#### 方法的重写要遵循“两同两小一大”
“两同”即方法名相同、形参列表相同；“两小”指的是子类方法返回值类型应比父类方法返回值类型更小或相等，子类方法声明抛出的异常类应比父类方法声明抛出的异常类更小或相等；“一大”指的是子类方法的访问权限应比父类方法的访问权限更大或相等。
![img](file:///C:UsersGLOOMAppDataRoamingTencentQQTempSysT_ZKW6KJ_X{%[%P_AY$`(]X.png) 关于 重写的返回值类型 这里需要额外多说明一下，上面的表述不太清晰准确：如果方法的返回类型是 void 和基本数据类型，则返回值重写时不可修改。但是如果方法的返回值是引用类型，重写时是可以返回该引用类型的子类的。



## ③ 面向对象基础

#### 面向对象和面向过程的区别
两者的主要区别在于解决问题的方式不同：面向过程把解决问题的过程拆成一个个方法，通过一个个方法的执行解决问题。面向对象会先抽象出对象，然后用对象执行方法的方式解决问题。另外，面向对象开发的程序一般更易维护、易复用、易扩展。


#### 面向对象三大特征
##### 封装

封装是指把一个对象的状态信息（也就是属性）隐藏在对象内部，不允许外部对象直接访问对象的内部信息。但是可以提供一些可以被外界访问的方法来操作属性。

通过使用继承，可以快速地创建新的类，可以提高代码的重用，程序的可维护性，节省大量创建新类的时间 ，提高我们的开发效率。
##### 继承
关于继承如下 3 点请记住：
- 子类拥有父类对象所有的属性和方法（包括私有属性和私有方法），但是父类中的私有属性和方法子类是无法访问，只是拥有。
- 子类可以拥有自己属性和方法，即子类可以对父类进行扩展。
- 子类可以用自己的方式实现父类的方法。

##### 多态

多态，顾名思义，表示一个对象具有多种的状态，具体表现为父类的引用指向子类的实例。
多态的特点:
- 对象类型和引用类型之间具有继承（类）/实现（接口）的关系；
- 引用类型变量发出的方法调用的到底是哪个类中的方法，必须在程序运行期间才能确定；
- 多态不能调用“只在子类存在但在父类不存在”的方法；强转为子类后才可以调用。
- 如果子类重写了父类的方法，真正执行的是子类覆盖的方法，如果子类没有覆盖父类的方法，执行的是父类的方法。

#### 接口和抽象类有什么共同点和区别？
##### 共同点：
- 都不能被实例化。
- 都可以包含抽象方法。
- 都可以有默认实现的方法（Java 8 可以用 default 关键字在接口中定义默认方法）。

##### 区别：
- 接口主要用于对类的行为进行约束，你实现了某个接口就具有了对应的行为。抽象类主要用于代码复用，强调的是所属关系。
- 一个类只能继承一个类，但是可以实现多个接口。
- 接口中的成员变量只能是 public static final 类型的，不能被修改且必须有初始值，而抽象类的成员变量默认 default，可在子类中被重新定义，也可被重新赋值。

#### 深拷贝和浅拷贝区别了解吗？什么是引用拷贝？

- 浅拷贝：浅拷贝会在堆上创建一个新的对象（区别于引用拷贝的一点），不过，如果原对象内部的属性是引用类型的话，浅拷贝会直接复制原对象的引用地址，也就是说拷贝对象和原对象共用同一个内部对象。
- 深拷贝：深拷贝会完全复制整个对象，包括这个对象所包含的内部属性中引用类型的对象。

那什么是引用拷贝呢？ 简单来说，引用拷贝就是两个不同的引用指向同一个对象。

## ④ Object

#### Object 类的常见方法有哪些？
getClass() native 方法，用于返回当前运行时对象的 Class 对象，使用了 final 关键字修饰，故不允许子类重写。

public native int hashCode() native 方法，用于返回对象的哈希码，主要使用在哈希表中，比如 JDK 中的HashMap。

 #### == 和 equals() 的区别

 == 对于基本类型和引用类型的作用效果是不同的：对于基本数据类型来说，== 比较的是值。对于引用数据类型来说，== 比较的是对象的内存地址。因为 Java 只有值传递，所以，对于 == 来说，不管是比较基本数据类型，还是引用数据类型的变量，其本质比较的都是值，只是引用类型变量存的值是对象的地址。

截取了一部分
另外需要注意的是：Object 的 hashCode() 方法是本地方法，也就是用 C 语言或 C++ 实现的。

总结下来就是：
- 如果两个对象的hashCode 值相等，那这两个对象不一定相等（哈希碰撞）。
- 如果两个对象的hashCode 值相等并且equals()方法也返回 true，我们才认为这两个对象相等。
- 如果两个对象的hashCode 值不相等，我们就可以直接认为这两个对象不相等。

ps: 哈希碰撞也就是指的是不同的对象得到相同的 hashCode

其实， **hashCode() 和 equals()都是用于比较两个对象是否相等**。
那为什么 JDK 还要同时提供这两个方法呢？
这是因为在一些容器（比如 HashMap、HashSet）中，有了 hashCode() 之后，判断元素是否在对应容器中的效率会更高（参考添加元素进HashSet的过程）:
当你把对象加入 HashSet 时，HashSet 会先计算对象的 hashCode 值来判断对象加入的位置，同时也会与其他已经加入的对象的 hashCode 值作比较，如果没有相符的 hashCode，HashSet 会假设对象没有重复出现。但是如果发现有相同 hashCode 值的对象，这时会调用 equals() 方法来检查 hashCode 相等的对象是否真的相同。如果两者相同，HashSet 就不会让其加入操作成功。如果不同的话，就会重新散列到其他位置。这样我们就大大减少了 equals 的次数，相应就大大提高了执行速度。

而不是每添加一个对象就遍历之前的所有的对象然后调用equals方法判断两个对象是否相等。


#### 为什么重写 equals() 时必须重写 hashCode() 方法？



## ⑤ String

#### String、StringBuffer、StringBuilder 的区别？
##### 可变性

String 是不可变的
String 真正不可变有下面几点原因：保存字符串的数组被 final 修饰且为私有的，并且String 类没有提供/暴露修改这个字符串的方法。String 类被 final 修饰导致其不能被继承，进而避免了子类破坏 String 不可变。

##### 线程安全性
- String 中的对象是不可变的，也就可以理解为常量，线程安全。
- StringBuffer 对方法加了同步锁，所以是线程安全的。
- StringBuilder 并没有对方法进行加同步锁，所以是非线程安全的。

##### 性能
- 每次对 String 类型进行改变的时候，都会生成一个新的 String 对象，然后将指针指向新的 String 对象。
- StringBuffer 每次都会对 StringBuffer 对象本身进行操作，而不是生成新的对象并改变对象引用。
- 相同情况下使用 StringBuilder 相比使用 StringBuffer 仅能获得 10%~15% 左右的性能提升，但却要冒多线程不安全的风险。
注意:
在 Java 9 之后，String、StringBuilder 与 StringBuffer 的实现改用 byte 数组存储字符串。

#### final关键字
我们知道被 final 关键字修饰的类不能被继承，修饰的方法不能被重写，修饰的变量是基本数据类型则值不能改变，修饰的变量是引用类型则不能再指向其他对象。

#### 字符串拼接用“+” 还是 StringBuilder

字符串对象通过“+”的字符串拼接方式，实际上是通过 StringBuilder 调用 append() 方法实现的，拼接完成之后调用 toString() 得到一个 String 对象 。

#### 字符串常量池的作用了解吗？
字符串常量池 是 JVM 为了提升性能和减少内存消耗针对字符串（String 类）专门开辟的一块区域，主要目的是为了避免字符串的重复创建。

#### String s1 = new String("abc");这句话创建了几个字符串对象？

## ⑥ Java的异常

#### Exception 和 Error 有什么区别？
在 Java 中，所有的异常都有一个共同的祖先 java.lang 包中的 Throwable 类。Throwable 类有两个重要的子类:
- Exception :程序本身可以处理的异常，可以通过 catch 来进行捕获。Exception 又可以分为 Checked Exception (受检查异常，必须处理) 和 Unchecked Exception (不受检查异常，可以不处理)。
- Error：Error 属于程序无法处理的错误 ，我们没办法通过 catch 来进行捕获不建议通过catch捕获 。例如 Java 虚拟机运行错误（Virtual MachineError）、虚拟机内存不够错误(OutOfMemoryError)、类定义错误（NoClassDefFoundError）等 。这些异常发生时，Java 虚拟机（JVM）一般会选择线程终止。

#### Checked Exception 和 Unchecked Exception 有什么区别？
- Checked Exception 即 受检查异常 ，Java 代码在编译过程中，如果受检查异常没有被 catch或者throws 关键字处理的话，就没办法通过编译。
- Unchecked Exception 即 不受检查异常 ，Java 代码在编译过程中 ，我们即使不处理不受检查异常也可以正常通过编译。

#### Throwable 类常用方法有哪些？
- String getMessage(): 返回异常发生时的简要描述
- String toString(): 返回异常发生时的详细信息
- String getLocalizedMessage(): 返回异常对象的本地化信息。使用 Throwable 的子类覆盖这个方法，可以生成本地化信息。如果子类没有覆盖该方法，则该方法返回的信息与 getMessage()返回的结果相同
- void printStackTrace(): 在控制台上打印 Throwable 对象封装的异常信息



#### try-catch-finally 如何使用？

- `try`块：用于捕获异常。其后可接零个或多个 `catch` 块，如果没有 `catch` 块，则必须跟一个 `finally` 块。
- `catch`块：用于处理 try 捕获到的异常。
- `finally` 块：无论是否捕获或处理异常，`finally` 块里的语句都会被执行。**当在 `try` 块或 `catch` 块中遇到 `return` 语句时，`finally` 语句块将在方法返回之前被执行。**

代码示例：



```java
try {
    System.out.println("Try to do something");
    throw new RuntimeException("RuntimeException");
} catch (Exception e) {
    System.out.println("Catch Exception -> " + e.getMessage());
} finally {
    System.out.println("Finally");
}
```

输出：



```plain
Try to do something
Catch Exception -> RuntimeException
Finally
```

**注意：不要在 finally 语句块中使用 return!** 当 try 语句和 finally 语句中都有 return 语句时，try 语句块中的 return 语句会被忽略。这是因为 **try 语句中的 return 返回值会先被暂存在一个本地变量中**，当执行到 finally 语句中的 return 之后，这个本地变量的值就变为了 finally 语句中的 return 返回值。

代码示例：



```java
public static void main(String[] args) {
    System.out.println(f(2));
}

public static int f(int value) {
    try {
        return value * value;
    } finally {
        if (value == 2) {
            return 0;
        }
    }
}
```

输出：



```plain
0
```



#### finally 中的代码一定会执行吗？

不一定的！在某些情况下，finally 中的代码不会被执行。

就比如说 finally 之前虚拟机被终止运行的话，finally 中的代码就不会被执行。



```java
try {
    System.out.println("Try to do something");
    throw new RuntimeException("RuntimeException");
} catch (Exception e) {
    System.out.println("Catch Exception -> " + e.getMessage());
    // 终止当前正在运行的Java虚拟机
    System.exit(1);
} finally {
    System.out.println("Finally");
}
```

输出：



```plain
Try to do something
Catch Exception -> RuntimeException
```

所以，在以下 3种特殊情况下，`finally` 块的代码也不会被执行：

1. 程序所在的线程死亡。
2. 关闭 CPU。
3.  finally 之前虚拟机被终止运行 调用`System.exit(1)`方法

#### 如何使用 `try-with-resources` 代替`try-catch-finally`？

1. **适用范围（资源的定义）：** 任何实现 `java.lang.AutoCloseable`或者 `java.io.Closeable` 的对象
2. **关闭资源和 finally 块的执行顺序：** 在 `try-with-resources` 语句中，**任何 catch 或 finally 块在声明的资源关闭后运行**



Java 中类似于`InputStream`、`OutputStream`、`Scanner`、`PrintWriter`等的资源都需要我们调用`close()`方法来手动关闭，一般情况下我们都是通过`try-catch-finally`语句来实现这个需求，如下：



```java
//读取文本文件的内容
Scanner scanner = null;
try {
    scanner = new Scanner(new File("D://read.txt"));
    while (scanner.hasNext()) {
        System.out.println(scanner.nextLine());
    }
} catch (FileNotFoundException e) {
    e.printStackTrace();
} finally {
    if (scanner != null) {
        scanner.close();
    }
}
```

使用 Java 7 之后的 `try-with-resources` 语句改造上面的代码:



```java
try (Scanner scanner = new Scanner(new File("test.txt"))) {
    while (scanner.hasNext()) {
        System.out.println(scanner.nextLine());
    }
} catch (FileNotFoundException fnfe) {
    fnfe.printStackTrace();
}
```

当然多个资源需要关闭的时候，使用 `try-with-resources` 实现起来也非常简单，如果你还是用`try-catch-finally`可能会带来很多问题。

通过使用分号分隔，可以在`try-with-resources`块中声明多个资源。



```java
try (BufferedInputStream bin = new BufferedInputStream(new FileInputStream(new File("test.txt")));
     BufferedOutputStream bout = new BufferedOutputStream(new FileOutputStream(new File("out.txt")))) {
    int b;
    while ((b = bin.read()) != -1) {
        bout.write(b);
    }
}
catch (IOException e) {
    e.printStackTrace();
}
```



#### 异常使用有哪些需要注意的地方？

- 不要把异常定义为静态变量，因为这样会导致异常栈信息错乱。每次手动抛出异常，我们都需要手动 new 一个异常对象抛出。
- 抛出的异常信息一定要有意义。
- 建议抛出更加具体的异常比如字符串转换为数字格式错误的时候应该抛出`NumberFormatException`而不是其父类`IllegalArgumentException`。
- 使用日志打印异常之后就不要再抛出异常了（两者不要同时存在一段代码逻辑中）。



### ⑦ 泛型

#### 什么是泛型？有什么作用？

1 编译时必须传入指定的类型：

​		编译器可以对泛型参数进行检测，通过泛型参数可以指定传入的对象类型，如果传入其他类型的对象就会报错。

2 使用泛型后编译器会自动转换类型。

#### 泛型的使用方式有哪几种？

泛型一般有三种使用方式:**泛型类**、**泛型接口**、**泛型方法**。

**1.泛型类**：



```java
//此处T可以随便写为任意标识，常见的如T、E、K、V等形式的参数常用于表示泛型
// 在 java 中泛型只是一个占位符，必须在传递类型后才能使用。类在实例化时才能真正的传递类型参数
public class Generic<T>{

    private T key;

    public Generic(T key) {
        this.key = key;
    }

    public T getKey(){
        return key;
    }
}
```

如何实例化泛型类：



```java
Generic<Integer> genericInteger = new Generic<Integer>(123456);
```

**2.泛型接口**：



```java
public interface Generator<T> {
    public T method();
}
```

实现泛型接口，不指定类型：



```java
class GeneratorImpl<T> implements Generator<T>{
    @Override
    public T method() {
        return null;
    }
}
```

实现泛型接口，指定类型：



```java
class GeneratorImpl<T> implements Generator<String>{
    @Override
    public String method() {
        return "hello";
    }
}
```

**3.泛型方法**：



```java
   // 静态泛型方法
public static < E > void printArray( E[] inputArray )
   {
         for ( E element : inputArray ){
            System.out.printf( "%s ", element );
         }
         System.out.println();
    }
// 类在实例化时才能真正的传递类型参数，由于静态方法的加载先于类的实例化，也就是说类中的泛型还没有传递真正的类型参数，静态的方法的加载就已经完成了，所以静态泛型方法是没有办法使用类上声明的泛型的。只能使用自己声明的 <E>
```

使用：



```java
// 创建不同类型数组：Integer, Double 和 Character
Integer[] intArray = { 1, 2, 3 };
String[] stringArray = { "Hello", "World" };
printArray( intArray  );
printArray( stringArray  );
```



### ⑧ 反射

#### 何谓反射？

反射之所以被称为框架的灵魂，主要是因为它赋予了我们在运行时分析类以及执行类中方法的能力。通过反射你可以获取任意一个类的所有属性和方法，你还可以调用这些方法和属性。

#### 反射的优缺点？

优点：

1. 反射可以让我们的代码更加灵活、为各种框架提供开箱即用的功能提供了便利。

2. 反射让我们在运行时有了分析操作类的能力

缺点：

1. 安全问题，比如可以无视泛型参数的安全检查（泛型参数的安全检查发生在编译时）
2. 反射的性能也要稍差点

#### 反射的应用场景了解么？

 Spring/Spring Boot、MyBatis 等等框架中大量使用了**动态代理**，而动态代理的实现也依赖反射。

**注解** 的实现也用到了反射。



### ⑨ 注解

#### 何谓注解？

注解本质是一个继承了`Annotation` 的特殊接口：

```java
@Target(ElementType.METHOD)
@Retention(RetentionPolicy.SOURCE)
public @interface Override {

}

public interface Override extends Annotation{

}
```

#### 注解的解析方法有哪几种？

注解只有被解析之后才会生效，常见的解析方法有两种：

- **编译期直接扫描**：编译器在编译 Java 代码的时候扫描对应的注解并处理，比如某个方法使用`@Override` 注解，编译器在编译的时候就会检测当前的方法是否重写了父类对应的方法。
- **运行期通过反射处理**：像框架中自带的注解(比如 Spring 框架的 `@Value`、`@Component`)都是通过反射来进行处理的。



### ⑩ SPI 和 API 有什么区别？

从广义上来说它们都属于接口，下面先用一张图说明一下：

![img](https://oss.javaguide.cn/github/javaguide/java/basis/spi/1ebd1df862c34880bc26b9d494535b3dtplv-k3u1fbpfcp-watermark.png)



当实现方提供了接口和实现，我们可以通过调用实现方的接口从而拥有实现方给我们提供的能力，这就是 API ，这种接口和实现都是放在实现方的。

当接口存在于调用方这边时，就是 SPI ，由接口调用方确定接口规则，然后由不同的厂商去根据这个规则对这个接口进行实现，从而提供服务。

举个例子：公司 H 是一家科技公司，新**设计**了一款芯片，然后现在需要量产了，而市面上有好几家芯片**制造**公司，这个时候，只要 H 公司指定好了这芯片生产的标准**（定义好了接口标准）**，那么这些合作的芯片公司（服务提供者）就按照标准交付自家特色的芯片**（提供不同方案的实现，但是给出来的结果是一样的）**。



SPI 机制的具体实现本质上还是通过反射完成的。即：**我们按照规定将要暴露对外使用的具体实现类在 `META-INF/services/` 文件下声明。**



### 11 Java 序列化
