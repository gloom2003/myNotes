# shell的使用

## 1基础知识

![img/1700901520313.png](img/1700901520313.png)

Linux的命令行界面（进程）本质就是一个shell解析器，把用户输入的命令进行解析，翻译给操作系统(Linux内核),以此让操作系统操作硬件。

shell的执行方式：写一句，解释一句，执行一句

Shell的两种主要语法类型有Bourne和C,这两种语法彼此
不兼容。Bourne家族主要包括sh、ksh、Bash(Linux的标准)、psh、
zsh;C家族主要包括：csh、tcsh

### 1.1 echo 相当于println()命令 

#### 1.1.1 $的使用 调用环境变量

查看当前使用的shell解释器，查看SHELL变量，$表示调用此变量

~~~sh
echo $SHELL
~~~

打印的内容中**有空格时需要使用双引号包裹**起来

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
# chmod 755 hello.sh
chmod u+x hello.sh
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

**注意**:在子shell中能够看到全局变量，也可以进行修改，但是，**在子shell中修改后的结果只作用于这个子shell中**，其他的子shell或父shell无法看到修改后的结果，在他们看来，修改后的全局变量的值仍然不变

（相当于：在子shell中的全局变量其实是一个局部变量但是被赋值为全局变量的值，所以在子shell中能够看到全局变量，也可以进行修改，其他的子shell或父shell无法看到修改后的结果）。

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

#### $*、$@ 获取传入的所有参数

1 当$*与$@ **不被双引号“”包含时**，$*与$@都一样，是一个存储着所有参数的数组，使用增强for循环遍历，都以$1 $2 …$n的形式输出所有参数。

2 当它们**被双引号“”包含时**，“$*”会将所有的参数作为一个整体，以“$1 $2 …$n”的形式输出所有参数；

“$@”会将各个参数分开，以“$1” “$2”…”$n”的形式输出所有参数。

例如：

~~~sh
#!/bin/bash
echo '=======$*========'
for param in "$*" # 必须使用""包裹起来才能够看出$*与$@的区别，否则两个的作用都可以遍历到每一个参数
do
	echo param # 输入: a b c d 结果: a b c d
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



#### $? 获取上一次命令的返回状态

$？ （功能描述：获取最后一次执行的命令的返回状态。如果这个变量的值为0，证明上一个命令正确执行；如果这个变量的值为非0（具体是哪个数，由命令自己来决定），则证明上一个命令执行不正确了。）

**注意**：获取的不是返回值而是状态，0表示正常，其他表示异常。

运算符

## 3 数值运算

在bash中，**变量默认类型都是字符串类型**，无法直接进行数值运算。

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

**规律**：都是-符号开头，都是两个字母，除了equal外都是两个单词的缩写

如：

~~~sh
z=2
[ $z -ge 8 ]
echo $? # 结果：1 表示false
~~~

（2）按照文件权限进行判断

-r 有读的权限（read）         -w 有写的权限（write）

-x 有执行的权限（execute）

**注意**： 判断符号-r是写在前面的

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

例如：save_path不是一个目录时

~~~sh
if [ ! -d ${save_path} ];then
	echo "输入的不是一个目录"
fi
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

-a : and

-o : or

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

#### 形式1 普通for循环:

~~~sh
for (( i=0;i<=100;i++ )) 
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

#### 形式2 增强for循环：

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

**注意**：如果路径的最后为/,则会查找倒数第二个/并且返回值不包含第一个/

如：

~~~sh
basename /root/下载/ # 结果： 下载
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
	res=$[ $1 + $1 ]
	echo $res # 不使用return而是使用echo来 返回结果
}
read -p "请输入第一个数字" a
read -p "请输入第二个数字" b
num=$(add $a $b) # 调用函数，传入参数,接收返回值
echo "结果："$num
~~~





## 8 编写好的脚本

#### 根据输入的路径进行备份：

传入需要备份的目录的路径（相对路径与绝对路径都可以）,能够自动把这个路径下面的文件压缩然后备份到/root/bf这个路径下面，/root/bf这个路径不存在则自动创建。

老师版本：

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
# 确定备份后的压缩文件名：
# 使用${}引用变量
start_dir_name=$(basename ${absolute_path}) 
# 显示当前日期并且转换为231126的格式
time=$(date +%y%m%d) 
end_file_name=${start_dir_name}_${time}.tar.gz 
save_path=/root/bf/${end_file_name}
# 如果保存处的目录不存在则创建
[ ! -d ${save_path} ] && mkdir -p ${save_path}

echo "开始备份..."
tar -zcvf $save_path $absolute_path # 注意：调用 ./bf.sh /root/test时，压缩包中的目录结构也为/root/test/xx.xx文件而不是单独一个test目录
if [ $? -eq 0 ]
then
	echo "备份成功！"
	echo "备份文件已存储到$save_path中"
