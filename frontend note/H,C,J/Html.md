# html的使用

网页的内容由html控制

## 1.基础知识

### 1.1标题标签 h1-h6：

```html
<h1></h1>
```



### 1.2段落标签 p：

~~~html
<p>
    
</p>
~~~

### 1.3无序列表 ul 列表元素 li 有序列表ol:

ul: unordered list, ol: ordered list

~~~html
<ul>
    
    <li> </li> 
</ul>

<ol>
    
</ol>
~~~

注意：ul里面只搭配li

### 1.4超链接标签a:

~~~html
<a href="http链接或者路径"></a>
~~~

### 1.5图片标签 img:

~~~html
<img src="图片路径" alt="src指定的图片加载失败时显示">
~~~

### 1.6 划分区域的div,span标签：

~~~html
<div>
    
</div>

<h1>我的<span>网页</span></h1>
~~~

### 1.7表格元素

~~~html
    <!-- 表格标签 -->
	<!-- border="1"显示表格的格子 -->
	<table border="1"> 
        <!-- 表头这一行 -->
        <thead> 
            <!-- 字段名 -->
            <th> 
                
            </th>
        </thead>
        <!-- 表体 -->
        <tbody>
            <!-- 一行 -->
            <tr>
                <!-- 每个格子的内容 -->
                <td> </td>
            </tr>
        </tbody>
    </table>
~~~

td:

~~~html
<!-- 表示行跨度为3个格子的大小 -->
<td colspan="3"> Light</td>
<!-- 表示列跨度为3个格子的大小 -->
<td rowspan="3"> Light</td>
~~~

例子：

~~~html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
</head>
<body>
    <!-- border="1"显示表格的格子 -->
    <table border="1">
        <thead>
            <tr>
                <td colspan="4">学生信息</td>
            </tr>
            <th>班级</th>
            <th>姓名</th>
            <th>年龄</th>
            <th>性别</th>
        </thead>
        <!-- 表体 -->
        <tbody>
            <!-- 一行 -->
            <tr>
                <!-- 表示列跨度为3个格子的大小 -->
                <td rowspan="3">1班</td>
                <td>Light</td>
                <td>18</td>
                <td>男</td>
            </tr>
            <tr>
                <td>L</td>
                <td>12</td>
                <td>男</td>
            </tr>
            <tr>
                <td>t</td>
                <td>18</td>
                <td>男</td>
            </tr>
        </tbody>
    </table>
</body>
</html>
~~~

效果：

![https://image.itbaima.net/images/173/image-20230917121028592.png](https://image.itbaima.net/images/173/image-20230917121028592.png)

### 1.8 表单元素

~~~html
<!-- action属性：指定表单提交的地址 -->
    <form action="">
        <!-- 文字标签，for属性：设置点击文字时for属性指定的文本框的光标会闪烁 -->
        <label for="username">用户名:</label>
        
        <!-- 文本框标签，placeholder:占位符 type属性：指定input标签的类型-->
        <input id="username" type="text" placeholder="用户名">
        <label for="password">密码：</label>
        <input id="password" type="password" placeholder="密码">
        
        <!-- 下拉列表标签:  -->
        <!-- name属性:指定提交数据时的变量名 -->
        <!-- 设置size = 2,限制一次显示2个选项 -->
        <select name="" id="" size = 2>
            <option value="">男</option>
            <option value="">女</option>
        </select>
        
        <!-- 单选框类型:redio,指定同一个name意味着只能选择一个-->
        <label>男</label>
        <input name="sex" type="radio">
        <label>女</label>
        <input name="sex" type="radio">
        
        <!-- 复选框(对错框):type = checkout -->
        <label>是否同意本公司的相关协议</label>
        <input type="checkbox">
        
        <!-- 按钮标签：submit按钮点击后会提交数据给后台，而button不会(需要自定义) -->
        <!--  submit默认的value属性为"提交",button默认为空" -->
        <input type="submit" value="登录">
        <input type="button" value="注册">
        
        <!-- <textarea> 元素定义多行输入字段（文本域）： -->
        <textarea name="message" rows="10" cols="30" class="contentInput"></textarea>
    </form>
</body>
~~~

html元素的分类：

- 块元素
- 行内元素
- 行内块元素

|            | 可以设置宽度和高度           | 不可以设置宽度和高度 |
| ---------- | ---------------------------- | -------------------- |
| 独立成行   | 块元素：h1-h6,p,div,ul,li    |                      |
| 不独立成行 | 行内块元素：img,input,button | 行内元素：a,span     |

## 2.vsCode快捷键

1！+Tab :快速生成html页面

2 ul>li*3 + Tab : 生成包含3个<li>的<ul>标签

3 div.pic : 生成class等于pic的div标签

## 3树状结构

同级节点：拥有同一个父节点的两个节点

## 4Html5新特性

### 4.1 有语义的div标签 header：

header:网页头部
nav:导航栏
aside:侧标栏
article:显示文章
section:布局的一部分
footer:网页页脚

其余与div一致

### 4.2 媒体标签 audio,video：

audio

~~~html
  <!-- controls使用浏览器自带的样式设置一个音乐播放器，src指定可以播放medio/1.mp3音乐 -->
    <audio src="medio/1.mp3" controls></audio>
~~~

video

~~~html
	<!-- controls指定了使用浏览器自带的样式设置一个视频播放器，src指定可以播放medio/1.mp4视频 -->
    <!-- autoplay设置视频自动播放(需要搭配muted设置静音才能生效) -->
    <video src="medio/1.mp4" controls autoplay muted></video>
~~~

