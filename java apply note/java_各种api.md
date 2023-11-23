# (1) 字符串相关

## 1.StringBuilder的使用

### 1.1常见操作

#### 修改ArrayList< String >中某个字符串的某个字符

~~~java
List<String> board = new ArrayList<>();
StringBuilder sb = new StringBuilder(board.get(row));
sb.setCharAt(0,'Q');//把第一个字符修改为Q
board.set(row,sb.toString());//使用修改后的字符串来替换原来的字符串
~~~



## 2 字符串的api

### api汇总

1. Boolean boolean = str.**contains**(String str1):判断str字符串中是否包含str1
2. String[] strs = str.**split**("/"):把str字符串进行分隔，以"/"作为分隔符，分隔后组成一个数组，返回这个数组
3. String res = str.**replace**("\r\n",""): 表示把str字符串的所有"\r\n"替换为空字符串
4. char[] chars = str.**toCharArray**();
5. char c = str.**charAt**(0);
6. String str =  str.trim(); 去除字符串前后的空格

### 2.1 contains函数：

~~~java
int sum = 0;
String str=i+"";
if (str.contains("0")) {
    sum=sum+i;
}
~~~

### 2.2 split()

~~~java
String str = "2003/11/30";
String[] date = str.split("/");  
int year = Integer.parseInt(date[0]);  
int month = Integer.parseInt(date[1]);  
int day = Integer.parseInt(date[2]);

~~~

# (2) Scanner类的使用

## 1 获取输入的方式：

```java
格式1：Scanner sc = new Scanner (new BufferedInputStream(System.in));
格式2：Scanner sc = new Scanner (System.in);
```

在读入数据量大的情况下，格式1的速度会快些。

- 读一个整数： int n = sc.nextInt(); 输入回车进行换行表示结束，使用空格作为间隔表示输入多个数字
- 读一个字符串：String s = sc.next(); 1、一定要读取到有效字符后才可以结束输入； 2、对输入有效字符之前遇到的空白，next()会自动将其去掉； 3、只有输入有效字符后才将其后面输入的空白作为分隔符或者结束符；所以next()不能得到带有空格的字符串。 
- 读一个浮点数：double t = sc.nextDouble(); 相当于 scanf("%lf", &t); 或 cin >> t; 
- 读一整行的字符串： String s = sc.nextLine(); 输入回车进行换行表示结束，可以获得空格。

判断是否有下一个输入和输入的类型可以用(会卡住程序，有输入并且类型符合时返回true)：

- sc.hasNext()
- sc.hasNextInt() （判断输入是否为整数）
- sc.hasNextDouble()
- sc.hasNextLine()



## 2 例子

### 2.1 读取行数的值与每行的数据

输入格式：

第一行：一共有n行

剩下的n行：第一行有a个数，第二行有b个数... 数量未知

**注意：**需要使用scanner.nextLine();来读取scanner.nextInt()读取不了的换行符

~~~java
public static void main(String[] args) {
    Scanner scanner = new Scanner(System.in);
    int col = scanner.nextInt();
    //读取换行符
    scanner.nextLine();
    //读取每行的数据
    for (int i = 0; i < col; i++) {
        String s = scanner.nextLine();
        String[] s1 = s.split(" ");
        for (int j = 0; j < s1.length; j++) {
            System.out.print(s1[j]);
        }
	}
}
~~~

输入格式：

第一行：一共有n行

剩下的n行：第一行有3个数，第二行有3个数... 固定数量

~~~java
public static void main(String[] args) {
    Scanner scanner = new Scanner(System.in);
    int col = scanner.nextInt();
    //读取每行的数据
    for (int i = 0; i < col; i++) {
        for(int j = 0;j<3;j++){
            int num = scanner.nextInt();
            System.out.print(num);
        }
	}
}
~~~



## 3 数据类型转换

parse?方法与valueOf方法：

如：并且可以按照名字的规律以此类推其他的api

```java
// Integer -> int
int num = Integer.parseInt(String str);

long a = Long.parseLong(String str);
// String -> Integer
Integer num = Integer.valueOf(String str);
```

