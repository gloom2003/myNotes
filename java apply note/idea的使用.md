# idea的使用

## 1 快捷键

### 搜索类：

####  ctrl+shift+R 全局搜索 

####  shift + shift 全局搜索类、接口、注解等

#### Ctrl + Fn + F12 在当前类中进行搜索，会展示 类的属性与方法



### 1.2 Alt + 7 显示类的属性、方法、构造器lombok动态添加的也可以查看到

### 1.3  shift + Fn + F6 选中文件、变量、文件夹进行重命名

### Ctrl + Shift + U 选中的字母全部变为大写

### Ctrl + Alt + T  快速生成try-catch等代码

### 清除没有使用的import语句 Ctrl + Alt + O (欧)



### 1.6 Ctrl + D 把剪切板的内容粘贴到下一行

### 1.7 Alt +Fn+ Insert  重写方法、生成构造器、set、get等...

### 1.9  误删代码的解决方法

1. 配置撤销快捷键 setting中搜索keymap,再搜索redo进行设置  
2. 右击类文件，选择local history后再点击show history,可以查看之前写的历史代码

### 1.10 Ctrl + H 查看接口的实现类、类的父子类

### 1.11 选中代码封装为方法 Ctrl + Alt + M

### 1.12 给选中的每一行添加注释 Ctrl + / ，在选中代码的前后添加两行文档注释 Ctrl + Shift + /

### 1.13 快速删除光标所在行  Shift + Delete 或者 Ctrl + Y



## 2 debug 快捷键与技巧

### 2.1 技巧

#### 2.1.1 add to watchs技巧
debug时，右键选中变量，右击选择 add to watchs就能够在debug栏目中看见变量的值

#### 2.1.2 直接进入类的内部是抽象时，使用debug的方式利用F7进入真正执行的方法

#### 2.1.3 全选住一个变量，鼠标右键选择Evaluate Expression + Enter回车，可以使用计算器对这个变量进行操作，并且会实实在在出现效果，相当于执行了新的代码。

### 2.2 快捷键

#### 2.2.1 Ctrl + Alt + 鼠标左键  查看进入接口的实现类

#### 2.2.2 Ctrl + 鼠标左键 进入对应接口、类、方法的内部

#### 2.2.3 Ctrl + 鼠标悬浮 快速查看接口、类、方法的定义信息

#### 2.2.4 Ctrl + Q 光标放在方法上可以显示方法的文档注释信息（如果有的话）

#### 2.2.5 Ctrl + Alt + 方向盘的左键 用于进入类内部后快速回到刚才的界面

#### 2.2.6 Ctrl + Alt + B 光标放在类、接口、方法上时，可以选择其子类(实现类)的相关信息





## 3 idea配置模版

### 3.1 配置关键词替换为代码的模版

如图：在settings中搜索Live Templates即可进入，此图配置了一个myTemplates模版组，里面有一个模版spring-mybatis,配置了当在xml文件中输入spring-mybatis时就会替换为Template Text中的代码内容。

