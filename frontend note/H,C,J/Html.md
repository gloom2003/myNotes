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
<!-- action属性：指定表单提交的地址 ,form表单默认提交的是QueryString格式的参数，并不是在请求体中-->
    <form action="">
        <!-- 文字标签，for属性：设置点击文字时for属性指定的文本框的光标会闪烁 -->
        <label for="username">用户名:</label>
        
        <!-- 文本框标签，placeholder:占位符 type属性：指定input标签的类型-->
        <input id="username" type="text" placeholder="用户名">
        <label for="password">密码：</label>
        <input id="password" type="password" placeholder="密码">
            <!-- value属性：设置文本框的内容 -->
    	<input type="text" value="123">
            <!-- disabled属性：设置输入框为禁用状态，不能输入内容 -->
    	<input type="text" value="123" disabled="true">
        
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
        
        <!-- 按钮标签：submit按钮点击后会提交数据给后台，而button不会(需要自定义) -->
        <!--  submit默认的value属性为"提交",button默认为空" -->
        <input type="submit" value="登录">
        <input type="button" value="注册">
        
        <!-- <textarea> 元素定义多行输入字段（文本域）： -->
        <textarea name="message" rows="10" cols="30" class="contentInput"></textarea>
    </form>
</body>
~~~



#### 复选框：

~~~html
<!-- 复选框(对错框):type = checkout -->
<label>是否同意本公司的相关协议</label>
<input type="checkbox" checked="checked"><!-- checked="checked"表示设置状态为√的状态 -->
~~~

##### 成品应用：一键多选框的实现

解法1：pink::

~~~html
<!DOCTYPE html>
<html>

<head lang="en">
    <meta charset="UTF-8">
    <title></title>
    <style>
        * {
            padding: 0;
            margin: 0;
        }
        
        .wrap {
            width: 300px;
            margin: 100px auto 0;
        }
        
        table {
            border-collapse: collapse;
            border-spacing: 0;
            border: 1px solid #c0c0c0;
            width: 300px;
        }
        
        th,
        td {
            border: 1px solid #d0d0d0;
            color: #404060;
            padding: 10px;
        }
        
        th {
            background-color: #09c;
            font: bold 16px "微软雅黑";
            color: #fff;
        }
        
        td {
            font: 14px "微软雅黑";
        }
        
        tbody tr {
            background-color: #f0f0f0;
        }
        
        tbody tr:hover {
            cursor: pointer;
            background-color: #fafafa;
        }
    </style>

</head>

<body>
    <div class="wrap">
        <table>
            <thead>
                <tr>
                    <th>
                        <input type="checkbox" id="j_cbAll" />
                    </th>
                    <th>商品</th>
                    <th>价钱</th>
                </tr>
            </thead>
            <tbody id="j_tb">
                <tr>
                    <td>
                        <input type="checkbox" />
                    </td>
                    <td>iPhone8</td>
                    <td>8000</td>
                </tr>
                <tr>
                    <td>
                        <input type="checkbox" />
                    </td>
                    <td>iPad Pro</td>
                    <td>5000</td>
                </tr>
                <tr>
                    <td>
                        <input type="checkbox" />
                    </td>
                    <td>iPad Air</td>
                    <td>2000</td>
                </tr>
                <tr>
                    <td>
                        <input type="checkbox" />
                    </td>
                    <td>Apple Watch</td>
                    <td>2000</td>
                </tr>

            </tbody>
        </table>
    </div>
    <script>
        // 1. 全选和取消全选做法：  让下面所有复选框的checked属性（选中状态） 跟随 全选按钮即可
        // 获取元素
        var j_cbAll = document.getElementById('j_cbAll'); // 全选按钮
        var j_tbs = document.getElementById('j_tb').getElementsByTagName('input'); // 下面所有的复选框
        // 注册事件
        j_cbAll.onclick = function() {
                // 点击复选框时，首先执行的是复选框本身的this.checked属性的更改，然后再执行点击事件
                // this.checked 它可以得到当前复选框的选中状态如果是true 就是选中，如果是false 就是未选中
                console.log(this.checked);// 返回类型为boolean
                for (var i = 0; i < j_tbs.length; i++) {
                    j_tbs[i].checked = this.checked;
                }
            }
            // 2. 下面复选框需要全部选中， 上面全选才能选中。做法： 给下面所有复选框绑定点击事件，
            // 每次点击，都要循环查看下面所有的复选框是否有没选中的，如果有一个没选中的， 
            // 上面全选就不选中。
        for (var i = 0; i < j_tbs.length; i++) {
            j_tbs[i].onclick = function() {
                // flag 控制全选按钮是否选中
                var flag = true;
                // 每次点击下面的复选框都要循环检查这4个小按钮是否全被选中
                for (var i = 0; i < j_tbs.length; i++) {
                    if (!j_tbs[i].checked) {
                        flag = false;
                        break; // 退出for循环 这样可以提高执行效率 因为只要有一个没有选中，剩下的就无需循环判断了
                    }
                }
                j_cbAll.checked = flag;
            }
        }
    </script>
