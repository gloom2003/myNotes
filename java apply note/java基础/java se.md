# java SE

# 常用DOS命令

在接触集成开发环境之前，我们需要使用命令行窗口对java程序进行编译和运行，所以需要知道一些常用DOS命令。

## 打开控制台（命令行窗口）

1、打开命令行窗口的方式：**win + R打开运行窗口，输入cmd，回车**。

2、在要打开控制台的目录的**地址栏输入cmd，然后按回车**。直接可以再该目录下打开控制台。

3、在要打开控制台的目录下，**按住shift不放在空白处鼠标右键点击:在此处打开PowerShell窗口**

## 常用DOS命令及其作用

| 操作      | 说明                              |
| --------- | --------------------------------- |
| help      | 查看帮助文档                      |
| 盘符名称: | 盘符切换。E:回车，表示切换到E盘。 |
| dir       | 查看当前路径下的内容。            |
| cd 目录   | 进入单级目录。cd java             |
| cd ..     | 回退到上一级目录。                |
| cls       | 清屏。                            |

# if语句

下面代码的执行结果是什么?

~~~~java
int a=10;
int b;
if(a%2==0){
    b=0;
}else if(a%2==1){
    b=1;
}
System.out.println(b);
~~~~

答案：<span style='background:black'> 会在编译的时候报错.因为在编译的时候编译器看到了if...else if语句.它认为有可能两个判断都不成立.这样的话b就可能出现没有赋值直接使用的情况. </span>

# 流程控制语句-switch

使用switch也可以根据判断不同的情况做不同的处理。

## 格式

~~~~java
		switch (表达式) {
			case 值1:
				语句体1;
				break;
			case 值2:
				语句体2;
				break;
			case 值3:
				语句体3;
				break;
			...
			default:
				语句体n+1;
				break; // 最后一个break语句可以省略，但是推荐不要省略
		}

~~~~

switch后面小括号当中只能是下列数据类型：
			**基本数据类型：byte/short/char/int**
			**引用数据类型：String字符串、enum枚举**

## 小思考

switch和if都可以做多种情况的判断,那他们之间有什么区别呢?你觉得谁更灵活?

> tips:可以从他们小括号里能写的表达式的类型方面去考虑
> if的小括号中是布尔表达式，switch是byte,short...

答案：

if的表达式的布尔表达式，可以进行更复杂条件的判断(例如：值在某个范围内，多个条件同时符合等)而switch的表达式的数据类型只能适合做有限个数的等值判断。所以如果是有限个数的等值判断的话switch有的时候会更方便。其他情况下if会更适合。 

# 变量

1.变量有其作用范围，它的作用访问是定义他的那行代码所在的大括号内。

2.在同一个大括号中定义的变量名不能重复。

3.变量在使用之前，必须先初始化（赋值）**注意**：成员变量可以不手动初始化，有默认值。

4.定义long类型的变量时，需要在整数的后面加L（大小写均可，建议大写）。因为整数默认是int类型，加L相当于告诉计算机这个整数很特殊是long类型的。定义float类型的变量时，需要在小数的后面加F（大小写均可，建议大写）。因为浮点数的默认类型是double， 加F相当于告诉计算机这个小数很特殊是float类型的。

## 命名规则

- 由字母、数字、下划线“_”、美元符号“$”组成。
- 不能以数字开头
- 不能使用java中的关键字。	


## 命名规范

* 小驼峰式命名：变量名、方法名

​		首字母小写，从第二个单词开始每个单词的首字母大写。 **例如：  nickname  setAge  getAge**

* 大驼峰式命名：类名

​		每个单词的首字母都大写。  **例如：HelloWorld  FileUploadController**

## 方法重载

定义：在同一个类中,方法名相同,参数列表不同的方法才叫方法重载（与返回值无关）

参数列表不同：1. 参数类型不同  2.参数个数不同  3.参数顺序不同

如何判断参数列表是否相同？把参数类型全部拼接成一个字符串，如果字符串的内容不同就算参数列表不同。

~~~~java
public class Demo{
    public int test(int a,int b){}   // intint
    public void test(int a,int b){}//错  
    
    
    public void test(int a,int b,int c){}
    public void test(int x,int y){}//错   
    public void test(int a,double b){}
    public void test(double a,int b){}
    public void test(double a,double b){}
}
~~~~





