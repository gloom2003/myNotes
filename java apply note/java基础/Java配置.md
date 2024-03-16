## win10安装JDK



参考文章：

1. https://blog.csdn.net/qq_41999034/article/details/111604649



在win10中，最好使用绝对路径配置环境变量，还有一点要注意，path中添加的jdk和jre路径要分开写，不要写在同一行。

1. 系统变量中创建JAVA_HOME
   - 变量值：D:\MySoft\jdk （jdk的安装路径，根据自己安装路径填写）

2. 在系统变量path中添加 （jdk，jre的安装路径，根据自己安装路径填写）
   - D:\MySoft\jdk\bin （末尾不用加 ;）
   - D:\MySoft\jdk\jre\bin


有些人重装过jdk或装了多个jdk可能会有类似：`C:\ProgramData\Oracle\Java\javapath`的路径，同样只要**把自己jdk路径值移到最前端**就行了。移动后还是不行就重启电脑。

这两个路径会使我们自己配置的jdk路径不被优先使用，导致找不到java命令。