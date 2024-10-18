

# 常用DOS命令

## 启动某一个服务

输入 net start mysql 启动服务。当然也可以通过“计算机管理-服务和应用程序-服务”中找到mysql开启服务的方式来启动mysql服务。

```bash
net start mysql
```



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
| ipconfig  | 查看本机的私网ip地址：            |

![image-20240802093225649](C:\Users\Gloom\AppData\Roaming\Typora\typora-user-images\image-20240802093225649.png)



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

## bat脚本

例子1：

![1722328901214](C:\Users\Gloom\Downloads\脚本代码图片\1722328901214.png)

解释：

图中的代码是一个批处理文件（Batch script），它用来启动多个 Java 应用程序以及执行另一个批处理文件。每个 `start` 命令都会在一个新的命令提示符窗口中执行。这些命令的具体解释如下：

```plaintext
@echo off
start cmd /c "title eureka && java -jar eureka-server-0.0.1-SNAPSHOT.jar"
start cmd /c "title zuul && java -jar zuul-server-0.0.1-SNAPSHOT.jar"
start cmd /c "title infrastructure && java -jar infrastructure-0.0.1-SNAPSHOT.jar"
start cmd /c "title dljz-base && java -jar dljz-base-0.0.1-SNAPSHOT.jar"
cd D:\GI_project\mosquitto\mosquitto
start .\start.bat
pause
```

具体解释如下：

1. `@echo off`：关闭命令行回显，以免显示出每一行的命令。
2. `start cmd /c "title eureka && java -jar eureka-server-0.0.1-SNAPSHOT.jar"`：在一个新的命令提示符窗口中，设置窗口标题为 "eureka"，并运行 `eureka-server-0.0.1-SNAPSHOT.jar` 文件。
3. `start cmd /c "title zuul && java -jar zuul-server-0.0.1-SNAPSHOT.jar"`：在一个新的命令提示符窗口中，设置窗口标题为 "zuul"，并运行 `zuul-server-0.0.1-SNAPSHOT.jar` 文件。
4. `start cmd /c "title infrastructure && java -jar infrastructure-0.0.1-SNAPSHOT.jar"`：在一个新的命令提示符窗口中，设置窗口标题为 "infrastructure"，并运行 `infrastructure-0.0.1-SNAPSHOT.jar` 文件。
5. `start cmd /c "title dljz-base && java -jar dljz-base-0.0.1-SNAPSHOT.jar"`：在一个新的命令提示符窗口中，设置窗口标题为 "dljz-base"，并运行 `dljz-base-0.0.1-SNAPSHOT.jar` 文件。
6. `cd D:\GI_project\mosquitto\mosquitto`：将当前目录切换到 `D:\GI_project\mosquitto\mosquitto` 目录。
7. `start .\start.bat`：在新的命令提示符窗口中运行 `start.bat` 文件。
8. `pause`：暂停执行，等待用户按下任意键后继续。这通常用于让用户查看前面的输出或结果。

这个批处理文件的主要目的是启动多个 Java 应用程序和一个额外的批处理脚本，并在每个应用程序各自的命令提示符窗口中运行，方便进行管理和监控。

例子2：

![1722328919023](C:\Users\Gloom\Downloads\脚本代码图片\1722328919023.png)

解释：

图中的代码是一个批处理文件（Batch script），设置了一些环境变量并启动多个 Java 应用程序。代码分为几个部分：设置环境变量、启动应用程序。具体解释如下：

