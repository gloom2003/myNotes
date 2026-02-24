

# 常用DOS命令



## windows电脑自带的远程工具：mstsc

win + R后，输入mstsc并回车

选择需要连接的电脑ip即可。



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

## 命令解释：



- netstat -aon: 这个命令用于显示所有活动的网络连接、监听的端口以及每个连接对应的可执行程序的进程ID。

- |: 这是一个管道符，它的作用是将前一个命令（netstat -aon）的输出结果，作为后一个命令（findstr "10001"）的输入。

- findstr "10001": 这个命令会从输入中（也就是 netstat 的结果里）搜索并显示所有包含 "10001" 这个字符串的行。

### 1. 可以有多个进程占用同一个端口吗？

**通常情况下，不可以。**

对于一个特定的IP地址，一个TCP或UDP端口在同一时间只能被**一个**进程“监听”（Bind & Listen）。这就像一栋大楼只有一个唯一的门牌号（IP地址+端口号），邮递员（操作系统）需要明确知道把信件（网络数据包）送到哪一户（进程）。如果两个进程都声称自己是这个门牌号的住户，系统就无法正确投递数据了。

当有程序尝试去监听一个已经被占用的IP和端口组合时，操作系统会返回一个错误，通常是 “Address already in use” (地址已被占用)。

**但是，存在一些特殊情况和例外：**

1.  **不同的IP地址**：同一个端口号可以被不同进程绑定在不同的IP地址上。例如，一个进程可以监听 `192.168.1.100:8080`，而另一个进程可以监听 `127.0.0.1:8080`。
2.  **特殊的套接字选项 (`SO_REUSEADDR`)**：在编程中，可以设置一个特殊的套接字选项 `SO_REUSEADDR`。它允许一个进程去绑定一个正处于 `TIME_WAIT` 状态（即刚刚关闭，但仍在等待残余数据包）的端口。这对于需要快速重启的服务器程序非常有用，但它**不**允许两个进程同时在该端口上处于有效的监听状态。
3.  **作为客户端连接**：一个进程在监听端口，但可以有**无数个**客户端进程来**连接**这个端口。您看到的 `netstat` 输出就是这种情况，但这不叫“占用”端口，而是“使用”端口进行通信。

---

### 2. 对 `netstat` 输出的意义解释

您提供的这个输出非常经典，它完美地展示了一个常见的误解。让我们逐行、逐列地分析它。

**命令:** `netstat -aon | findstr "10001"`

**输出:**
```
  协议   本地地址                 外部地址                 状态            PID
  TCP    127.0.0.1:3306         127.0.0.1:10001        ESTABLISHED     5412
  TCP    127.0.0.1:10001        127.0.0.1:3306         ESTABLISHED     24060
```

**这不是两个进程在监听同一个端口。这实际上是同一个TCP连接从两个不同进程的视角看到的样子。**

#### 逐行分析：

*   **第一行: `TCP 127.0.0.1:3306 127.0.0.1:10001 ESTABLISHED 5412`**
    *   **协议**: `TCP`，表示这是一个TCP连接。
    *   **本地地址**: `127.0.0.1:3306`。这是**进程 5412** 的地址。`127.0.0.1` 是本机地址（localhost），而端口 `3306` 是 **MySQL数据库的默认端口**。这是一个关键线索！
    *   **外部地址**: `127.0.0.1:10001`。这是它连接到的对方的地址。
    *   **状态**: `ESTABLISHED`，表示连接已成功建立，双方可以通信。
    *   **PID**: `5412`。这是拥有这个连接的进程ID。
    *   **本行释义**: **有一个PID为 5412 的进程（很可能是MySQL服务器），它正在监听3306端口，并且已经和本机上的10001端口建立了一个连接。**

*   **第二行: `TCP 127.0.0.1:10001 127.0.0.1:3306 ESTABLISHED 24060`**
    *   **协议**: `TCP`。
    *   **本地地址**: `127.0.0.1:10001`。这是**进程 24060** 的地址。
    *   **外部地址**: `127.0.0.1:3306`。这是它连接到的对方的地址，也就是MySQL服务器。
    *   **状态**: `ESTABLISHED`，连接已建立。
    *   **PID**: `24060`。这是拥有这个连接的进程ID。
    *   **本行释义**: **有一个PID为 24060 的进程（这是一个数据库客户端程序），它从本机的 10001 端口发起了对本机 3306 端口（MySQL服务）的连接。**

#### 总结

整个过程就像一次电话通话：

1.  **MySQL服务器（进程 5412）** 就像是公司的总机，它的电话号码是 `3306`，它一直开机等待别人打进来。
2.  **某个应用程序（进程 24060）** 想要查询数据，于是它就从自己的一个分机（操作系统为它临时分配的端口 `10001`）拨通了总机 `3306` 的电话。
3.  双方接通后，`netstat` 就记录下了这次通话：从总机 `3306` 的视角看，它正在和 `10001` 通话；从应用程序 `10001` 的视角看，它正在和 `3306` 通话。

