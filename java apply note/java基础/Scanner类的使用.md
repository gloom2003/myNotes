# Scanner类的使用

## 1 获取输入的方式：

格式1：Scanner sc = new Scanner (new BufferedInputStream(System.in));
格式2：Scanner sc = new Scanner (System.in);
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

剩下的n行：第一行有1个数，第二行有2个数...

简洁版本：

~~~java
public static void main(String[] args) {
        Scanner scanner = new Scanner(System.in);
        while (scanner.hasNext()) {
            //读取行数的值
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
    }
~~~

详细版本：

~~~java
public static void main(String[] args) {
        Scanner scanner = new Scanner(System.in);
        System.out.print("请输入行数的值：");
        while (scanner.hasNext()) {
            //读取行数的值
            int col = scanner.nextInt();
            //读取换行符
            scanner.nextLine();
            //读取每行的数据
            for (int i = 0; i < col; i++) {
                System.out.print("请输入第"+(i+1)+"行的值:");
                String s = scanner.nextLine();
                String[] s1 = s.split(" ");
                for (int j = 0; j < s1.length; j++) {
                    System.out.print(s1[j]+" ");
                }
                System.out.println();
            }
            System.out.print("请输入行数的值：");
        }
    }
~~~

## 3 数据类型转换

parse?方法与valueOf方法：

如：

```java
int num = Integer.parseInt(String str);

long a = Long.parseLong(String str);

Integer num = Integer.valueOf(String str);
```