else
	echo "备份失败!"
fi
~~~

my版本：

~~~sh
#!/bin/bash

# 备份脚本

# 判断传入的参数数量是否为一个
if [ $# -ne 1 ];then
        echo "需要一个参数，请输入要备份的路径!"
        exit
fi

# 判断输入的路径是否是一个目录
if [ -d $1 ];then
        echo "路径校验成功。"
else
        echo "路径校验失败，你输入的不是一个目录!";
        exit
fi

# 转换路径，处理传入的路径为相对路径的情况
abs_path=$(cd $1;pwd)

# 设置生成的压缩备份的文件名
dir_name=$(basename $abs_path)
time=$(date +%Y-%m-%d_%H%M%S)
file_name=${dir_name}_${time}.tar.gz

# 备份
echo "开始备份..."
# 把目录打包压缩到/root/bf目录下
tar -zcvf /root/bf/${file_name} ${abs_path} # 注意：调用 ./bf.sh /root/test时，压缩包中的目录结构也为/root/test/xx.xx文件
# over提示
if [ $? -eq 0 ];then
        echo "备份成功！"
        echo "已经备份至/root/bf/${file_name}"
else
        echo "出现错误，备份失败！"
fi

~~~

**设置定时任务**：

查看当前所有的定时任务

~~~sh
crontab -l
~~~

创建一个定时任务

~~~sh
crontab -e
~~~

设置定时任务的内容：

每天2点开始执行

~~~sh
# 格式： cron表达式 要执行脚本的路径 参数
# 分别表示 分钟、小时、一个月的第几天、第几个月、星期几  注意：这个cron表达式没有对秒的控制,也没有?的限制
0 2 * * * /root/bf.sh /root/图片 # 表示执行/root/bf.sh这个脚本，脚本需要的参数为/root/图片
~~~

删除定时任务：
 方法一：使用`crontab -r`命令

```sh
crontab -r
```

这会删除当前用户的所有定时任务。系统将提示你确认删除操作。

方法二：使用`crontab -l`和`crontab -r`命令

1. 使用`crontab -l`命令列出当前用户的定时任务。

```sh
crontab -l
```

1. 找到要删除的定时任务的行号。
2. 使用`crontab -r [行号]`删除指定行的定时任务。

```sh
crontab -r [行号]
```

方法三：编辑定时任务文件

1. 使用下面的命令编辑当前用户的定时任务文件：

```sh
crontab -e
```

1. 找到并删除你想要删除的定时任务的行。
2. 保存文件并退出编辑器。

#### 备份数据库中的文件

1 如果备份目录不存在，则自动生成备份目录

2 使用mysqldump命令获取数据库文件进行压缩 为sql.gz文件并且存放到目录中

3 把单个目录打包为tar.gz后缀的文件,解压出来后只带一个文件夹,文件夹里面的压缩包才是要数据库文件

4 删除10天前的备份文件,防止占空间

~~~sh
#!/bin/bash
# 专门备份数据库的脚本

# 获取备份的时间 
DATE_TIME=$(date +%Y-%m-%d_%H%M%S) # 不能使用 冒号:,不然解压会失败
BASE_PATH=/data/backup/db
# 数据库相关的配置信息
DB_NAME=test
FILE_NAME=${DB_NAME}${DATE_TIME}
BACKUP_PATH=${BASE_PATH}/${DB_NAME}
# echo "备份后的数据库文件全名为：${FILE_NAME}.sql.gz"
echo "备份文件的存储路径为：${BASE_PATH}"
DB_USER=root
DB_PASSWORD=123456
HOST=localhost
# 如果备份目录不存在则创建
[ ! -d ${BACKUP_PATH} ] && mkdir -p ${BACKUP_PATH} # 创建/data/backup/db/test目录

# mysqldump命令登录数据库获取一个数据库文件，使用|给到gzip命令进行压缩，重定向到xxx.sql.gz文件
# mysqldump -u${DB_USER} -p${DB_PASSWORD} --host=${HOST} -q -R -databases ${DB_NAME} | gzip > ${BACKUP_PATH}/${FILE_NAME}.sql.gz 
# 因为没有安装数据库，所以模拟备份数据库文件
cd ${BACKUP_PATH}
touch 1.txt # /data/backup/db/test/1.txt
gzip 1.txt # /data/backup/db/test/1.txt.gz
# 先切换到BASE_PATH目录再压缩整个DB_NAME目录为tar.gz文件
cd ${BASE_PATH} # /data/backup/db
tar -zcvf ${BASE_PATH}/${FILE_NAME}.tar.gz ${DB_NAME} # 只压缩了test目录,生成/data/backup/db/${FILE_NAME.tar.gz}文件
echo "备份好的压缩文件名为${FILE_NAME}"
# 查找并删除10天前备份的数据库文件
cd $BASE_PATH # /data/backup/db
find -atime +10 -name '*.tar.gz' -exec rm -rf {} \; # 还没有测试通过
# 删除已经压缩好的目录
rm -rf ${BACKUP_PATH}
echo "备份${DB_NAME}数据库成功！"

~~~

#### 简化Docker部署

写一个xshell脚本，用于简化docker的部署操作，主要的命令有下面5条：
cd /home/wyu
docker compose down
构建前端镜像：docker build -t mattress_frontend:1.2 -f mattress_frontend_dockerfile .
构建后端镜像：docker build -t mattress_backend:1.2 -f mattress_backend_dockerfile .
docker compose up -d

1:

~~~sh
下面是一个用于简化Docker部署操作的Xshell脚本。该脚本包含了你所提供的五条主要命令：

```bash
#!/bin/bash

# 切换到目标目录
cd /home/wyu

# 关闭当前的Docker Compose服务
docker compose down

# 构建前端镜像
docker build -t mattress_frontend:1.2 -f mattress_frontend_dockerfile .

# 检查前端镜像构建是否成功
if [ $? -ne 0 ]; then
    echo "前端镜像构建失败，脚本退出"
    exit 1
fi

# 构建后端镜像
docker build -t mattress_backend:1.2 -f mattress_backend_dockerfile .

# 检查后端镜像构建是否成功
if [ $? -ne 0 ]; then
    echo "后端镜像构建失败，脚本退出"
    exit 1
fi

# 启动Docker Compose服务
docker compose up -d

# 检查服务启动是否成功
if [ $? -eq 0 ]; then
    echo "Docker服务启动成功"
else
    echo "Docker服务启动失败"
    exit 1
fi
```

### 使用说明
1. 将上述脚本内容复制到一个文件中，比如`deploy.sh`。
2. 给脚本赋予可执行权限：
    ```bash
    chmod +x deploy.sh
    ```
3. 运行脚本：
    ```bash
    ./deploy.sh
    ```

### 脚本说明
- `cd /home/wyu`：切换到目标目录。
- `docker compose down`：关闭当前的Docker Compose服务。
- 构建前端和后端镜像的命令带有错误检测，如果构建失败，脚本将输出错误信息并退出。
- `docker compose up -d`：在后台启动Docker Compose服务。
- 每一步操作后均有状态检测，如果操作失败，脚本会终止并输出相应的错误信息。
~~~

2:添加一个需求，用户可能这一次只是想更新前端，那么就不需要构建后端了，只需要构建前端即可，用户也可能只是想更新后端，请实现这个需求

~~~sh
#!/bin/bash
echo "Docker部署脚本"
echo "请选择要构建的部分："
echo "1. 构建前端镜像"
echo "2. 构建后端镜像"
echo "3. 构建前端和后端镜像"
read -p "输入选项 (1/2/3): " choice

$(cd /home/wyu)

# 首先停止所有现有的服务
$(docker compose down)
# 检查服务启动是否成功
if [ $? -eq 0 ]; then
    echo "Docker服务停止成功"
else
    echo "Docker服务停止失败"
    exit 1
fi

# 根据用户的选择进行相应的构建操作
case $choice in
    1)
        echo "正在构建前端镜像..."
        $(docker build -t mattress_frontend:1.2 -f mattress_frontend_dockerfile .)
        # 检查前端镜像构建是否成功
        if [ $? -ne 0 ]; then
            echo "前端镜像构建失败，脚本退出"
            exit 1
        fi
        ;;
    2)
        echo "正在构建后端镜像..."
        $(docker build -t mattress_backend:1.2 -f mattress_backend_dockerfile .)
        # 检查后端镜像构建是否成功
        if [ $? -ne 0 ]; then
            echo "后端镜像构建失败，脚本退出"
            exit 1
        fi
        ;;
    3)
        echo "正在构建前端和后端镜像..."
        $(docker build -t mattress_frontend:1.2 -f mattress_frontend_dockerfile .)
        # 检查前端镜像构建是否成功
        if [ $? -ne 0 ]; then
            echo "前端镜像构建失败，脚本退出"
            exit 1
        fi
        $(docker build -t mattress_backend:1.2 -f mattress_backend_dockerfile .)
        # 检查后端镜像构建是否成功
        if [ $? -ne 0 ]; then
            echo "后端镜像构建失败，脚本退出"
            exit 1
        fi
        ;;
    *)
        echo "无效选项，请重新运行脚本并选择有效的选项。"
        exit 1
        ;;
