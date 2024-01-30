

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