所以，当您想启动自己的程序去**监听** `10001` 端口时，系统会告诉您“地址已被占用”，因为这个端口此刻正被那个应用程序作为客户端端口占用着，用于和数据库进行通信。



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
  
  
  
  如：
  
  ![image-20250225091257283](windows_cmd.assets/image-20250225091257283.png)
  
  
  
  
  
  禁止win7 红蜘蛛：
  
  修改 ： C:/用户/System32下面的  checkrs 文件的名称，并在任务管理器中结束红蜘蛛任务。



## Java 

- `jps` 命令查看所有 Java 进程 ，会显示Java进程的类名

  

- `jstack <PID>` 查看某个 Java 进程（PID）的所有线程状态 

- `jconsole` 来查看某个 Java 进程中线程的运行情况（图形界面）



## 自动清理c盘脚本



~~~sh
rem code begin
 @echo off echo ???????????????????????......
del /f /s /q %systemdrive%\*.tmp
del /f /s /q %systemdrive%\*._mp
del /f /s /q %systemdrive%\*.log
del /f /s /q %systemdrive%\*.gid
del /f /s /q %systemdrive%\*.chk
del /f /s /q %systemdrive%\*.old
del /f /s /q %systemdrive%\recycled\*.*
del /f /s /q %windir%\*.bak
del /f /s /q %windir%\prefetch\*.*
rd /s /q %windir%\temp & md %windir%\temp
del /f /q %userprofile%\recent\*.*
del /f /s /q %userprofile%\AppData\Local\Temp\*.*"
del /f /s /q "%userprofile%\recent\*.*"
echo ?????LJ????
echo. & pause
rem   code end
~~~





Windows 命令 `del /f /s /q C:\*.gid` 的含义如下：

### 命令结构

1. **`del`**
    用于删除指定的文件。
2. **参数**：
   - **`/f`**
      强制删除只读文件。
   - **`/s`**
      在当前目录及其所有子目录中搜索匹配的文件并删除。
   - **`/q`**
      静默模式删除文件，不提示用户确认。
3. **路径和文件类型**：
   - **`C:\*.gid`**
      在 C: 盘根目录及其所有子目录中查找扩展名为 `.gid` 的文件。

### 整体意思

该命令的作用是：

- 在 C: 盘及其所有子目录中，
- 强制删除扩展名为 `.gid` 的所有文件，
- 过程中不会提示确认。

### 注意事项

1. **`.gid` 文件**
    `.gid` 是 Windows 帮助系统生成的索引文件，用于加速帮助文件的访问。删除这些文件不会对系统造成严重影响，但可能导致帮助系统稍慢，因为这些文件会在需要时重新生成。

2. **风险**
    由于 `/f /s /q` 是强制删除操作，一旦执行，删除的文件无法通过回收站恢复，需谨慎使用。建议在确认无重要数据后执行。

3. **建议**
    如果需要测试，先在某个文件夹中执行类似命令：

   ```cmd
   del /f /s /q D:\Test\*.gid
   ```

   以确保命令效果符合预期。



# 我把目标服务器 192.168.110.36(win10系统)上的防火墙都关闭了，如何设置开启对 6001 端口的 TCP 访问

~~~
已经关闭了防火墙，再去配置一个具体的入站规则没有意义对吧？

您提出的这一点非常合理。从逻辑上讲，如果一个功能已经“关闭”，再为它设置规则确实看似没有必要。
~~~



好的，即使您已经关闭了防火墙，配置一个具体的入站规则是更规范和安全的操作。有时候“关闭防火墙”的操作可能因为某些策略没有完全生效。创建一个明确的允许规则可以确保端口访问不被阻止。

请在目标服务器 `192.168.110.36` (Windows 10 系统)上按照以下步骤操作，来添加入站规则以允许对 `6001` 端口的 TCP 访问：

### 通过图形化界面设置

这是最直观的方法：

1.  **打开高级安全防火墙**
    *   按下 `Win` + `R` 键，打开“运行”对话框。
    *   输入 `wf.msc` 然后按回车。这将打开“高级安全 Windows Defender 防火墙”。

2.  **创建新规则**
    *   在左侧面板中，点击“**入站规则**”。
    *   在右侧的“操作”面板中，点击“**新建规则...**”。

3.  **规则类型**
    *   选择“**端口**”，然后点击“下一步”。

4.  **协议和端口**
    *   选择“**TCP**”。
    *   选择“**特定本地端口**”，然后在文本框中输入 `6001`。
    *   点击“下一步”。

5.  **操作**
    *   选择“**允许连接**”。
    *   点击“下一步”。

6.  **配置文件**
    *   保持“域”、“专用”和“公用”三个复选框都勾选。这能确保无论您的网络连接被识别成哪种类型，规则都能生效。
    *   点击“下一步”。

