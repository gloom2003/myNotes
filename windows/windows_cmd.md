

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



## 清除占用某个端口的进程

### 1、查看被占用端口对应的 PID

管理员身份打开cmd：

输入命令：

```
netstat -aon|findstr "8081"
```

最后一位数字就是 PID, 这里是 9088。

![img](https://www.runoob.com/wp-content/uploads/2018/07/1530674518-6203-2159693-10d9bae7a6e59b06.png)

### 2、查看指定 PID 的进程

继续输入命令：

```
tasklist|findstr "9088"
```

查看是哪个进程或者程序占用了 8081 端口，结果是：node.exe。

![img](https://www.runoob.com/wp-content/uploads/2018/07/1530674518-3794-2159693-30d1a50103f28cea.png)

### 结束进程

强制（/F参数）杀死 pid 为 9088 的所有进程包括子进程（/T参数）：

```
taskkill /T /F /PID 9088 
```

或者是我们打开任务管理器，切换到进程选项卡，在PID一列查看9088对应的进程是谁，如果看不到PID这一列,如下图：

![img](https://www.runoob.com/wp-content/uploads/2018/07/1530674518-2583-2159693-78c510e9c1023f6e.png)

之后我们就可以结束掉这个进程，这样我们就可以释放该端口来使用了。



## 线程

## windows 

win10:

- 任务管理器可以查看进程和线程数，也可以用来杀死进程 

- `tasklist` 查看进程 

  ~~~sh
  tasklist | findstr java # 查看名字有java的线程
  ~~~

  

- `taskkill` 杀死进程 

  ~~~sh
  taskkill /F /PID 28060 # /F:强制 /PID:指定进程号
  ~~~

  win7: 测试无效
  
  ~~~sh
  ntsd -c q -p PID
  ntsd -c q -pn student.exe
  ~~~
  
  
  
  禁止win7 红蜘蛛：
  
  修改 ： C:/用户/System32下面的  checkrs 文件的名称，并在任务管理器中结束红蜘蛛任务。



## Java 

- `jps` 命令查看所有 Java 进程 ，会显示Java进程的类名

  

- `jstack <PID>` 查看某个 Java 进程（PID）的所有线程状态 

- `jconsole` 来查看某个 Java 进程中线程的运行情况（图形界面）