esac

# 启动所有的服务
$(docker compose up -d)
# 检查服务启动是否成功
if [ $? -eq 0 ]; then
    echo "Docker服务部署成功"
else
    echo "Docker服务部署失败"
    exit 1
fi

这个脚本的工作流程如下：

1. 提供一个菜单，让用户选择他们想要构建的部分。
2. 根据用户的选择，执行相应的Docker构建命令。
3. 在构建完成后，启动所有服务。
~~~

**在Windows中处理后传输到Linux中，要清除一下换行符:**

**查看：**使用vim -b xxx.sh发现有很多^M是什么？

vim -b 的作用:`vim -b`命令用于打开文件时，设置vim的二进制模式。在二进制模式下，Vim 会以二进制方式读取文件内容，而非文本方式，因此可以查看和编辑二进制文件（如图片、可执行文件等）。

在Linux中，当使用vim -b打开一个文件时，^M通常表示回车符（carriage return）。在Windows操作系统中，换行符由回车符和换行符两个字符组成（\r\n），而在Linux系统中通常只使用换行符（\n）。

因此，当你用vim -b打开一个Windows格式的文件时，会看到很多^M字符，表示回车符的存在。如果你需要处理这种情况，可以使用sed工具或其他方法将^M字符去除（例如使用dos2unix命令来转换文件格式）。