## 1 权限修饰符

protected 修饰的: 只有这个类、其子类 、同一个包下的其他类 才能够访问。



# 面向对象基础

## 1.面向对象思想

​		面向对象的思想其实就是让我们**去指挥别人**或者是**使用工具**帮我们去把要做的事情完成。

成员变量：定义在类中方法外的变量就是成员变量。

成员方法：没有static修饰的方法就是成员方法。

# 面向对象-抽象类



## 1. 为什么要抽象

​	当一个类中有一个方法,这个方法在不同的子类中有不同的实现的时候,在父类中我们没有办法去写具体的方法体,这个时候就可以使用抽象.(即不写方法体)



## 2. 抽象类和抽象方法的格式

### 2.1 抽象方法

​	在成员方法的返回值类型前加<font color='red'>abstract</font>修饰，然后<font color='red'>去掉方法的大括号</font>,加上一个<font color='red'>分号</font>。

例如

~~~~java
public abstract void eat();
~~~~



### 2.2 抽象类

​	在class关键字的前面加上<font color='red'> abstract </font>修饰。

例如

~~~~java
public abstract class Animal{
    
}
~~~~



## 3.  抽象类的特点

- 抽象类可以有抽象方法，还有就是不能直接创建对象，其他所有都和普通类一样
- 抽象类的子类，要么重写抽象父类中所有的抽象方法，要么子类本身也是一个抽象类

# 面向对象-代码块

# 1. 局部代码块

## 1.1 格式

​	在方法中直接写一对大括号即可

~~~~java
public class Demo{
    
    public void test(){
        int a = 10;
        //下面是一个局部代码块
        {
            int b = 20;
        }
    }
}
~~~~



## 1.2 应用场景

​	如果需要控制局部变量的生命周期，想让其使用完后尽快销毁，可以把局部变量定义在局部代码块



# 2. 构造代码块

## 2.1 格式

​	在类中方法外部直接写一对大括号即可

~~~~java
public class Student{
    
    int age;
     //下面是一个构造代码块
    {
       age = 0;
    }

}
~~~~



## 2.2 调用时机

​	构造代码块会在调用构造方法的时候执行，并且会在构造方法中的代码执行之前执行。



## 2.3 应用场景

​	用来抽取构造方法中的重复代码，提高代码的复用性。



# 3. 静态代码块

## 3.1 格式

​	在类中方法外部直接写一对大括号即可，在括号前用static修饰

~~~~java
public class Student{
    
    static int num;
     //下面是一个构造代码块
    static {
       num = 0;
    }

}
~~~~



## 3.2 调用时机

​	在类被加载的时候会执行，同一个类在程序运行过程中只会被加载一次，所以只会执行一次



## 3.3 应用场景

​	用来给类当中的静态成员进行初始化。

# 面向对象-封装



## 1.封装的概念

​			封装其实就相当于把不需要用户了解细节（隐私或者的特别复杂的细节)包装(隐藏)起来，只对外提供公共访问方式 。



## 2.  成员变量私有化（封装的一种体现形式）

​		我们可以使用private来修饰成员变量，提供对应的set/get方法提供刚刚的访问方式。

# 面向对象-继承

## 1. 继承的概念

​			继承可以理解为就是让两个类（事物）产生从属关系，有了从属关系子类就肯定会具有父类的特征（父类中的非私有成员），这样我们用类去描述一些事物的时候就可以更方便

## 4. 继承的优缺点

### 4.1 优点

​		提高了代码的复用性

### 4.2 缺点

​		增加了类和类之间的耦合性。违背了`高内聚，低耦合`原则

## 5. 继承后成员相关语法

​		

### 5.1 成员变量

1. 父类非私有的成员变量才会继承给子类。所以当我们看到使用子类对象的某个成员变量时，有可能这个成员变量是定义在子类中。也有可能是定义在其父类中。

2. 父类中如果已经有了某个成员变量，我们不应该再在子类中定义同名的成员变量。否则可能会导致非常难排查的Bug。



### 5.2 构造方法

 1. 构造方法不会继承给子类

 2. 子类的构造中必须调用父类的构造并且要求在第一行。

 3. 子类的构造默认都会在第一行调用父类的无参构造，所以当父类没有无参构造的时候子类构造中会报错。解决方案是给父类加上无参构造或者在子类构造中显示的调用父类的有参构造。

     