</body>

</html>
~~~

解法2：my

~~~html
<!DOCTYPE html>
<html>

<head lang="en">
    <meta charset="UTF-8">
    <title></title>
    <style>
        * {
            padding: 0;
            margin: 0;
        }
        
        .wrap {
            width: 300px;
            margin: 100px auto 0;
        }
        
        table {
            border-collapse: collapse;
            border-spacing: 0;
            border: 1px solid #c0c0c0;
            width: 300px;
        }
        
        th,
        td {
            border: 1px solid #d0d0d0;
            color: #404060;
            padding: 10px;
        }
        
        th {
            background-color: #09c;
            font: bold 16px "微软雅黑";
            color: #fff;
        }
        
        td {
            font: 14px "微软雅黑";
        }
        
        tbody tr {
            background-color: #f0f0f0;
        }
        
        tbody tr:hover {
            cursor: pointer;
            background-color: #fafafa;
        }
    </style>

</head>

<body>
    <div class="wrap">
        <table>
            <thead>
                <tr>
                    <th>
                        <input type="checkbox" id="j_cbAll" />
                    </th>
                    <th>商品</th>
                    <th>价钱</th>
                </tr>
            </thead>
            <tbody id="j_tb">
                <tr>
                    <td>
                        <input type="checkbox" />
                    </td>
                    <td>iPhone8</td>
                    <td>8000</td>
                </tr>
                <tr>
                    <td>
                        <input type="checkbox" />
                    </td>
                    <td>iPad Pro</td>
                    <td>5000</td>
                </tr>
                <tr>
                    <td>
                        <input type="checkbox" />
                    </td>
                    <td>iPad Air</td>
                    <td>2000</td>
                </tr>
                <tr>
                    <td>
                        <input type="checkbox" />
                    </td>
                    <td>Apple Watch</td>
                    <td>2000</td>
                </tr>

            </tbody>
        </table>
    </div>
    <script>
        // 1. 全选和取消全选做法：  让下面所有复选框的checked属性（选中状态） 跟随 全选按钮即可
        // 获取元素
        var j_cbAll = document.getElementById('j_cbAll'); // 全选按钮
        var j_tbs = document.getElementById('j_tb').getElementsByTagName('input'); // 下面所有的复选框
        var count = 0;
        // 注册事件
        j_cbAll.onclick = function() {
                // 点击复选框时，首先执行的是复选框本身的this.checked属性的更改，然后再执行点击事件
                // this.checked 它可以得到当前复选框的选中状态如果是true 就是选中，如果是false 就是未选中
                console.log(this.checked);// 返回类型为boolean
                if(this.checked){
                    count = 4;
                }else{
                    count = 0;
                }
                for (var i = 0; i < j_tbs.length; i++) {
                    j_tbs[i].checked = this.checked; // 注意：没有引号包裹
                }
            }
            // 2. 下面复选框需要全部选中， 上面全选才能选中。做法： 给下面所有复选框绑定点击事件，
            // 每次点击，都要循环查看下面所有的复选框是否有没选中的，如果有一个没选中的， 
            // 上面全选就不选中。
        for (var i = 0; i < j_tbs.length; i++) {
            j_tbs[i].onclick = function() {
                if(this.checked){
                    count++;
                }else{
                    count--;
                }
                if(count === j_tbs.length){
                    j_cbAll.checked = true;
                }else{
                    j_cbAll.checked = false;
                }
            }
        }
    </script>
</body>

</html>
~~~





### html元素的分类：

- 块元素
- 行内元素
- 行内块元素

|            | 可以设置宽度和高度           | 不可以设置宽度和高度 |
| ---------- | ---------------------------- | -------------------- |
| 独立成行   | 块元素：h1-h6,p,div,ul,li    |                      |
| 不独立成行 | 行内块元素：img,input,button | 行内元素：a,span     |

### 1.9 文本域 textarea

一个大文本框

~~~html
<textarea>
</textarea>
~~~



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



## 待定