**处理：**

~~~sh
sed -i 's/\r//g' xxx.sh
~~~

作用是用来去掉shell脚本文件中的 **回车符（Carriage Return）**。这种操作常用在脚本在Windows和Linux之间传输导致的格式问题，即由于Windows和Linux系统的换行符不同（Windows为CRLF，Linux为LF），在Linux中编辑Windows格式的文件时，会留下回车符，这可能导致一些问题。因此，这条命令的作用就是去掉脚本文件中的回车符，确保其在Linux环境下正确执行。



**希望只有root用户能运行`deploy_docker.sh`脚本**

以下是步骤：

1. **将文件的所有者设置为root：** 使用`chown`命令将文件的所有者设置为root用户。

   ```sh
   sudo chown root:root ./deploy_mattress.sh
   ```

2. **修改文件权限：** 使用`chmod`命令修改文件权限，以便只有文件的所有者（即root）能够执行它。

   ```sh
   sudo chmod 766 ./deploy_mattress.sh
   ```

在完成上述操作后，只有root用户具有读、写和执行`deploy_docker.sh`脚本的权限。



3:给第二个脚本添加上第一个脚本的状态检测，如果操作失败，脚本会终止并输出相应的错误信息

~~~sh
以下是一个示例的Xshell脚本，用于过程化简化Docker操作以及根据用户选择更新前端或后端，并根据第一个脚本的状态进行检测，如任一操作失败，脚本会终止并输出相应的错误信息。

#!/bin/bash

set -e

# 函数用于输出错误信息并退出脚本
error_exit() {
    echo "$1" 1>&2
    exit 1
}

# 进入工作目录
cd /home/wyu || error_exit "无法进入目录 /home/wyu"

# 停止所有服务
docker-compose down || error_exit "停止服务失败"

# 用户选择更新的选项
read -p "请选择更新的部分（1. 前端 2. 后端 3. 全部）： " CHOICE

case $CHOICE in
    1)
        echo "正在构建前端镜像..."
        docker build -t mattress_frontend:1.2 -f mattress_frontend_dockerfile . || error_exit "前端镜像构建失败"
        ;;
    2)
        echo "正在构建后端镜像..."
        docker build -t mattress_backend:1.2 -f mattress_backend_dockerfile . || error_exit "后端镜像构建失败"
        ;;
    3)
        echo "正在构建前端镜像..."
        docker build -t mattress_frontend:1.2 -f mattress_frontend_dockerfile . || error_exit "前端镜像构建失败"
        
        echo "正在构建后端镜像..."
        docker build -t mattress_backend:1.2 -f mattress_backend_dockerfile . || error_exit "后端镜像构建失败"
        ;;
    *)
        error_exit "无效的选择，请选择 1、2 或 3"
        ;;
esac

# 启动所有服务
docker-compose up -d || error_exit "启动服务失败"

echo "操作完成"
```

### 说明：
1. `set -e`：这个命令用于在任一命令失败时终止脚本。
2. `error_exit` 函数：用于输出错误信息并退出脚本。
3. 用户选择更新前端、后端或全部。
4. 在每一步操作中，如发生错误，脚本终止并输出相应的错误信息。
5. 最后，启动所有Docker服务。

运行该脚本时，用户将被提示选择要更新的部分。根据用户的选择，将构建相应的镜像并启动服务。如果任何步骤失败，将输出错误信息并终止脚本。
~~~



### 各种容易忘的xshell符号

- 条件判断：[  ], (( ))
- 命令替换：$() 
- 引用变量：${ num }
- 运算方法：s=$[ $s + $i ]  使用$[  ]进行运算



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