**理解**

问：继承的时候构造方法为什么不会被继承？

答：<span style='background:black'> 假设会被继承,这个方法名和子类的类名就肯定不相同,所以不能算是构造方法.所以假设不成立 </span>



问：子类的构造方法会默认调用父类的无参构造super().为什么?

答：<span style='background:black'>因为父类中可能有成员变量,并且这个成员变量要继承给子类使用,但是所有的变量在使用之前必须先赋值.而父类的成员变量只能用父类的构造进行默认的初始化。</span>



问：在子类的构造方法中,能不能把父类的构造放到第二行?

答：<span style='background:black'>不能,因为要保证成员变量先初始化了.如果放到第二行,有可能在第一行还没初始化的时候就使用的父类的成员变量</span>



### 5.3 成员方法

​		父类非私有的成员方法会继承给子类。所以当我们看到使用子类对象的某个成员成员方法时，有可能这个成员方法是定义在子类中。也有可能是定义在其父类中。

​		

## 6. 方法重写

### 6.1 方法重写的概念

​		 当子类拥有父类继承下来的某个功能（成员方法）,但是在子类中对这个方法的具体实现和父类不同。这个时候我们在子类中定义了一个和父类方法相同的方法(包括返回值类型,方法名,参数列表) ，这就叫做方法重写。



### 6.2 注意事项

- ​		我们在重写方法的时候方法的权限修饰符其实可以和父类不同**（一般都相同）**。但是子类中方法的权限不能比父类低。**（权限修饰符 : private  <   默认(什么都不写)  <   protected  < public）**

- ​		我们在重写方法的时候方法的返回值类型其实可以不同**（一般都相同）**。但是要求子类中方法的返回值类型必须是父类方法返回值类型的子类。

- ​		我们可以用 @Override 注解来校验是不是方法重写。

- ​        私有的方法不能被重写，因为私有的不会被继承

  

### 6.3 小思考

面试题：说说overload和override的区别。

答：		
<span style='background:black'>	方法重载：在同一个类中，方法名相同，参数列表不同，和返回值无关方法重写：在子父类中，子类有一个和父类方法名相同，参数列表相同，返回值类型也相同的方法。这个就叫方法的重写				</span>







## 7. this和super

​			this就代表本类的，super就代表父类的

使用：

- 访问成员变量
- ​			this.成员变量名                 本类的成员变量
- ​			super.成员变量名             父类的成员变量
- 访问成员方法
- ​			this.成员方法名(参数)       调用本类的成员方法
- ​			super.成员方法名(参数)   调用父类的成员方法
- 调用构造方法
- ​			this(参数)                           调用本类的构造方法
- ​			super(参数)                       调用父类的构造方法



# 面向对象-多态

## 1. 多态的概念

​	同一个数据类型的不同对象对同一种行为会有多种不同的实现。



## 2. 多态的前提

​	① 子类重写了父类的方法

​	② 父类引用指向子类对象（创建的是一个子类的对象，并把该对象赋值给一个变量，这个变量的类型是其父类类型）

​	例如：

~~~~java
Animal a = new Dog();
Animal b = new Cat();
~~~~



## 3. 父类引用指向子类对象后成员访问的特点

​	除了成员方法编译看左边，运行看右边。其他所有成员都是编译看左边，运行看左边。

​	解读：编译期间会去看左边(父类),看父类有没有这个成员方法。如果没有则直接报错,如果有则编译通过,不报错。运行期间,实际执行代码,看的是右边(子类),看子类中有没有重写该方法,如果有则执行子类中的该方法,如果没有
则运行父类中的该方法。



## 4. 多态的应用场景

​	多态最大的应用场景其实就用在方法的参数上。在适当的时候把方法的参数类型定义成父类类型。调用方法的时候就可以传入任意的子类对象。提高了代码的复用性和可扩展性。



## 5. 多态的优缺点

优点：提高了代码的复用性和可扩展性

缺点：不能直接使用子类的成员。



## 6.向上转型，向下转型

### 6.1 向上转型 

​			向上转型就是子类转父类。因为是绝对安全的所以会自动进行转换。

例如：

~~~~java
	Animal a = new Dog();
~~~~



### 6.2 向下转型