# (3) 常用的数据结构的api

## 1.HashMap

- 增、改 ：put(key,value)
  - 删：clear(),value = remove(key)如果指定键存在于 HashMap 中，则该方法会删除对应的键值对，并返回所删除的值；如果指定键不存在于 HashMap 中，则返回 null。

- 查：value = get(key)
- 找：boolean = containsKey(key)

## 2.HsahSet

- add(obj) 增
- Boolean boolean = set.contains("key") 找

## 3.ArrayList

- add(obj) 增
- get(0) 查
- contains(obj) 找
- set(0,obj) 替换
- remove(0) 删

## 4 对集合进行排序 Collections.sort

默认排序：

~~~java
List<Integer> list = new ArrayList<>();
// 对集合进行排序，默认排序为从小到大
Collections.sort(list);
~~~

自定义排序标准：

~~~java
Collections.sort(list, new Comparator<Integer>() {
    @Override
    public int compare(Integer o1, Integer o2) {
        // 对集合进行排序，设置o1-o2表示从小到大进行排序
        return o1-o2;
    }
});
~~~

## 5 Arrays类

### api汇总：

- Arrays.fill(int[],-666) 初始化
- Arrays.sort() 排序
- Arrays.toString() 快速打印数组


### Arrays.fill(dp[],-666); 

把dp数组全部初始化为-666

### Arrays.sort(); 

其中的**compare（）方法**：返回负数表示第一个对象应排在前面，返回正数表示第二个对象应排在前面，返回0表示两个对象相等。

自定义排序的标准写法：

~~~java
// 自定义如何排序
public int test(int[][] envelopes){
    // envelopes存储了很多信封的数据，具体为每个信封的宽度与高度,多行两列：第一列为宽度，第二列为高度
    int n = envelopes.length;
    // 按宽度升序(宽度 前减后)排列，如果宽度一样，则按高度降序(高度 后减前)排列
    Arrays.sort(envelopes, new Comparator<int[]>() 
    {
        //形参为envelopes数组中的元素类型
        public int compare(int[] a, int[] b) {
            // 翻译结果：两个信封的宽度相同吗？相同则按照高度降序排列:否则按宽度升序排列
            return a[0] == b[0] ? 
                b[1] - a[1] : a[0] - b[0];
        }
    });
}
~~~

防止compare()方法中计算时出现爆int导致排序结果不符合预期的写法:

~~~java
// 区间调度算法（时间管理大师算法）：求最多的无重叠区间的个数
    public int maxOverlapInterval(int[][] intervals){
        // 区间格式：[start,end]
        // 找到end最小的区间
        Arrays.sort(intervals, new Comparator<int[]>() {
            @Override
            public int compare(int[] point1, int[] point2) {
                // 注意：不能使用return point1-point2,有可能会爆Integer导致排序错误
                // 如果point1比较大，结果为一个正数，进行返回，根据compare方法会把第二个对象point2排在前面,最终结果为升序排列
                if (point1[1] > point2[1]) {
                    return 1; //point1比较大时，直接返回1，把第二个对象point2排在前面，表示根据数组中的第一个元素进行升序排列,下面以此类推
                } else if (point1[1] < point2[1]) {
                    return -1;
                } else {
                    return 0;
                }
            }
        });
        // 注意:count的初始值为1，表示第一个不重叠的区间为end最小的区间
        int count = 1;
        long x_end = intervals[0][1];
        // 选择end最小的区间，排除重叠的区间，不断循环即可
        for(int[] interval : intervals){
            // 注意：这里是 >，而不是>=,因为=时气球会被引爆
            // 如果start > x_end,则找到一个不重叠的区间，更新x_end,继续查找
            if(interval[0] > x_end){
                count++;
                x_end = interval[1];
            }
        }
        return count;
    }
~~~



# (4) 大数与日期相关的api

## 4 BigInteger的使用

理论上能够表示无线大的数，只要计算机内存足够大。

### 4.1 BigInteger 的基本运算