```plaintext
@echo off

::mongodb
set mongo-uri=mongodb://bitds:bitds2019@localhost:27017/bitds

::mqtt
set mqtt-hostname=localhost
set mqtt-username=bjtds
set mqtt-port=1883

::mysql
set mysql-database=bjtds
set mysql-username=root
set mysql-password=123456

::user photo location
set user-photo-save-path=C:/gtzz_deploy/sr-root/static-resources/photos

start cmd /c "title eureka && java -jar eureka-server-0.0.1-SNAPSHOT.jar"
start cmd /c "title zuul && java -jar zuul-server-0.0.1-SNAPSHOT.jar"
start cmd /c "title infrastructure && java -jar infrastructure-0.0.1-SNAPSHOT.jar"
start cmd /c "title repairplan && java -jar prodmge-repairplan-0.0.1-SNAPSHOT.jar"
start cmd /c "title repairstandard && java -jar prodmge-repairstandard-0.0.1-SNAPSHOT.jar"
start cmd /c "title prodscheduling && java -jar prodmge-prodscheduling-0.0.1-SNAPSHOT.jar"
start cmd /c "title repairprocess && java -jar prodmge-repairprocess-0.0.1-SNAPSHOT.jar"
start cmd /c "title tool && java -jar tool-service-0.0.1-SNAPSHOT.jar"
start cmd /c "title parts && java -jar parts-service-0.0.1-SNAPSHOT.jar"
pause
```

具体解释如下：

1. `@echo off`：关闭命令行回显，以免显示出每一行的命令。

2. `::mongodb` 到 `::user photo location`：注释部分，用于标识不同的配置部分。

3. `set mongo-uri=mongodb://bitds:bitds2019@localhost:27017/bitds`：设置 MongoDB 的连接 URI。

4. `set mqtt-hostname=localhost`：设置 MQTT 的主机名为 localhost。
   `set mqtt-username=bjtds`：设置 MQTT 的用户名为 bjtds。
   `set mqtt-port=1883`：设置 MQTT 的端口为 1883。

5. `set mysql-database=bjtds`：设置 MySQL 数据库名为 bjtds。
   `set mysql-username=root`：设置 MySQL 用户名为 root。
   `set mysql-password=123456`：设置 MySQL 密码为 123456。

6. `set user-photo-save-path=C:/gtzz_deploy/sr-root/static-resources/photos`：设置用户照片保存路径。

7. `start cmd /c "title eureka && java -jar eureka-server-0.0.1-SNAPSHOT.jar"`：在新的命令提示符窗口中运行 eureka-server 的 Java 程序，并将窗口标题设置为 "eureka"。

8. 其他 `start cmd /c "title ... && java -jar ..."` 命令类似，用于启动不同的 Java 程序并设置各自的窗口标题。

9. `pause`：暂停执行，等待用户按下任意键后继续。这通常用于让用户查看前面的输出或结果。

这个批处理文件通过设置环境变量和启动多个 Java 应用程序，简化了系统的启动和配置过程。每个应用程序在各自的命令提示符窗口中运行，方便进行管理和监控。

## 把各种软件注册为服务

**nssm与winsw**

有时候我们需要将bat、exe等文件作为Windows的服务，我们可以使用bat、nssm等工具将此类文件设置为Windows服务，此处我们介绍使用WinSW这个工具。参考：https://blog.csdn.net/qubernet/article/details/120290136

例子：C:\Users\Gloom\alwaysUse\北铁所\OAEX系统\初始资料\环境搭建\检修项目软件依赖window\系统部署教程20190719-02



## windows 进程

win10:

- 根据进程号进行删除

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
  
  
  
  
  
  > 参考：https://blog.csdn.net/weixin_43734095/article/details/106990775
  >
  > 根据进程名称进行删除
  
  查看端口 8989 被哪个进程占用：
  
  ```sh
  netstat -ano | findstr "8989"
  ```
  
  查看进程号为 3736 对应的进程；由下图可以看出，是被 java.exe 占用了：
  
  ```sh
  tasklist | findstr "3736"
  ```
  
  结束该进程：
  
  ```sh
  taskkill /f /t /im java.exe
  ```
  
  
  
  禁止win7 红蜘蛛：
  
  修改 ： C:/用户/System32下面的  checkrs 文件的名称，并在任务管理器中结束红蜘蛛任务。



## Java 

- `jps` 命令查看所有 Java 进程 ，会显示Java进程的类名

  

- `jstack <PID>` 查看某个 Java 进程（PID）的所有线程状态 

- `jconsole` 来查看某个 Java 进程中线程的运行情况（图形界面）