![https://image.itbaima.net/images/173/image-2023110221340873.png](https://image.itbaima.net/images/173/image-2023110221340873.png)

### 3.2 生成一个自定义文件的选项

配置了一个mybatis-cfg.xml文件的选项，设置以后，当想要创建文件时，可以直接点击mybatis-cfg.xml文件，会自动生成含有模板的文件，达到快速创建文件的目的。

![image-20210218211942452](C:\Users\GLOOM\Desktop\for zip\not system\sangGeng files\普通配套资料\Mybatis\img\image-1.png)



## 4 快速生成代码模版

配合idea提示食用更佳：

.var 快速接收变量

psvm main方法

.for 增强for循环升序遍历

~~~java
        ArrayList<Object> objects = new ArrayList<>();
        // objects.for 的结果：
        for (Object object : objects) {
            
        }
~~~



.forr 使用for循环降序遍历  .fori 使用for循环升序遍历

~~~java
public static void main(String[] args) {
        Integer i = 1;
        int num = i;
        ArrayList<Object> objects = new ArrayList<>();
        // objects.forr 的结果：
        for (int i1 = objects.size() - 1; i1 >= 0; i1--) {
            
            
        }
    }
~~~





## 5 设置主题

idea2018的Google主题好用

或者：

Plugins中搜索/tag:Theme,选择Material Theme UI Lite即可



## 配置自动生成序列化id





## 高级断点调试

结合你提供的截图和断点调试（Debug）的特性，我为你介绍几个非常实用但容易被忽略的高级断点技巧。掌握这些，你的调试效率会提升一个量级。

### 1. 线程模式 (Suspend Policy) —— **并发调试核心**

图中你选中的是 `Suspend: All`（默认），这是导致你觉得“后端像单线程”的元凶。
- **All (默认)**：断点触发时，暂停**整个 JVM**。所有线程（包括定时任务、心跳检测、其他用户的请求）全部冻结。
  - **适用场景**：单人开发、调试简单逻辑，不想被后台杂音打扰。
- **Thread**：断点触发时，只暂停**当前执行这行代码的线程**。其他线程照常运行。
  - **适用场景**：**并发测试**（如你要测的抢单、秒杀、重复提交）、生产环境远程调试（不影响其他用户使用）。

注意：每个断点的 Suspend Policy 都是独立的，默认是 All。如果你新建了一个断点或者没改之前的，它就会用默认的 All，一触发就会暂停整个 JVM，或者一放行就把大家都放了。

### 2. 条件断点 (Condition) —— **精准捕获**

图中 `Suspend` 下方有一个 `Condition` 复选框。勾选后，可以在输入框里写 Java 代码（返回 boolean）。
- **用法**：假设你在循环里跑 10000 次，只想看第 9999 次的情况，或者只想抓特定 ID 的请求。
- **示例代码**：
  - `jobId == 10086`
  - `"admin".equals(user.getName())`
  - `list.size() > 0`
- **效果**：只有当条件为 `true` 时，断点才会触发。再也不用狂按 F9 跳过无关请求了。

### 3. 不暂停只打印 (Log: "Breakpoint hit" message / Evaluate and log) —— **无感监控**
有时候你不想打断程序运行，只想看某个变量在运行时的值（类似 `System.out.println`，但不用改代码重启）。
- **操作**：
  1. **取消勾选** `Suspend`（关键！）。
  2. 勾选 `Evaluate and log`。
  3. 在输入框填入你想看的值，比如：`"当前处理人: " + authResponseVO.getHandlerName()`。
- **效果**：程序运行**不会停**，但在 IDEA 的 Console 控制台里会源源不断打印出你设置的日志。这在排查偶发 Bug 或观察数据流向时简直是神器。

### 4. 实例过滤器 (Instance filters)
图中右侧的 `Instance filters`。
- **用法**：如果你有多个对象实例（比如多个 `WebSocketService` 实例，虽然 Spring 默认单例，但在原型模式或多线程对象中常见），你可以指定只拦截**特定对象实例**的断点。
- **操作**：输入特定对象的引用 ID（Debug 时对象后面跟的 `@1234` 那个数字）。

### 5. 异常断点 (Java Exception Breakpoints) —— **自动捕获报错**
在左侧菜单里，除了 `Line Breakpoints`，还有一个闪电图标 `Java Exception Breakpoints`。
- **用法**：点击 `+` 号，添加一个异常，比如 `NullPointerException`。
- **效果**：程序一旦抛出空指针异常，**自动**在抛出异常的那一行暂停。
- **价值**：当你不知道哪里报了错，或者报错被框架吞了（只打印了日志），用这个能瞬间定位“案发现场”。

### 总结建议
对于你现在的并发测试：
1. **必须改**：把 `Suspend` 改为 `Thread`。
2. **可选**：如果你只想测特定作业的并发，可以在 `Condition` 里写 `jobId == 123`，防止被其他测试数据干扰。