- 加：a.add(b)
- 减：a.subtrac(b)
- 乘: a.multiply(b)
- 除: a.divide(b)
- mod: BigInteger c = b.remainder(a)
- 绝对值：a.abs()
- n次幂：a.pow(n)
- 取反数：a.negate()
- 最大公约数：a.gcd(b)
- 比较：a.equals(b) 返回boolean、a.compareTo(b) 返回数字(0,正数，负数)
- 通用的类型转换：类名.valueOf(): BigInteger.valueOf()，对象.intValue(): bigInteger.intValue()



valueOf(parament); 将参数转换为调用者的类型, 比如 :

```java
int a=3;
BigInteger b=BigInteger.valueOf(a)
```

intValue()方法：把当前对象的类型转换为int类型，返回一个int类型

~~~java
BigInteger a = new BigInteger("7");
int i = a.intValue();
System.out.println(i);//7
~~~



BigInteger 之间的比较

```java
BigInteger a = new BigInteger("123");
BigInteger b = new BigInteger("456");

System.out.println(a.equals(b)); // a == b 时为 true 否则为 false
BigInteger a = new BigInteger("123");
BigInteger b = new BigInteger("456");

if(a.compareTo(b) == 0) System.out.println("a == b"); // a == b
else if(a.compareTo(b) > 0) System.out.println("a > b"); // a > b
else if(a.compareTo(b) < 0) System.out.println("a < b"); // a < b
```

加法

```
BigInteger a = new BigInteger("123");
BigInteger b = new BigInteger("456");

a.add(b);
```

减法

```
BigInteger a = new BigInteger("123");
BigInteger b = new BigInteger("456");

a.subtract(b);
```

乘法

```
BigInteger a = new BigInteger("123");
BigInteger b = new BigInteger("456");

a.multiply(b);
```

除法

```
BigInteger a = new BigInteger("123");
BigInteger b = new BigInteger("456");

a.divide(b);
```

取余

```
BigInteger a = new BigInteger("123");
BigInteger b = new BigInteger("456");

BigInteger c = b.remainder(a);
System.out.println(c);
```

除法取余

```
BigInteger a = new BigInteger("123");
BigInteger b = new BigInteger("456");

BigInteger result[] = b.divideAndRemainder(a); // 该函数返回的是数组
System.out.println("商是：" + result[0] + "；余数是：" + result[1]);
```

最大公约数

```
BigInteger a = new BigInteger("12");
BigInteger b = new BigInteger("56");
        
System.out.println(a.gcd(b)); // 4
```

绝对值

```
BigInteger a = new BigInteger("-12");
        
System.out.println(a.abs()); // 12
```

取反数

```
BigInteger a = new BigInteger("-12");
        
System.out.println(a.negate()); // 12
```

幂

```
BigInteger a = new BigInteger("2");
        
System.out.println(a.pow(3)); // 8
```

## 5 BigDecimal类的使用

创建BigDecimal对象：

~~~java
// 1
double d1 = 0.10334;
BigDecimal num2 = BigDecimal.valueOf(d1);
// 2
BigDecimal num1 = new BigDecimal("1.347391");

~~~

比较BigDecimal对象:

~~~java
BigDecimal b1 = BigDecimal.valueOf(1);
BigDecimal b2 = BigDecimal.valueOf(1.00);
System.out.println(b1.equals(b2));// false 表示equals方法会比较每一位数字是否相同
System.out.println(b1.compareTo(b2));// 0 值相等但具有不同标度的两个 BigDecimal 对象（如，1 和 1.00）被认为是相等的;

~~~

#### 5.1 常用方法

1. add(BigDecimal)

   BigDecimal对象中的值相加，返回BigDecimal对象

2. subtract(BigDecimal)

   BigDecimal对象中的值相减，返回BigDecimal对象

3. multiply(BigDecimal)

   BigDecimal对象中的值相乘，返回BigDecimal对象

4. divide(BigDecimal)

   BigDecimal对象中的值相除，返回BigDecimal对象

