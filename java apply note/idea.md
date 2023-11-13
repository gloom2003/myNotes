# idea的使用

## 1 快捷键

### 1.1 ctrl+shift+R 全局搜索 

### 1.2 Alt + 7 显示类的属性、方法、构造器

lombok动态添加的也可以查看到

### 1.3  shift + Fn + F6 选中文件、变量、文件夹进行重命名

### 1.4 Ctrl + Alt + 鼠标左键  查看选定接口的实现类

### 1.5 shift + shift 全局搜索类、接口、注解等

### 1.6 Ctrl + D 把剪切板的内容粘贴到下一行

### 1.7 Alt +Fn+ Insert  重写方法、生成构造器、set、get等...

### 1.8 Ctrl + Q 光标放在方法上可以显示方法的文档注释信息（如果有的话）

### 1.9 配置撤销快捷键 setting中搜索keymap,再搜索redo进行设置







## 2 debug 技巧

### 2.1 add to watchs
debug时，右键选中变量，右击选择 add to watchs就能够在debug栏目中看见变量的值

## 3 idea配置模版

### 3.1 配置关键词替换为代码的模版

如图：在settings中搜索Live Templates即可进入，此图配置了一个myTemplates模版组，里面有一个模版spring-mybatis,配置了当在xml文件中输入spring-mybatis时就会替换为Template Text中的代码内容。

![https://image.itbaima.net/images/173/image-2023110221340873.png](https://image.itbaima.net/images/173/image-2023110221340873.png)

### 3.2 生成一个自定义文件的选项

配置了一个mybatis-cfg.xml文件的选项，设置以后，当想要创建文件时，可以直接点击mybatis-cfg.xml文件，会自动生成含有模板的文件，达到快速创建文件的目的。

![image-20210218211942452](C:\Users\GLOOM\Desktop\for zip\not system\sangGeng files\普通配套资料\Mybatis\img\image-1.png)