7.  **名称**
    *   为规则起一个描述性的名称，例如 `Allow Face Service Port 6001`。
    *   （可选）在“描述”中添加更多信息。
    *   点击“完成”。

### 如何测试端口是否开放

完成上述设置后，您可以从您的开发电脑上打开 PowerShell 窗口，然后运行以下命令来测试端口是否可以访问：

```powershell
Test-NetConnection -ComputerName 192.168.110.36 -Port 6001
```

*   如果 `TcpTestSucceeded` 的值为 `True`，那么表示从您的电脑到目标服务器的 `6001` 端口的 TCP 连接是通畅的。
*   如果值为 `False`，那么问题可能仍然存在，可能的原因包括网络设备（路由器/交换机）的访问控制列表（ACL）限制，或者服务本身没有在 `192.168.110.36` 的 `6001` 端口上正确监听。

如果端口测试成功，但您的 C# 程序依然连接失败，那么问题就很可能出在您的 C# 代码逻辑或者人脸识别服务本身的行为上。届时，按照我之前的建议，在代码的 `catch` 块中捕获并记录详细的 `WebException` 信息将是定位问题的关键。



# Test-NetConnection命令 实际测试

~~~sh
PS C:\Users\Gloom> Test-NetConnection -ComputerName 192.168.110.36 -Port 6001
警告: TCP connect to (192.168.110.36 : 6001) failed


ComputerName           : 192.168.110.36
RemoteAddress          : 192.168.110.36
RemotePort             : 6001
InterfaceAlias         : WLAN
SourceAddress          : 192.168.110.30
PingSucceeded          : True
PingReplyDetails (RTT) : 4 ms
TcpTestSucceeded       : False



PS C:\Users\Gloom> Test-NetConnection -ComputerName 192.168.110.36 -Port 6000
警告: TCP connect to (192.168.110.36 : 6000) failed


ComputerName           : 192.168.110.36
RemoteAddress          : 192.168.110.36
RemotePort             : 6000
InterfaceAlias         : WLAN
SourceAddress          : 192.168.110.30
PingSucceeded          : True
PingReplyDetails (RTT) : 2 ms
TcpTestSucceeded       : False
~~~



非常感谢您提供这个测试结果！这个结果非常关键，它明确地告诉我们问题所在。

`Test-NetConnection` 的结果显示 `PingSucceeded: True` 但 `TcpTestSucceeded: False`，这证实了我之前的猜测：

**问题不在您的 C# 代码中，而是在从您的电脑到服务器 `192.168.110.36` 的 `6001` 端口之间的 TCP 网络层。**

C# 程序之所以连接失败，正是因为这个底层的 TCP 连接无法建立。`ping` 通只能说明网络设备路由正确，但 TCP 端口不通，通常只有两个原因：

1.  **防火墙拦截**：尽管您认为已经关闭了 `192.168.110.36` 上的防火墙，但它很可能仍在起作用。
2.  **服务未监听**：`192.168.110.36` 上根本没有任何应用程序在监听 `6001` 端口。

现在我们需要在**人脸识别服务所在的服务器**（`192.168.110.36`）上进行检查，以确定是上述哪种情况。

请您在服务器 `192.168.110.36` 上执行以下操作：

1.  打开一个命令提示符（CMD）或 PowerShell 窗口。

2.  输入以下命令并按回车：

    ```shell
    netstat -ano | findstr ":6001"
    ```

    *   `netstat -ano` 会列出所有网络连接和监听端口。
    *   `| findstr ":6001"` 会过滤出包含 `:6001` 的行。

3.  **分析命令的输出结果：**

    *   **情况一：命令没有任何输出。**
        这表示服务器上**没有任何程序在监听 6001 端口**。您需要：
        *   确认人脸识别服务已经启动。
        *   检查该服务的配置文件，确保它被配置为使用 `6001` 端口。

    *   **情况二：命令有输出，并且状态是 `LISTENING`。**
        请仔细查看输出的 "本地地址" (Local Address) 那一列。
        *   如果显示 `0.0.0.0:6001` 或 `192.168.110.36:6001`，这表示服务正在监听，并且允许来自其他计算机的连接。如果 `Test-NetConnection` 仍然失败，那么问题 **100% 是由防火墙引起的**。请务必按照我之前提供的步骤，添加一个明确的入站规则来允许 6001 端口，而不是仅仅依赖“关闭防火墙”这个操作。
        *   如果显示 `127.0.0.1:6001`，这表示服务**只在本地监听**，拒绝来自网络上其他计算机的连接。您需要修改人脸识别服务的配置文件，将其监听地址从 `127.0.0.1` (或 `localhost`) 修改为 `0.0.0.0` 或 `192.168.110.36`。

请您根据 `netstat` 的结果进行排查。这会是定位问题的最快方法。



## 解决文件夹移动时的已被使用提示

![image-20250812162830086](C:\Users\Gloom\AppData\Roaming\Typora\typora-user-images\image-20250812162830086.png)