5. toPlainString()

   将BigDecimal对象中的值转换成字符串

6. doubleValue()

   将BigDecimal对象中的值转换成双精度数

7. floatValue()

   将BigDecimal对象中的值转换成单精度数

8. longValue()

   将BigDecimal对象中的值转换成长整数

9. intValue()

   将BigDecimal对象中的值转换成整数

   

#### 5.2 divide方法详解：

BigDecimal中的divide主要就是用来做除法的运算(本身调用改方法的 被除数)。其中有这么一个方法.

```java
public BigDecimal divide(BigDecimal divisor, int scale, int roundingMode) 
```

第一个参数是除数，第二个参数是精确到小数点后的位数。2代表精确到小数点后第二位。

第三个参数是舍入模式:

- BigDecimal.ROUND_DOWN：直接省略多余的小数，比如1.28如果保留1位小数，得到的就是1.2
- BigDecimal.ROUND_UP：直接进位，比如1.21如果保留1位小数，得到的就是1.3

- BigDecimal.ROUND_HALF_UP：四舍五入，2.35保留1位，变成2.4

- BigDecimal.ROUND_HALF_DOWN：四舍五入，2.35保留1位，变成2.3


后边两种的区别就是如果保留的位数的后一位如果正好是5的时候，一个舍弃掉，一个进位。

例如：

~~~java
 public static void main(String[] args) {

                   BigDecimal a=new BigDecimal("10");
 
                   BigDecimal b=new BigDecimal("3");
 
                   BigDecimal c=a.divide(b,2, BigDecimal.ROUND_HALF_UP);
 
                   System.out.println(c);//结果：3.33
         }

~~~

#### 5.3 转换为字符串

- toEngineeringString：有必要时使用工程计数法。工程记数法是一种工程计算中经常使用的记录数字的方法，与科学技术法类似，但要求10的幂必须是3的倍数
- toPlainString：不使用任何指数,直接使用字符串形式显示BigDecimal中的数
- toString：有必要时使用科学计数法

例子：

~~~java
import java.math.BigDecimal;

public class BigDecimalDemo {
    public static void main(String[] args) {
        BigDecimal bg = new BigDecimal("1E11");
        System.out.println(bg.toEngineeringString());
        System.out.println(bg.toPlainString());
        System.out.println(bg.toString());
    }
}
~~~

输出

- 100E+9
- 100000000000
- 1E+11


## 6 Calendar 日期类的使用 1970年后的日期才能使用

获取`Calendar`实例的代码如下所示：

```java
Calendar calendar = Calendar.getInstance();
```

### 获取当前时间

```java
package com.zwwhnly.springbootaction.date;

import java.util.Calendar;

public class CalendarDemo {
    public static void main(String[] args) {
        Calendar calendar = Calendar.getInstance();

        int year = calendar.get(Calendar.YEAR);
        // 月份的下标是从0开始的，即0~11分别代表1~12月，因此需要+1
        int month = calendar.get(Calendar.MONTH) + 1;
        int day = calendar.get(Calendar.DAY_OF_MONTH);
        // 24小时制
        int hour = calendar.get(Calendar.HOUR_OF_DAY);
        int minute = calendar.get(Calendar.MINUTE);
        int second = calendar.get(Calendar.SECOND);

        System.out.println("现在是：" + year + "年" + month + "月" + day + "日" + hour + "时" + minute + "分" + second + "秒");
    }
}
```

运行结果：

> 现在是：2019年2月21日15时36分38秒

**注意事项：**月份的下标是从0开始的，即0到11分别代表1到12月。

### 设置时间

假设我们现在需要将时间设置为2019-02-21 23:59:59。

####  一起设置

```java
Calendar calendar = Calendar.getInstance();
//1:将时间设置为2019-02-21 23:59:59。
calendar.set(2019, Calendar.FEBRUARY, 21, 23, 59, 59);
//2:将时间设置为2019-01-31
calendar.set(2019, Calendar.JANUARY, 31);
System.out.println(calendar.getTime());
```

#### 分别设置

