# shell的使用

## 1基础知识

![img/1700901520313.png](img/1700901520313.png)

Linux的命令行界面（进程）本质就是一个shell解析器，把用户输入的命令进行解析，翻译给操作系统(Linux内核),以此让操作系统操作硬件。

shell的执行方式：写一句，解释一句，执行一句

Shell的两种主要语法类型有Bourne和C,这两种语法彼此
不兼容。Bourne家族主要包括sh、ksh、Bash(Linux的标准)、psh、
zsh;C家族主要包括：csh、tcsh

### 1.1 echo 相当于打印命令 

#### 1.1.1 $的使用 调用环境变量

查看当前使用的shell解释器，查看SHELL变量，$表示调用此变量

~~~sh
echo $SHELL
~~~

打印的内容中**有空格时需要使用双引号包裹**起来,**注意：不能打印！号**

~~~sh
echo "hello world"
~~~

单引号''可以防止转义,双引号不能，例如：

~~~sh
n=1
echo '======$n======'
# 结果为======$n====== 使用单引号后不会认为$n是在调用一个变量
~~~



#### 1.1.2 -e参数

参数-e 表示开启对字符的特殊处理

**改变颜色：**

~~~sh
echo -e "\e[1;31m error \e[0m"
~~~

\e[1;31m表示开启颜色显示 其中31m表示使用红色

\e[0m 表示取消颜色显示

输出的颜色有：

- 30m=黑色，31m=红色，32m=绿色，33m=黄色
- 34m=蓝色，35m=洋红，36m=青色，37m=白色

**处理格式：**

![https://image.itbaima.net/images/173/image-20231008122956152.png](https://image.itbaima.net/images/173/image-20231008122956152.png)

~~~sh
echo -e "hello \t world"
~~~



### 1.2 可以查看当前支持哪些shell

~~~sh
vi /etc/shells
~~~

### 1.3 直接输入sh,csh 开启一个子shell

~~~sh
sh
~~~

可以开启一个子shell（相对于现在的shell）,如果支持则可以使用对应的shell,输入exit退回上一个shell界面(即bash)



### 1.4 第一个shell脚本

#### 1.4.1 准备工作

后缀一般是.sh,其实不加或者使用其他的也可以

~~~sh
vim hello.sh
~~~

注释，指定使用bash解析器(使用sh也可以，因为sh指向了bash)

~~~sh
#!/bin/bash
echo -e "\e[1;31m error \e[0m"
~~~

#### 1.4.2 脚本运行的3种方式

1.赋予执行权限后，开启一个**子shell**去解析脚本来运行(常用),运行完成后会回到父shell中

```sh
# 在hello.sh目录下时：
chmod 755 hello.sh
./hello.sh # 注意：不能使用省略 ./ ,否则不能执行这个脚本，而是会去/bin目录下面寻找命令
```

2.另外开启一个bash进程(**子shell**)去调用脚本(可以使用相对路径或者绝对路径)

```sh
bash hello.sh
```

3 使用. 与 source 直接**使用当前的shell进程**进行解析脚本

~~~sh
. hello.sh
source hello.sh
~~~

## 2 Shell中的变量

### 2.1常用系统变量

$HOME、$PWD、$SHELL、$USER等

查看系统变量：

1 查看**系统**的**全局**的shell环境变量，并通过分页器 less 进行分页显示

~~~sh
env | less # 两个命令作用相同
printenv | less
~~~

2 查看单个变量：

~~~sh
echo $HOME
printenv HOME
~~~

3 查看所有的变量（无论全局、局部，系统、自定义）

~~~sh
set
~~~

### 2.2 自定义变量

#### 2.2.1 局部变量：

注意：=旁边不能有空格,因为Linux中空格表示的是 命令与参数之间的间隔

~~~sh
a=1
echo $a
~~~

#### 2.2.2 全局变量：

自定义一个全局变量，需要先定义一个局部变量

~~~sh
a=1
export a # 把a提升为全局变量
~~~

**注意**:在子shell中能够看到全局变量，也可以进行修改，但是，**在子shell中修改后的结果只作用于这个子shell中**，其他的子shell或父shell无法看到修改后的结果，在他们看来，修改后的全局变量的值仍然不变。

#### 撤销变量

```sh
unset A
```

#### 静态变量

声明只读的变量(静态变量)B=2，不能被修改，也不能unset撤销

```sh
readonly B=2
B=3 # 报错：-bash: B: readonly variable
```



Linux命令存储的路径： /bin与/sbin ...

全部存储在PATH环境变量中：

~~~sh
echo $PATH
~~~

### 2.3 特殊变量(传递参数)

#### $n 获取参数

 （功能描述：n为数字，$0代表该脚本名称，$1-$9代表第一到第九个参数，十以上的参数需要用大括号包含，如${10}）

test.sh的内容：

~~~sh
#!/bin/bash
echo "$0  $1  $2"
~~~

调用：

~~~sh
./test.sh cls xz
~~~

结果：

```
./test.sh cls  xz
```

#### $#  获取参数的数量

（功能描述：获取所有输入参数个数，常用于循环）。

#### $*、$@ 获取参数整体

**不被双引号“”包含时**，都以$1 $2 …$n的形式输出所有参数。

当它们**被双引号“”包含时**，“$*”会将所有的参数作为一个整体，以“$1 $2 …$n”的形式输出所有参数；

“$@”会将各个参数分开，以“$1” “$2”…”$n”的形式输出所有参数。

例如：

~~~sh
#!/bin/bash
echo '=======$*========'
for param in "$*" # 必须使用""包裹起来才能够看出$*与$@的区别，否则两个的作用都可以遍历到每一个参数
do
	echo param # 结果 a b c d
done
echo '=======$@========'
for param in "$@"
do
	echo param 
done
# 结果：
a
b
c
d
~~~



#### $? 获取上一次命令的返回值

$？ （功能描述：最后一次执行的命令的返回状态。如果这个变量的值为0，证明上一个命令正确执行；如果这个变量的值为非0（具体是哪个数，由命令自己来决定），则证明上一个命令执行不正确了。）



运算符

## 3 数值运算

在bash中，变量默认类型都是字符串类型，无法直接进行数值运算。

要想进行数值运算的两种方式：

1.$(())

2.$[]

~~~sh
a=$((1+2))
echo $a # 3

b=$[5+3]
echo $b # 8
~~~

或者使用expr:

加减乘除：

~~~sh
expr 1 + 2 # 表示把1、+、2这三个参数传给expr命令来执行 执行结果：3
expr 2 \* 2 # 使用\*代替*进行转义，表示进行乘法运算 结果： 4
~~~

给变量赋值：使用**命令替换(就是调用系统函数)**

语法：$(Linux命令)

~~~sh
a=$(expr 1 + 2)
# 或者使用反引号
a=`expr 1 + 2`
~~~



## 4 数值、文件、权限判断

### 4.1 各种判断符号

（1）两个整数之间比较

-lt 小于（less than）           -le 小于等于（less equal）

-eq 等于（equal）             -gt 大于（greater than）

-ge 大于等于（greater equal）  -ne 不等于（Not equal）

如：

~~~sh
z=2
[ $z -ge 8 ]
echo $? # 结果：1 表示false
~~~

（2）按照文件权限进行判断

-r 有读的权限（read）         -w 有写的权限（write）

-x 有执行的权限（execute）

例如：在当前目录下面有test.sh文件时：

~~~sh
[ -r test.sh ] # 判断当前用户(user)对test.h是否有可读权限
echo $? # 结果： 0 true
~~~



（3）按照文件类型进行判断

-f 文件存在并且是一个文件（file）

-e 文件存在（existence）       -d 文件存在并是一个目录（directory）

例如：/home/test/路径下没有1.txt文件时：

~~~sh
[ -e /home/test/1.txt ]
echo $? # 结果： 1 false
~~~

(4)字符串运算符：=、！=、**-z 判断字符串是否为空**

### 4.2 单条件判断

test 命令：

~~~sh
a=hello
test a = hello
echo $? # 结果： 0 表示测试成功，相当于返回true
~~~

[]命令：

格式：[ 条件 ] 

**注意**1：[]内部的头尾要有空格

~~~sh
a=hello
[ a = hello ]
echo $? # 结果： 0 表示测试成功，相当于返回true
~~~

**注意2**: 条件必须使用空格分隔开，如果写成这样:

~~~sh
a=hello
[ a=abc]
echo $? # 结果： 0 表示测试成功,因为没有空格分隔，[]里面有值的话就会判断为true,返回0
# 与此相对的
[  ] # 结果： 1 ，因为里面是空值
~~~

### 4.3 多条件判断

~~~sh
[ 判断 ] && echo OK || echo notOk
# 相当于java中的三目运算符:  判断 ? echo OK : echo notOk
~~~

## 5 流程控制

### 5.1 if语句

语法：**注意**：if后要有空格

#### 单条件判断：

~~~sh
if [ 条件判断式 ];then  # ;表示同时执行两个命令
  程序 
fi 

# 或者这样：把then放在单独一行来执行
if [ 条件判断式 ] 
then 
    程序 
fi

# 或者使用(()) ,然后就可以使用正常的>号来进行数字之间的数值比较了
if ((a > 2))
then
	echo $a
else
	echo a<=2
fi

# 多个if分支使用elif
if [ $1 -eq "1" ]
then
        echo "if"
elif [ $1 -eq "2" ]
then
        echo "elif"
else
		echo "else"
fi

# 防止没有传入的参数而导致的报错
if [ "$1"x = "jojo"x ];then # "$1"x 表示字符串拼接，即使没有传入参数，也会进行 [ x = "jojo"x ]的判断,不会报错
	echo "hello,jojo"
fi

~~~

#### 多条件判断：

~~~sh
if [ $a -gt 18 ] && [ $a -lt 35 ];then
	echo Ok
fi
# 相当于
if [ $a -gt 18 -a $a -lt 35 ];then # -a 表示 and,-o 表示 or
	echo Ok
fi
~~~

### 5.2 case语句

基本语法

~~~sh
case $变量名 in 
  "值1"） # ""可以省略
    如果变量的值等于值1，则执行程序1 
    ;; # 相当于 break;
  "值2"） 
    如果变量的值等于值2，则执行程序2 
    ;; 
  …省略其他分支… 
   *） # 相当于default
    如果变量的值都不是以上的值，则执行此程序 
    ;; 
esac # case反写
注意事项：
1)	case行尾必须为单词“in”，每一个模式匹配必须以右括号“）”结束。
~~~

### 5.3 for循环

#### 形式1:

~~~sh
for (( 初始值;循环控制条件;变量变化 )) 
do 
    程序 
done
~~~

例如：一个计算1-100的脚本

~~~sh
#!/bin/bash

s=0
for((i=0;i<=100;i++)) # 因为在双小括号中，所以可以使用<=而不是-le,同样的，因为在双小括号中所以也可以使用i++
do
        s=$[ $s + $i ] # 使用$[]进行运算
done
echo $s

~~~

#### 形式2：

~~~sh
for 变量 in 值1 值2 值3… 
do 
    程序 
done
~~~

例如：

~~~sh
for os in linux windows macos
do
	echo $os
done
~~~

再如：

~~~sh
sum=0
for i in {1..100} # 表示1-100
do
	sum=$[ $sum + $i ]
done
echo $sum
~~~

### 5.4 while循环

~~~sh
while [ 条件判断式 ] 
do 
    程序
done
~~~

例子：

~~~sh
#!/bin/bash
a=1
while [ a -le 100 ]
do
	sum=$[ $sum + $a ] 
	a=$[ $a + 1 ]
	# 新版写法:
	let sum+=a
	let a++
done
echo $sum
~~~



## 6 read读取控制台输入

1．基本语法

​    read(选项)(参数)

​    选项：

-p：提示信息；

-t：指定超时退出时间（秒）。

参数

​    变量：指定赋值给哪一个变量

例如：

~~~sh
#!/bin/bash
read -t 10 -p "请输入你的名称：" name
echo $name
~~~

## 7 函数

### 7.1 系统函数(Linux命令)

调用方式：命令替换： $(命令)

语法：basename [string / pathname] [suffix]

##### basename 

找到最后一个/进行一个字符串的切割，返回/后面的部分，并且清除指定的后缀suffix

例如：

~~~sh
basename /root/test/hello.sh .sh # 结果：hello
~~~



dirname 文件绝对路径 

##### dirname 

找到最后一个/进行一个字符串的切割，返回/前面的部分

例如：

~~~sh
dirname /root/test/hello.sh # 结果:/root/test
~~~

### 7.2 自定义函数

1．基本语法 ：[]里面的可以省略

```sh
[ function ] funName[()]{

    # 函数体
    [return int]
}
```

2．**注意**

​    （1）必须在调用函数地方之前，先声明函数，shell脚本是逐行运行的。不会像其它语言一样先编译。

​    （2）函数返回值，只能通过$?系统变量获得，可以加：return返回，如果不加，将以最后一条命令运行结果，作为返回值。return后跟数值n(0-255),不能返回字符串

例如：

~~~sh
#!/bin/bash
function add(){
	res=$[$1 + $1]
	echo $res # 不使用return而是使用echo来返回结果
}
read -p "请输入第一个数字" a
read -p "请输入第二个数字" b
num=$(add $a $b) # 调用函数，传入参数,接收返回值
echo "结果："$num
~~~





## 8 编写好的脚本

备份脚本，传入需要备份的目录(路径的最后没有/),能够自动把这个命令下面的文件压缩然后备份到/root/bf这个路径下面

~~~sh
#!/bin/bash
# 判断参数是否只有一个
if [ $# -ne 1 ]
then
	echo "参数的数量错误！只能传入一个参数,告诉我需要备份的目录"
	exit # 退出当前执行命令的shell窗口
fi
# 判断输入的路径是否是一个目录
if [ -d $1 ]
then
	echo
else
	echo "输入的目录不存在"
	exit
fi
# 无论传入的是相对路径还是绝对路径都统一转换为绝对路径
absolute_path=$(cd $1;pwd)
start_dir_name=$(basename ${absolute_path}) 
time=$(date +%y%m%d)
end_file_name=${start_dir_name}_${time}.tar.gz # 使用${}选择变量名称
save_path=/root/bf/${end_file_name}
echo "开始备份..."
tar -zcvf $save_path $absolute_path
if [ $? -eq 0 ]
then
	echo "备份成功！"
	echo "备份文件已存储到$save_path中"
else
	echo "备份失败!"
fi
exit
~~~

## 9 Shell工具

### 9.1 cut

基本用法

```sh
cut [选项参数]  filename
```

说明：默认分隔符是制表符 /t

2.选项参数说明

表1-55

| 选项参数 | 功能                   |
| -------- | ---------------------- |
| -f       | 列号，提取第几列       |
| -d       | 按照指定分隔符来分割列 |

案例：数据：cut.txt

~~~
dong shen
guan zhen
wo  wo
lai  lai
le  le
~~~

1基本操作：

~~~sh
cut -d " " -f 1 cut.txt # 表示以" "为分隔符分割列后，获取第一列的数据
# 结果：
dong
guan
wo
lai
le
~~~

2配合管道符使用：

**使用grep提取行，使用cut提取列**

操作：

~~~sh
cat /etc/passwd | grep bash$ | cut -d ":" -f 1,2,3 # 首先获取以bash结尾的行，然后以":"作为分隔符，获取分隔后的第1,2,3列
# 相当于
cat /etc/passwd | grep bash$ | cut -d ":" -f -3 # 获取1,2,3列
~~~

3提取ip地址：

![img/cut的使用.png](D:\alwaysUse\notes\myNotes\Linux\img\cut的使用.png)

**注意**：标记处为10的原因： inet前面有8个空格，每个占了一列，inet是第9列，ip地址是第10列，inet与ip地址中间的空格不是列。

### 9.2 awk(gawk)

一个强大的文本分析工具，把文件逐行的读入，**以空格为默认分隔符**将每行切片，切开的部分再进行分析处理。

1基本用法

awk [选项参数] ‘/pattern1/{action1} ,/pattern2/{action2}...’ filename

pattern：正则表达式

action：在找到匹配内容时所执行的一系列命令(脚本)

2选项参数说明

| 选项参数 | 功能                 |
| -------- | -------------------- |
| -F       | 指定文件分隔符       |
| -v       | 赋值一个用户定义变量 |

例子：

#### -F 指定分隔符

~~~sh
cat /etc/passwd | awk -F ":" '/^root/{print $7}' 
# 对/etc/passwd文件的内容，行以^root进行匹配，列以":"作为分隔，把结果的每个列作为参数传递给{}脚本进行执行
res=$(cat /etc/passwd | awk -F ":" '/^root/{print $7}') # print 还可以把第7列的结果赋值给变量res
~~~

使用awk切割ip地址：

![D:\alwaysUse\notes\myNotes\Linux\img\cut的使用.png](D:\alwaysUse\notes\myNotes\Linux\img\cut的使用.png)

~~~sh
ifconfig | awk '/netmask/{print $2}' # 默认以" "作为分隔符，对于前面全部是空格的情况，inet是第一行，即$1
~~~



#### 初始化代码块BEGIN与结束代码块END

~~~sh
cat /etc/passwd | awk -F ":" 'BEGIN{开始输出结果...}{print $7}END{输出完毕！}' 
# 结果：
开始输出结果...
bin/bash
...
输出完毕！
~~~

#### -v 自定义变量

使用自定义的变量,把第三列的数字加1:

~~~sh
cat /etc/passwd | awk -F ":" -v i=1 '{print $3+i}' # 在这里进行数值运算不需要加$[]
~~~

#### awk的内置变量

| 变量     | 说明                                   |
| -------- | -------------------------------------- |
| FILENAME | 文件名                                 |
| NR       | 已读的记录数                           |
| NF       | 浏览记录的域的个数（切割后，列的个数） |

案例：

~~~sh
awk -F ":" '{print "文件名:"FILENAEM "行号:" NR "列名:" NF}' /etc/passwd # 注意：在这里不需要$来调用内置变量
# 最后一条结果为："文件名:"/etc/passwd "行号:" 50 "列名:" 7 表示一共有50行数据，每一行有7列
~~~

### 9.3 sort

1  基本语法

sort(选项)(参数)

| 选项 | 说明                     |
| ---- | ------------------------ |
| -n   | 依照数值的大小排序       |
| -r   | 以相反的顺序来排序       |
| -t   | 设置排序时所用的分隔字符 |
| -k   | 指定需要排序的列         |

参数：指定待排序的文件列表

2案例实操

（0）数据准备

```sh
touch sort.sh

vim sort.sh 

bb:40:5.4

bd:20:4.2

xz:50:2.3

cls:10:3.5

ss:30:1.6
```

按照“：”分割后的第三列倒序排序:

```sh
sort -t : -nrk 3 sort.sh 
# 结果：
bb:40:5.4

bd:20:4.2

cls:10:3.5

xz:50:2.3

ss:30:1.6
```