​          向下转型就是父类转子类。因为不是绝对安全的所以必须使用强转的方式来进行转换。

例如：

~~~~java
	Animal a = new Dog();
	Dog d = (Dog)a;
~~~~



注意：必须是这个子类的对象才可以转换成该子类,否则会出异常



### 6.3 instanceof进行类型判断

​		在向下转型的时候为了保证安全我们可以使用instanceof进行类型判断。判断一个对象是否某个类的对象。如果是的话我们再把它转换成该类型，这样会更加安全。



#### 6.3.1 使用格式

​					对象 instanceof 类名/接口名  

示例：

~~~~java
	//判断对象是否是某个类的对象，如果是结果为true，如果不是结果为false
	Animal a = new Dog();
	if(a instanceof Dog){
        //说明a是Dog这个类的对象，我们可以把他强转成Dog类型
        Dog d = (Dog)a;
    }
~~~~





## 2级 2. 泛型

### 2.1 概述

​		泛型可以把类型明确的工作推迟到**创建对象、定义子类**或**调用方法**的时候才去明确的特殊的类型 。

​		相当于把数据类型作为参数来进行传递。

​		**注意：泛型只能是引用数据类型。，如果输入的是基本数据类型，则会自动转换为对应的包装类来确定泛型**

### 2.2 使用

#### 2.2.1 泛型类&泛型接口

​		 泛型类和泛型接口的用都相同，下面我们以泛型类为例进行讲解。

​		泛型类就是把泛型定义在类上，用户使用该类的时候，才把类型明确下来 。

##### 2.2.1.1 定义泛型

​	在类名后加<>，在<>中定义泛型，<>中的内容相当于泛型的名字，可以随便写。在泛型类中我们可以把这个泛型的名字当做一个数据类型来使用。

~~~~java
public class TestClass<T> {
    //...
}
~~~~

##### 2.2.1.2 使用泛型

​	在泛型类中可以使用在类名后面定义的泛型。

~~~~java
public class TestClass<T> {
    public void test(T t){
       
    }
}
~~~~



##### 2.2.1.3 泛型的确定

①创建对象时确定

​		在创建泛型类对象的时候确定之前定义的泛型代表什么数据类型。在定义泛型类对象的时候，在类名的后加<>，在其中写一个具体的数据类型。

~~~~java
    public static void main(String[] args) {
        TestClass<String>  t = new TestClass();//指定了该对象的泛型T是String类型
        t.test("三更草堂");//所以test方法的参数类型应该也是String类型
    }
~~~~

②定义子类时确定

​		在定义子类的时候可以确定泛型。具体用法如下：

~~~~java
public class SubClass extends TestClass<String> {
    @Override
    public void test(String s) {
        
    }
}
~~~~

​		这样在子类SubClass中泛型就确定为String类型了。



**注意**：我们在定义子类时也可以选择不确定泛型，让其在创建对象的时候在确定。写法如下

~~~~java
public class SubClass<T> extends TestClass<T> {
    @Override
    public void test(T t) {
        super.test(t);
    }
}
~~~~

**创建对象时确定`SubClass<T>`的类型，然后传递给extends的`TestClass<T>`，也为String类型**，于是：

~~~java
SubClass()<String> sub = new SubClass<>();
sub.test("test");
~~~



#### 2.2.2泛型方法

##### 2.2.2.1 定义泛型

​		在方法返回值类型的前面加<>，在<>中定义泛型，<>中的内容相当于泛型的名字，可以随便写。在该泛型方法中我们可以把这个泛型的名字当做一个数据类型来使用。

~~~~java
    public static  <T> T test(T t){
        return t;
    }
~~~~



##### 2.2.2.2 使用泛型

​		在泛型方法中可以使用定义的泛型。并且我们一般是**在参数列表中(必须的，不然没有办法确定这个泛型的类型)**或者是返回值类型上使用到这个泛型。

~~~~java
    public static  <T> T test(T t){
        return t;
    }
~~~~



##### 2.2.2.3 泛型的确定

​		在**调用泛型方法**的时候才真正确定之前定义的泛型代表什么数据类型。在**调用泛型方法**的时候，程序会根据你的调用自动推导泛型的具体类型。

~~~~java
    public static void main(String[] args) {
        Integer test = test(1);
        String s = test("三更草堂");
    }