```java
Calendar calendar = Calendar.getInstance();
calendar.set(Calendar.YEAR, 2019);
calendar.set(Calendar.MONTH, Calendar.FEBRUARY);
calendar.set(Calendar.DAY_OF_MONTH, 21);
calendar.set(Calendar.HOUR_OF_DAY, 23);
calendar.set(Calendar.MINUTE, 59);
calendar.set(Calendar.SECOND, 59);

System.out.println(calendar.getTime());
```

3.1和3.2的运行结果都如下所示：

> Thu Feb 21 23:59:59 CST 2019

###  时间计算

#### 增加秒

我们在3.1的基础上增加1秒，那么时间应该是2019-02-22 00:00:00。

```java
Calendar calendar = Calendar.getInstance();
calendar.set(2019, Calendar.FEBRUARY, 21, 23, 59, 59);
calendar.add(Calendar.SECOND, 1);
System.out.println(calendar.getTime());
```

运行结果：

> Fri Feb 22 00:00:00 CST 2019

#### 增加月

首先我们将时间设置为2019-01-31，然后先增加1个月，再增加2个月，代码如下所示：

```java
Calendar calendar = Calendar.getInstance();

calendar.set(2019, Calendar.JANUARY, 31);
System.out.println(calendar.getTime());
calendar.add(Calendar.MONTH, 1);
System.out.println(calendar.getTime());
calendar.add(Calendar.MONTH, 2);
System.out.println(calendar.getTime());
```

运行结果：

> Thu Jan 31 15:58:03 CST 2019
> **Thu Feb 28 15:58:03 CST 2019**
> Sun Apr 28 15:58:03 CST 2019

**注意事项：当所在的月份没有那个日期时，如2月份没有31号，返回的是所在月的最后一天(2月28号)。**

###  获取某月的第一天和最后一天

```java
package com.zwwhnly.springbootaction.date;

import java.text.SimpleDateFormat;
import java.util.Calendar;

public class CalendarDemo {
    public static void main(String[] args) {
        System.out.println(getFirstDayOfMonth(2019, 2));
        System.out.println(getLastDayOfMonth(2019, 2));

        System.out.println(getFirstDayOfMonth(2019, 3));
        System.out.println(getLastDayOfMonth(2019, 3));
    }

    public static String getLastDayOfMonth(int year, int month) {
        Calendar calendar = Calendar.getInstance();
        calendar.set(Calendar.YEAR, year);
        calendar.set(Calendar.MONTH, month - 1);
        // 关键：获取当前月份的最后一天：calendar.getActualMaximum(Calendar.DATE)
        calendar.set(Calendar.DAY_OF_MONTH, calendar.getActualMaximum(Calendar.DATE));

        return new SimpleDateFormat("yyyy-MM-dd").format(calendar.getTime());
    }

    public static String getFirstDayOfMonth(int year, int month) {
        Calendar calendar = Calendar.getInstance();
        calendar.set(Calendar.YEAR, year);
        calendar.set(Calendar.MONTH, month - 1);
        //关键：获取当前月份的第一天: calendar.getMinimum(Calendar.DATE)
        calendar.set(Calendar.DAY_OF_MONTH, calendar.getMinimum(Calendar.DATE));

        return new SimpleDateFormat("yyyy-MM-dd").format(calendar.getTime());
    }
}
```

运行结果：

> 2019-02-01
> 2019-02-28
> 2019-03-01
> 2019-03-31



### 判断当前日期是星期几

~~~java
Calendar calendar = Calendar.getInstance();
if(calendar.get(Calendar.DAY_OF_WEEK)==Calendar.MONDAY){
    System.out.println("今天星期一");
}
~~~

### 格式化为中文格式的日期

calendar.getTime()返回一个Date类型的对象

~~~java
String date = new SimpleDateFormat("yyyyMMdd").format(calendar.getTime())
~~~

(5) 判断类的类型：

- instanceof  :  if(apple instanceof  fruit ) 
- getClass()  : 返回调用对象的运行时类的Class对象。
