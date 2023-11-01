# shell的使用

## 1基础知识

shell起到一个翻译官的作用，把Linux命令(Ascii字符)翻译为计算机能够识别的二进制

![https://image.itbaima.net/images/173/image-20231008125948235.png](https://image.itbaima.net/images/173/image-20231008125948235.png)

Shell的两种主要语法类型有Bourne和C,这两种语法彼此
不兼容。Bourne家族主要包括sh、ksh、Bash(Linux的标准)、psh、
zsh;C家族主要包括：csh、tcsh

### 1.1 echo 相当于打印命令 

#### 1.1.1 $的使用 调用环境变量

查看SHELL变量，$表示调用此变量

~~~sh
echo $SHELL
~~~

打印的内容中有空格时需要使用双引号包裹起来,**注意：不能打印！号**

~~~sh
echo "hello world"
~~~

#### 1.1.2 -e参数

参数-e 表示开启对字符的特殊处理

**改变颜色：**

~~~sh
echo -e "\e[1;31m error \e[0m"
~~~

\e[1;31m表示开启颜色(31m表示使用红色)显示

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

~~~sh
vim hello.sh
~~~

注释，表示这是一个标准的shell脚本

~~~sh
#!/bin/bash
echo -e "\e[1;31m error \e[0m"
~~~

#### 1.4.2 脚本运行的两种方式

1.赋予执行权限，直接运行

```sh
chmod 755 hello.sh
./hello.sh
```

2.通过Bash调用执行脚本

```sh
bash hello.sh
```



Bash的基本功能
1、命令别名与快捷键
2、历史命令
3、输出重定向
4、多命令顺序执行
5、Shell中特殊符号