~~~~



### 2.3 泛型上限&泛型下限

#### 2.3.1 泛型限定的概念

​	我们在使用确定泛型的时候可以使用任意的引用数据类型去确定。但是在某些场景下我们**要求这个泛型必须是某个类的子类或者是某个类的父类**。这种情况下我们就需要用到泛型上限和泛型下限来限制泛型的范围。



#### 2.3.1 泛型上限

​	限制泛型必须是某个类或者是其子类。

格式：

~~~~java
  <? extends 具体的类型>
~~~~

例如：

~~~~java
public static void test(List<? extends Person> t){

}
~~~~

这样我们再调用test方法的时候只能存入泛型为Person或者是Person子类的List集合对象。



#### 2.3.2 泛型下限

​	限制泛型必须是某个类或者是其父类。

格式：

~~~~java
<? super 具体的类型> 
~~~~

例如：

~~~~java
public static  void test(List<? super Student> t){

}
~~~~

这样我们再调用test方法的时候只能存入泛型为Student或者是Student父类的List集合对象。



#### 2.3.3 注意事项

​	1.泛型上限可以在定义泛型类和方法参数上使用

~~~~java
public class Box<E extends Person> { // 不能使用?了
    E e;
}

~~~~

​	2.泛型下限主要在方法参数上使用，不能在泛型类上使用（没有意义）。



### 方法泛型实战例子

~~~java
    //方法上的泛型<V>,设置返回的类型为V类型，由传入Class参数的泛型决定V的类型
    //类似于List<String>,Class<String>表示传入的是String的字节码对象
    public static <V> V beanCopy(Object source, Class<V> clazz) {
        V v = null;
        try {
            v = clazz.newInstance();
            //使用org.springframework.beans.BeanUtils中的copyProperties方法
            BeanUtils.copyProperties(source, v);
        } catch (Exception e) {
            e.printStackTrace();
        }
        return v;
    }

    public static <V,O> List<V> beanListCopy(List<O> sourceLists, Class<V> clazz) {
            return sourceLists.stream()
                    .map(o->beanCopy(o,clazz))
                    .collect(Collectors.toList());

        }
~~~

使用：

~~~java
List<HotArticleVo> articleVos = BeanCopyUtil.beanListCopy(articles, HotArticleVo.class);
~~~

## 浅拷贝与深拷贝

在Java语言里，当我们需要拷贝一个对象时，有两种类型的拷贝：浅拷贝与深拷贝。浅拷贝只是拷贝了源对象的地址，所以源对象的值发生变化时，拷贝对象的值也会发生变化。而深拷贝则是拷贝了源对象的所有值，所以即使源对象的值发生变化时，拷贝对象的值也不会改变。如下图描述：

![img](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2018/10/15/166750fcb9394c44~tplv-t2oaga2asx-jj-mark:3024:0:0:0:q75.awebp)





# 集合-3



## 1. 常用Map集合

### 1.1 Map集合的概述

​	Map接口是双列集合的顶层接口，下面是Map接口的定义

~~~~java
interface Map<K,V>  K：键的类型；V：值的类型
~~~~

​	存储的数据必须包含key和value。

​	key和value在Map集合中是一一对应的关系。一个key对应一个value。

​	key在map集合中是不会重复的。

​	



### 1.2 HashMap

HashMap集合的特点

- 底层数据结构是哈希表
- 存储元素的顺序和遍历获取出来的顺序可能不一致
- key不会重复



#### 1.2.1 创建对象

~~~~java
HashMap<key的数据类型,value的数据类型> map = new HashMap<>();
~~~~

例如：

~~~~java
    public static void main(String[] args) {
        HashMap<String,String> map = new HashMap<>();
        HashMap<String,Integer> map = new HashMap<>();
    }
~~~~



#### 1.2.2 常用方法

>```java
>V put(K key, V value)   //添加元素，如果key不存在就添加，如果key已经存在则是修改对应的value,并且返回修改前的value
>V get(Object key)  //根据key获取对应的value值返回。如果key不存在就返回null
>V remove(Object key) //根据key删除map中对应的键值对。并且把删除的value返回
>boolean containsKey(Object key) //判断key是否存在
>int size() //集合中键值对的对数
>void clear() //清空集合中的所有键值对    
>```

~~~~java
    public static void main(String[] args) {
        HashMap<String,String> map = new HashMap<>();
//        map.put()
        //添加元素
        map.put("name", "三更");
        map.put("age", "15");
        String v = map.put("name", "三更草堂");
        String name = map.get("name");
        String age = map.get("age");
        //删除元素
        String delV = map.remove("age");
        //判断key是否存在
        if(map.containsKey("name")){
            String age111 = map.get("name");//null
            System.out.println(age111.length());
        }
        //size
        int size = map.size();
        map.clear();
    }
~~~~



#### 1.2.3 遍历

1.使用entrySet遍历

​	map集合的entrySet方法可以获取一个Set集合，集合中存放的是Entry对象，一个Entry对象相当于一个键值对。我们可以遍历set集合拿到Entry对象，然后获取出里面的键和值。

~~~~java
    public static void main(String[] args) {
        HashMap<String,String> map = new HashMap<>();
        map.put("name","三更");
        map.put("age","15");

        Set<Map.Entry<String, String>> entries = map.entrySet();
        //使用迭代器遍历entrySet
        Iterator<Map.Entry<String, String>> it = entries.iterator();
        while (it.hasNext()){
            Map.Entry<String, String> entry = it.next();
            System.out.println(entry.getKey()+"===="+entry.getValue());
        }
    }
~~~~

~~~~java
    public static void main(String[] args) {
        HashMap<String,String> map = new HashMap<>();
        map.put("name","三更");
        map.put("age","15");
		
        Set<Map.Entry<String, String>> entries = map.entrySet();
        //使用foreach遍历entrySet
        for (Map.Entry<String, String> entry : entries) {
            System.out.println(entry.getKey()+"===="+entry.getValue());
        }
    }
~~~~



2.使用keySet遍历

​	map集合的keySet方法可以获取一个Set集合，集合中存放的是所有的key。我们可以遍历set集合拿到key对象，然后通过key获取对应的value。

~~~~java
    public static void main(String[] args) {
        HashMap<String,String> map = new HashMap<>();
        map.put("name","三更");
        map.put("age","15");

        Set<String> keys = map.keySet();
        for (String key : keys) {
            System.out.println(key+"===="+map.get(key));
        }
    }
~~~~



### 1.3 HashMap的key去重原理

​	HashMap在添加元素的时候会判断集合中是否有key和本次存入的key相同。判断的时候主要是通过hashCode方法和equals方法来进行判断的。hashCode相同，并且equals判断也相同就会认为是同一个key。

![image-20201205213813932](C:\Users\GLOOM\Desktop\for zip\not system\sangGeng files\普通配套资料\JavaSE进阶\img\image-20201205213813932.png)

​	所以如果我们要存储到HashMap中的key是一个自定义的类型(如：Student)。就需要根据情况判断下是否需要重写下hashCode方法和equals方法。

Student类：

~~~java
public class Student{
    private String name;
    private int age;
    // 省略构造器
}
~~~



有代码：

~~~java
Map<Student,String> map = new HashMap<>();
map.put(new Student("kana",15),"kana");
map.put(new Student("kana",15),"kana");
System.out.println(map);// 结果: map中有两个键值对，而不是一个，因为两个HaashMap判断这两个Student是不同的key
~~~

如何让HashMap根据Student的两个属性来判断是否是同一个对象，比如：如果name属性与age属性的值都相同，就判断为同一个对象。

根据HashMap的源码(上图)，只需要重写Student的equals和hashCode方法即可：

重写方式如下（使用idea自动生成即可）：

~~~~java
public class Student {
    private int age;
    private String name;
	// ....此次省略了构造方法和set/get方法

    @Override
    public boolean equals(Object o) {
        if (this == o) return true;
        if (o == null || getClass() != o.getClass()) return false;
        Student student = (Student) o;
        return age == student.age && // 如果两个都属性相同，则返回true
                Objects.equals(name, student.name);
    }

    @Override
    public int hashCode() {
        // 根据age、name两个属性来生成hashCode，如果两个属性都相同，则生成的hashCode也相同
        return Objects.hash(age, name);
    }
}
~~~~



​	**注意：HashSet存储数据其实也是使用了HashMap。所以如果往HashSet中存储自定义对象也要看情况是否需要重写hashCode方法和equals方法。**









​	

