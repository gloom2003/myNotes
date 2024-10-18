# Css的使用

网页的样式由css控制

## 特殊字符

空格：

```html
&nbsp;
```



## 1.基础知识

### 1.1常用选择器

- 元素:根据标签找 例如： h1{}

- 类  .test{}

- id #id{}

- 通配符 *{}

  权重：id(100)>类(10)>元素(1)

  使用 !important设置某个属性为最高权重

  ~~~html
  <style>
      .box #txt{
          color:red !important;
      }
  </style>
  ~~~

  

~~~html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    <!-- 可以在style标签内部写css -->
    <style>
        /* 选择器{属性名：属性值} */
        h1{
            color: red;
        }
    </style>
</head>
<body>
    <h1>hello world</h1>
</body>
</html>
~~~

#### 1.1.1伪类选择器(增加行为):

~~~html
<style>
        .box{
            width: 100px;
            height: 100px;
            background-color: red;
        }
        /* 伪类选择器：hover(悬停)表示鼠标移动进入区域时执行下面的css设置颜色为蓝色 */
        .box:hover{
            background-color: blue;
        }
</style>
~~~

#### 1.1.2 伪元素选择器(增加元素):

~~~html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    <style>
         /* content表示在指定标签前面添加内容0 */
        .box::before{
            content: "0";
        }
    </style>
</head>
<body>
    <div class="box">123</div>
</body>
</html>
~~~

#### 1.1.3 只选择子级不选择孙级的选择器:

~~~css
/* 表示只选择box下面一级的所有li标签*/
.box>li{
    
}

 /* 移动到menu类的子级li时,操作的是menu子级下的sub-menu类下面的子级li */
        .menu>li:hover > .sub-menu>li{
            width: 50px;
            height: 30px;
            
        }
/* 错误选择器 ,直接使用.show{}即可*/
.menu>.show{
    
}
~~~



### 1.2常用css属性(字体，颜色...)

- 字体大小：font-size  Chrome浏览器中最小为12px
- 字体宽度 font-weight: bold;  （加粗）相当于 font-weight: 700; 
- 字体颜色: color
- 宽度：width
- 高度：height
- 背景颜色：background-color
- 文本水平对齐(align): text-align : center       解释：可ctrl + f 搜索text-align 或者 参考：https://developer.mozilla.org/zh-CN/docs/Web/CSS/text-align
- 文本行高: line-height
- 设置字体：css设置黑体样式的方法：可以利用font-family属性来进行设置，如【font-family: 黑体;】。font-family属性用于指定一个元素的字体。
- 防止文字换行：`white-space: nowrap;` 

例子：

~~~html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    <!-- 可以在style标签内部写css,嵌入样式 -->
    <style>
        .test{
            color: white;
            background-color: black;
            text-align: center;
            line-height: 100px;
        }
        img{
            width: 600px;
            /* auto属性值：表示自动按照图片原来的比例进行展示 */
            height: auto;
        }
    </style>
</head>
<body>
    <h1 class="test">hello world</h1>
    <img src="tou.jpg" alt="图片加载失败">
</body>
</html>
~~~

### 1.3引入css的方式

1.嵌入样式 

```html
<style> 
    img{
            width: 600px;
            /* auto属性值：表示自动按照图片原来的比例进行展示 */
            height: auto;
        }
</style>>
```



2.内联样式

~~~html
<h1 style="color:red">
    
</h1>
~~~

3.引入文本样式:

~~~html
<!-- 表示引入style目录下的index.css文件-->
<link rel="stylesheet" href="style/index.css"/>
~~~

4）在Vue文件中引入CSS

![image-20240902161035261](E:\alwaysUse\notes\myNotes\frontend note\H,C,J\Css.assets\image-20240902161035261.png)



## 2盒子模型的css属性

### 2.1 边框 board：

**board-width** 1px;

**board-style** :

- none 无
- dotted 点状边框
- dashed 虚线边框
- solid 实线边框
- double 双线边框

**board-color** red;

上可简写为：

```html
<style>
    .box{
        border:1px solid red
    }
</style>

```

### 2.2 外边距 margin

(边框与外部之间的距离):

**margin-: 上 右 下 左 顺时针**

- top
- right
- bottom
- left

一般写法: margin-left:100px;

可简写为：

- margin:100px 0 0 100px   0相当于0px
- margin:100px 50px  表示上下设置为100px(上优先),左右设置为50px(左优先)
- margin:0px               表示上下左右都设置为0px
- margin:0 auto           auto表示自动设置左右两边外边距相等



### 2.3 内边距 padding

(边框与内部之间的距离):

**padding-:**

- top
- right
- bottom
- left





### 2.4 其他css：

#### 2.4.1**使实际宽度 =设置的宽度** box-sizing

css设置**box-sizing:border-box;**后：

元素的**实际宽度 =** width属性**设置的宽度**

元素的实际高度 = width属性设置的高度



#### 2.4.2 li的css

无序列表(ul)中列表元素(li)的css样式:

~~~html
.fruits ul{
<!-- 表示设置无序列表的点在fruits边框的内部-->
	list-style: inside;
<!-- 表示去除无序列表的点-->
	list-style: none;
}
~~~

#### 2.4.3 **清除所有默认的内外边距**

~~~html
*{
            margin: 0;
            padding: 0;
  }
~~~

#### 2.4.4**html元素的分类 display：**

- 块元素
- 行内元素
- 行内块元素

|            | 可以设置宽度和高度           | 不可以设置宽度和高度 |
| ---------- | ---------------------------- | -------------------- |
| 独立成行   | 块元素：h1-h6,p,div,ul,li    |                      |
| 不独立成行 | 行内块元素：img,input,button | 行内元素：a,span     |



通过设置display属性可以进行改变：

把a标签转换为块元素

~~~css
a{
            display:block 
 }
~~~

- block  标签转换为块元素
- inline 转换为行内元素
- inline-block  转换为行内块元素: 可以把内容显示在同一列上：![image-20240828141350202](E:\alwaysUse\notes\myNotes\frontend note\H,C,J\Css.assets\image-20240828141350202.png)![image-20240828141541245](E:\alwaysUse\notes\myNotes\frontend note\H,C,J\Css.assets\image-20240828141541245.png)以达到文字居中效果：![image-20240828141729624](E:\alwaysUse\notes\myNotes\frontend note\H,C,J\Css.assets\image-20240828141729624.png)
- none  把元素隐藏起来



### 2.5 示例:

~~~html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    <!-- 可以在style标签内部写css -->
    <style>
        *{
            margin: 0;
            padding: 0;
        }
        .fruits1{
            width: 800px;
            border: 1px solid red;
            margin: 0 auto 10px auto;
            padding-left: 20px;
            padding-top: 5px;
            box-sizing:border-box;
            
        }
        .fruits2{
            width: 800px;
            border: 1px solid blue;
            margin: 10px auto;
            padding-left: 20px;
            padding-top: 5px;
            box-sizing:border-box;
        }
        .fruits1 li,.fruits2 li{
            list-style: inside;
        }
    </style>
    <!-- 表示引入style目录下的index.css文件-->
    <link rel="stylesheet" href="style/index.css">
</head>
<body>
    <div class="fruits1">
        <h2>我喜欢的水果</h2>
        <ul>
            <li>葡萄</li>
            <li>哈密瓜</li>
            <li>石榴</li>
        </ul>
    </div>
    <div class="fruits2">
        <h2>我喜欢的运动</h2>
        <ul>
            <li>乒乓球</li>
            <li>羽毛球</li>
            <li>足球</li>
        </ul>
    </div>
</body>
</html>
~~~

效果：

![https://image.itbaima.net/images/173/image-20230918102482551.png](https://image.itbaima.net/images/173/image-20230918102482551.png)

## 3浮动布局：实现两个div在同一行显示:

在两个div中分别指定：

~~~html
div{
    float: left;
}
~~~

float: left;元素会向左边靠

**但是float会产生负面效果，使标签不占位置**，

**解决方法1：**

使用clear: both;

~~~css
.clear{
	clear: both;
}
~~~

设置在空div上，表示净化此div标签上面的所有标签的float带来的负面效果

~~~html

<div class="clear"></div>
~~~

**解决方法2：**

配合伪元素选择器,content与display相当于空的div

~~~html
.clear::before,.clear::after{
	content: "";
	display: block;
	clear: both;
}
~~~

设置在浮动元素div的class上面即可清除上面和下面的浮动效果：

~~~html
<div class=clear>
    浮动div集合
</div>
~~~

## 4常用css成品与属性

### 4.1一个蓝色的按钮(a标签)：

~~~css
.button{
        /* 把a标签并且转换为块元素 */
            display: block;
            width: 50px;
            height: 20px;
            /* 背景颜色 */
            background-color: blue;
            /* 字体颜色 */
            color: white;
            /* 去除a标签的下划线 */
            text-decoration: none;
            /* 设置行高等于height,实现垂直居中 */
            line-height: 20px;
            /* 实现水平居中 */
            text-align: center;
       }
~~~

### 4.2一个返回顶部的按钮(button,a)

~~~html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    <style>
        body{
            height: 2000px;
        }
       .pos{
        position: fixed;
        bottom: 50px;
        right: 50px;
       }
       .pos a{
        text-decoration: none;
       }
    </style>
</head>
<body>
    <h1>111111</h1>
    <h1>111111</h1>
    <h1>111111</h1>
    <h1>111111</h1>
    <h1>111111</h1>
    <button class="pos">
        <a href="#">返回顶部</a>
    </button>
</body>
</html>
~~~

### 4.3 全选菜单



## 5 作业：

### 5.1 简单的选择文章页面

![https://image.itbaima.net/images/173/image-20230919098982381.png](https://image.itbaima.net/images/173/image-20230919098982381.png)

代码：

~~~html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    <link rel="stylesheet" href="style/index.css">
</head>
<body>
    <div class="title">
        <h2>
            文章列表
            <a href="">查看更多></a>
        </h2>
    </div>
    <div class="list">
        <ul>
            <li class="clear">
                <div class="img">
                    <img src="tou.jpg" alt="">
                </div>
                <div class="text">
                    <h3>kami带你学前端</h3>
                    <p>2003年2月8日</p>
                    <a href="">阅读</a>
                </div>
            </li>
            <li class="clear">
                <div class="img">
                    <img src="tou.jpg" alt="">
                </div>
                <div class="text">
                    <h3>kami带你学前端</h3>
                    <p>2003年2月8日</p>
                    <a href="">阅读</a>
                </div>
            </li>
        </ul>
    </div>
</body>
</html>
~~~

~~~css
*{
    margin: 0;
    padding: 0;
}
.title{
    width: 800px;
    height: 30px;
    border: 1px solid #aaa;
    box-sizing:border-box;
}
.title h2{
    font-size: 16px;
    line-height: 30px;
}
.title h2 a{
    font-size: 12px;
    color: #aaa;
    text-decoration: none;
    float: right;
    line-height: 17px; 
    text-align: center;
}
.list img{
    width: 200px;
    height: auto;
    float: left;
    margin: 10px;
}
.list .text{
    width: 200px;
    height: 100px;
    float: left;
    margin-top: 10px;
    padding: 20px 0 0 0 ;
}
.list .text p,a,h3{
    margin: 10px;
}
 .list .text a{
    /* 把a标签并且转换为块元素 */
    display: block;
    width: 50px;
    height: 20px;
    /* 背景颜色 */
    background-color: blue;
    /* 字体颜色 */
    color: white;
    /* 去除a标签的下划线 */
    text-decoration: none;
    /* 设置行高等于height,实现垂直居中 */
    line-height: 20px;
    /* 实现水平居中 */
    text-align: center;
} 
.clear::before,.clear::after{
    content: "";
    display: block;
    clear: both;
}
.list li{
    width: 800px;
    border: 3px solid #aaa;
    box-sizing:border-box;
}
~~~

### 5.2 一个下拉菜单的效果

~~~html
<!DOCTYPE html>
<html lang="en">
    <!-- 一个下拉菜单的效果 -->
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    <style>
        *{
            margin: 0;
            padding: 0;
        }
        .menu>li{
            float: left;
            list-style: none;
            width: 50px;
            height: 20px;
            border: 1px solid red;
            background-color: yellow;
            line-height: 20px;
            text-align: center;
        }
        .sub-menu>li{
            list-style: none;
            /* 设置高度为0，隐藏起来 */
            height: 0px;
            overflow: hidden;
            background-color: blue;
            color: white;
            transition: height 0.5s;
            line-height: 30px;
            text-align: center;
        }
        /* 移动到menu类的子级li时,操作的是menu子级下的sub-menu类下面的子级li */
        .menu>li:hover > .sub-menu>li{
            width: 50px;
            height: 30px;
            
        }
    </style>
</head>
<body>
    <ul class="menu">
        <li>
            数字
            <ul class="sub-menu">
                <li>1</li>
                <li>2</li>
                <li>3</li>
            </ul>
        </li>
        <li>
            字母
            <ul class="sub-menu">
                <li>a</li>
                <li>b</li>
                <li>c</li>
            </ul>
        </li>
        <li>
            符号
            <ul class="sub-menu">
                <li>#</li>
                <li>%</li>
                <li>*</li>
            </ul>
        </li>
    </ul>
</body>
</html>
~~~

### 5.3一个滚动菜单

~~~html
<!DOCTYPE html>
<html lang="en">
    <!-- 一个滚动菜单 -->
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    <style>
        *{
            margin: 0;
            padding: 0;
        }
        .menu>li{
            list-style: none;
            width: 80px;
            height: 30px;
            border: 1px solid black;
            float: left;
            overflow: hidden;
        }
        .show{
            width: 80px;
            height: 30px;
            line-height: 30px;
            text-align: center;
            background-color: red;
            transition: margin 0.1s;
        }
        .click{
            width: 80px;
            height: 30px;
            line-height: 30px;
            text-align: center;
            background-color: yellow;
        }
        /* 移动到菜单列表上面时 操作的是显示(.show)的p标签（向上移动30px），
        没有显示(.click)的p标签也被带着移动了30px ,两个p连在一起了？*/
        .menu>li:hover > .show{
            margin-top: -30px;
        }

    </style>
</head>
<body>
    <ul class="menu">
        <li>
            <p class="show">首页</p>
            <p class="click">c</p>
        </li>
        <li>
            <p class="show">动漫</p>
            <p class="click">n</p>
        </li>
        <li>
            <p class="show">电影</p>
            <p class="click">m</p>
        </li>
    </ul>
</body>
</html>
~~~

### 5.4 一个旋转覆盖原来的菜单

~~~html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>一个旋转覆盖原来的菜单</title>
    <style>
        *{
            margin: 0;
            padding: 0;
        }
        .menu{
            position: fixed;
            bottom: 100px;
            right: 100px;
        }
        .menu>li{
            width: 50px;
            height: 50px;
            border: 1px solid red;
            list-style: none;
            /* 设置为相对布局，子标签的绝对布局以此标签为参照物 */
            position: relative;
            overflow: hidden;
        }
        .show{
            width: 50px;
            height: 50px;
            line-height: 50px;
            text-align: center;
        }
        .click{
            width: 50px;
            height: 50px;
            line-height: 50px;
            text-align: center;
            background-color: yellow;
            /* 设置为绝对布局，移动位置到参照物的上0px,左0px处，即重和了 */
            position: absolute;
            top: 0;
            left: 0;
            transform: rotate(90deg);
            /* 设置圆心，坐标系原点为左上角 */
            transform-origin: 0px 50px;
            transition: transform 0.5s;
            
        }
        /* 移动进入每一个li时，操作的是li里面的click类，而不是全部的click类 */
        .menu>li:hover > .click{
            transform: rotate(0deg);
        }

    </style>
</head>
<body>
    <ul class="menu">
        <li>
            <p class="show">首页</p>
            <p class="click">首页</p>
        </li>
        <li>
            <p class="show">动漫</p>
            <p class="click">动漫</p>
        </li>
        <li>
            <p class="show">电影</p>
            <p class="click">电影</p>
        </li>
    </ul>
</body>
</html>
~~~







## 6 css定位 使用定位+设置top,left等属性快速设置元素的位置

~~~html
    <div class="box">1</div>
    <div class="box pos">2</div>
    <div class="box">3</div>
~~~

### 6.1 绝对定位 absolute

~~~css
        .pos{
           /* 设置为绝对定位，不占位置，浮动起来,会覆盖下面的内容(脱离文档流)，默认坐标系为浏览器的左上角 */
            position: absolute;
            /* 设置距离坐标系原点的位置，没有设置position属性会无效 */
            top: 50px;
            left: 50px;
        }
~~~

### 6.2 相对定位 relative

~~~css
.pos{
            /* 设置为相对定位，坐标系原点为没有设置之前的位置，原来的位置仍然被占用.会覆盖下面的内容(不脱离文档流) */
            position: relative;
            top: 50px;
            left: 50px;
        }
~~~

**设置子级以父级的左上角作为坐标系的原点：(相对+绝对)**

一般情况下：

父级 position: relative;

子级 position: absolute;

### 6.3 固定定位 fixed

~~~css
.pos{
            /* 设置为固定定位,坐标系原点为左上角，即使上下移动页面，位置也是固定的 
    			不占位置，浮动起来,会覆盖下面的内容(脱离文档流)
    */
            position: fixed;
            /* 设置距离坐标系原点的位置，没有设置position属性会无效 */
            top: 50px;
            left: 50px;
        }
~~~

### 6.4 谁显示在前面? z-index

~~~css
.pos{
            position: fixed;
            top: 50px;
            left: 50px;
    		 /* 默认为0，大的显示在前面 */
            z-index: 10;
        }
~~~

## 7 css3新特性

### 7.1 边框圆角 border-radius：

~~~css
.box{
            width: 100px;
            height: 100px;
            border: 1px solid red;
            /* 设置圆的半径大小，这个圆的四分之一的圆弧会代替边框的4个角落 
    			设置为50%即是正圆
    */
            border-radius: 10px;
    		/* 分别设置左上 右上 右下 左下的圆的半径 */
            border-radius: 10px 20px 30px 40px;
        }
~~~

### 7.2 阴影效果 box-shadow:

```css
.box{
            width: 100px;
            height: 100px;
            background-color: red;
            /* 设置阴影效果，值分别为(从第4象限开始计算)x轴偏移量，y轴偏移量，模糊度，阴影的颜色(默认为灰色) */
            box-shadow: 10px 20px 30px blue;
        }
```

### 7.3 转换 transform:

#### 7.3.1 旋转 rotate：

~~~css
.box{
            width: 100px;
            height: 100px;
            background-color: red;
            /* 设置旋转45度 */
            transform: rotate(45deg);
        }
~~~

#### 7.3.2 设置旋转的原点 transform-origin

~~~css
            .box{
            width: 100px;
            height: 100px;
            margin: 100px auto;
            background-color: blue;
            transition: transform 1s ease;
            /* 设置旋转的原点为图形左上角而不是图形的中心 */
            transform-origin: 0 0;
        }
        .box:hover{
            transform: rotate(360deg);
        }
~~~



#### 7.3.3 按照倍数缩放 scale：

~~~css
.box{
            width: 100px;
            height: 100px;
            background-color: red;
            /* 设置大小变为乘0.5倍 */
            transform: scale(0.5);
        }
~~~

#### 7.3.4 位移 translate：

~~~css
.box{
            width: 100px;
            height: 100px;
            background-color: red;
            /* (第4象限)x轴向右偏移50px，y轴向下偏移100px */
            transform: translate(50px,100px);
        }
~~~



#### 7.3.5 应用：让一个元素在网页水平垂直居中(使其在图片上面显示)

~~~css
.box{
            /* 让一个元素在网页水平垂直居中 */
            width: 100px;
            height: 100px;
            background-color: red;
            position: absolute;
            top: 50%;
            left: 50%;
            /* (从第4象限开始计算)x轴左偏移50%，y轴上偏移50%,由width和height决定具体的偏移大小*/
            transform: translate(-50%,-50%);
        }
~~~

### 7.4 过度效果

transition：
#### 7.4.1 transition-property：过渡属性

设置对哪一个属性添加过度效果

（例如transform);

#### 7.4.2 transition-duration: 过渡持续时间

（例如1s)）;

~~~css
.box{
            width: 100px;
            height: 100px;
            margin: 100px auto;
            background-color: blue;
            /* 指定对transform属性进行过渡效果*/
            transition-property: transform;
    		/* 指定过渡完成的时间为1秒 */
            transition-duration: 1s;

        }
        .box:hover{
            /* 旋转45度 */
            transform: rotate(45deg);
        }
~~~



#### 7.4.3 transiton-timing-function : 过渡函数;

 指定过渡过程中的速度

- ease:开始和结束慢，中间快。默认值

- linear:匀速。

- ease-in:开始慢。

- ease-out:结束慢。

- ease-in-out:和ease类似，但比ease幅度大。

  ~~~css
  .box{
              width: 100px;
              height: 100px;
              margin: 100px auto;
              background-color: blue;
      		//简写
              transition: transform 1s ease;
          }
          .box:hover{
              transform: rotate(360deg);
          }
  ~~~
  
  

#### 7.4.4 transition-delay: 过渡延迟时间;

（例如1s)）;

简写：transition: 属性 持续时间 函数 延迟时间;

处理溢出元素边框的部分 overflow:

~~~css
.box{
    /* 溢出的部分隐藏起来 */
    overflow: hidden;
    /* 溢出的部分自动使用滚轮进行处理 */
    overflow: auto;
}
~~~

#### 7.5 动画效果 @keyframes animation

动画属性(animation):

1. animation-name: 规定需要绑定到选择器的keyframes名称。。
2. animation-duration: 规定完成动画所花费的时间，以秒或毫秒计。
3. animation-timing-function: 规定动画的速度曲线。
4. animation-delay: 规定在动画开始之前的延迟。
5. animation-iteration-count: 规定动画应该播放的次数。

简写：

~~~css
.box{
    		/* 添加名称为rotate的动画效果 2秒结束 匀速执行 延迟1s执行 无限循环
    			类似java的构造器？中间可以漏直接写后面的参数*/
            animation: rotate 2s linear 1s infinite;
}
~~~

动画会从0%执行到100%，最后回到0%时的状态，所以开始与结束时的状态相同会使动画更加平滑。

例如：

~~~css
/* 定义一个动画效果，命名为rotate */
        @keyframes rotate{
            0%{
                transform: rotate(0deg);
            }
            100%{
                transform: rotate(360deg);
            }
            /* 从0到100%，另一种定义方式 */
            form{
                transform: rotate(0deg);
            }to{
                transform: rotate(360deg);
            }
        }
        .box{
            width: 100px;
            height: 100px;
            background-color: red;
            margin: 100px auto;
            /* 添加rotate的动画效果，2秒结束 */
            animation: rotate 2s;
        }
~~~

### 8 flex布局

#### 8.1 介绍

基本上可以**代替float布局**

**main axis:主轴(类似x轴 从左到右)**
**cross axis:交叉轴(类似y轴 从上到下)**

将元素设置为display:flex;元素会变为一个flex容器。
容器内部的元素为flex元素或者叫flex项目(flex-item)。

~~~html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    <style>
        .container{
             /*
            设置元素为flex容器,代替float布局,容器里面的项目默认会按照主轴进行排列,
            没有设置项目的高度时，默认的高度为容器的高度而不是元素内容的高度
            */
            display: flex;
            width: 800px;
            height: 400px;
            background-color: yellow;
        }
        .item{
            width: 100px;
            background-color: red;
            border: 1px solid blue;
        }
    </style>
</head>
<body>
    <div class="container">
        <div class="item">111</div>
        <div class="item">222</div>
        <div class="item">333</div>
    </div>
</body>
</html>
~~~



设置flex容器
#### 8.2 flex-direction:设置主轴的方向

flex-direction

1. row(默认值)：主轴为水平方向，起点在左端。
2. row-reverse:主轴为水平方向，起点在右端。
3. column:主轴为垂直方向，起点在上沿。
4. column-reverse:主轴为垂直方向，起点在下沿。

~~~css
.container{
            display: flex;
            width: 800px;
            height: 400px;
            background-color: yellow;
    		/* 设置主轴为垂直方向，起点在上沿。 */
            flex-direction: column;
        }
~~~



#### 8.3 justify-content: 设置容器中flex项目在主轴的显示方式(center 水平居中)

justify-content

1. flex-start(默认值)：左对齐

2. flex-end:右对齐

3. center:居中

4. space-between:最左与最右两端各占一个项目，每个项目之间的间隔都相等。

5. space-around: 每个项目自动设置一个左右的外边距（每个项目两侧的间隔相等。所以，项目之间的间隔比项目与边框的间隔大一倍。）

   

~~~css
.container{
            display: flex;
            width: 800px;
            height: 400px;
            background-color: yellow;
            /* 设置项目居中显示 */
            justify-content: center;
        }
        .item{

            width: 100px;
            background-color: red;
            border: 1px solid blue;
        }
~~~





#### 8.4 align-items: 设置容器中flex项目在交叉轴的排列方式 (center 垂直居中） 

align-item:

1. flex-start:交叉轴的起点对齐。设置项目的高度为用户设置的高度，不再自适应容器的高度，并且设置项目的位置为容器**顶部**
2. flex-end:交叉轴的终点对齐。设置项目的高度为用户设置的高度，不再自适应容器的高度，并且设置项目的位置为容器**底部**
3. center:交叉轴的中点对齐。设置项目的高度为用户设置的高度，不再自适应容器的高度，并且设置项目的位置为容器**中间**
4. stretch(延伸)（默认值)：如果项目未设置高度或设为auto,将占满整个容器的高度。

~~~css
 .container{
            display: flex;
            width: 800px;
            height: 400px;
            background-color: yellow;
            /* 设置项目居中显示 */
            justify-content: center;
            /* 设置高度不再进行自适应并且项目的位置设置在容器顶部 */
            align-items: flex-start;
        }
        .item{

            width: 100px;
            background-color: red;
            border: 1px solid blue;
        }
~~~



#### 8.5 设置单个flex项目的大小、对齐方式：fex(grow、shrink、basis)、align-self
哪怕flex项目的大小比容器大，**flex项目也不会超出flex容器的宽度与高度**，而是会按照每个flex项目的比例来分配容器的空间。

1. fex-grow: 定义了项目的增大比例，默认为0，设置此属性后，项目的宽度之和会自适应容器的宽度，此属性指定每个项目占的比例

2. flex-shrink: 定义了项目的缩小比例，默认为1，即如果空间不足，该项目将缩小，值越大，缩小的越多
3. flex-basis:  设置px,默认值是auto。表示设置项目的宽度
4. 缩写：flex:  g s b
5. align-self: flex项目的对齐方式(auto|flex-start|flex-end|center|baseline|stretch)

~~~css
        .container{
            display: flex;
            width: 800px;
            height: 400px;
            background-color: yellow;
        }
        .item{
            background-color: red;
            border: 1px solid blue;
            /* 相当于 flex-grow: 1;  */
            flex: 1;
        }
		.big{
            height: 100px;
            /* 设置单个项目的对齐方式为居中 */
            align-self: center;
        }
~~~



#### 8.6 应用：使用flex布局并使一个元素水平垂直居中

~~~css
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    <style>
        *{
            margin: 0;
            padding: 0;
        }
		/*想要设置一个标签占据浏览器的100%(height:100%)，它的所有父级必须也是height:100%才能够生效。*/
        body,html{
            height: 100%;
        }
        .container{
            height: 100%;
            background-color: yellow;
            display: flex;
            /* 设置容器内的item水平对齐 */
            justify-content: center;
            /* 设置容器内的item垂直对齐 */
            align-items: center;
        }
        .box{
            background-color: red;
            width: 100px;
            height: 100px;
        }
    </style>
</head>
<body>
    <div class="container">
        <div class="box"></div>
    </div>
</body>
</html>
~~~

#### 8.7 作业：一个手机端的底部菜单页面

~~~html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>一个手机端的底部菜单页面</title>
    <style>
       *{
        margin: 0;
        padding: 0;
       }
       .menu{
        background-color: yellow;
        position: fixed;
        bottom: 0px;
        display: flex;
        /* 使用百分数自动填满水平方向 */
        width: 100%;
        height: 70px;
        /* 设置垂直居中 */
        align-items: center;
       }
       .sub{
        /* 设置图片与文字水平居中，并且大小等比例进行分配
           最终效果类似于 justify-content: space-around;直接设置水平居中*/
        text-align: center;
        flex: 1;
       }
       .sub img{
        height: 30px;
       }
    </style>
</head>
<body>
    <div class="menu">
        <div class="sub">
            <img src="images/home.png" alt="图片加载失败">
            <P>首页</P>
        </div>
        <div class="sub">
            <img src="images/home.png" alt="图片加载失败">
            <P>电影</P>
        </div>
        <div class="sub">
            <img src="images/home.png" alt="图片加载失败">
            <P>动漫</P>
        </div>
        <div class="sub">
            <img src="images/home.png" alt="图片加载失败">
            <P>漫画</P>
        </div>
        <div class="sub">
            <img src="images/home.png" alt="图片加载失败">
            <P>小说</P>
        </div>
    </div>
</body>
</html>
~~~



### 9 grid布局

脱离文档流，浮在上面



#### 9.1 设置表现为grid容器，设置单元格的数量与长度

~~~css
    .container{
            display:grid;
            /* 设置为3行，1-3个单元格的宽度分别为100px 100px 100px*/
            grid-template-columns:100px 100px 100px;
            /* 设置为3列，1-3个单元格的高度分别为100px 100px 100px*/
            grid-template-rows:100px 100px 100px;
			}
    
    .container{
            display:grid;
            /* 设置为3行，第一个单元格的宽度为100px，剩下两个单元格会占满容器的宽度,按照设置的比例进行分配*/
            grid-template-columns:100px 1fr 2fr;
            grid-template-rows:100px 100px 100px;
			}
~~~



~~~html
<body>
    <div class="container">
        //下面的每一个item存放在一个单元格中
        <div class="item">111</div>
        <div class="item">222</div>
        <div class="item">333</div>
        <div class="item">444</div>
    </div>
</body>
~~~

#### 例子：

~~~html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>图4-15</title>
    <style>
        *{
            margin: 0;
            padding: 0;
        }

        .background{
            width: 100%;
            height: 600px;
        }

        .container{
            position: absolute;
            top: 0;
            right: 0;
            display:grid;
            /* 设置为2行，1-2个单元格的宽度分别为300px 300px*/
            grid-template-columns:300px 300px;
            /* 设置为2列，1-2个单元格的高度分别为300px 300px*/
            grid-template-rows:300px 300px;
        }
        .item{
            padding: 30px 30px;
        }
        .item img{
            width: 250px;
            height: 155px;
            margin: 20px 0;
        }
        .item a{
            margin: 30px 70px;
            color: yellow;
        }
        .text{
            text-align: center;
        }
        .big-container{
            border: 3px solid rgb(59, 58, 58);
        }
    </style>
</head>
<body>
    <div class="big-container">
        <img src="images/5.png" alt="" class="background">
        <div class="container">
            <div class="item">
                <a href="http://www.baidu.com">点击查看详情</a>
                <img src="images/1.png" alt="">
            </div>
            <div class="item">
                <a href="http://www.baidu.com">点击查看详情</a>
                <img src="images/2.png" alt="">
            </div>
            <div class="item">
                <a href="http://www.baidu.com">点击查看详情</a>
                <img src="images/3.png" alt="">
            </div>
            <div class="item">
                <a href="http://www.baidu.com">点击查看详情</a>
                <img src="images/4.jpg" alt="">
            </div>
        </div>
        <div class="text">粤ICP备2023040577号-2</div>
        <div class="text">以上图片来源于网络,如有侵权</div>
        <div class="text">
            请联系我们删除:
            <a href="http://www.baidu.com">123@163.com</a>
        </div>
    </div>
    
</body>
</html>
~~~



容器属性



#### 9. 项目属性

grid-column-start属性
grid-column--end属性

~~~css
        .first{
            /* 设置开始线为第1条线，结束线为第3条线，来扩大单元格 */
            grid-column-start: 1;
            grid-column-end: 3;
        }
~~~



grid-column属性
grid-row-start属性
grid-row-end属性
grid-row属性
justify-self属性，
align-self属性，

#### 9.2 justify-items属性   (center 设置item水平居中)

设置item在单元格中的**水平对齐**方式，并且item的宽度与高度不会自动填满单元格

1. start 左对齐
2. center 水平居中对齐
3. end 右对齐
4. stretch(默认) 水平方向会自动占满单元格(一开始设置的高宽度）

~~~css
        .container{
            justify-items: start;
        }
~~~



#### 9.3 align-items属性 ( center 设置item垂直居中)

设置item在单元格中的**垂直对齐**方式，并且item的宽度与高度不会自动填满单元格

1. start 上对齐
2. center 垂直居中对齐
3. end 下对齐
4. stretch(默认) 垂直方向会自动占满单元格(一开始设置的高度)

#### 9.4 justify-content属性 (center 单元格整体水平居中)

1. start 左对齐
2. center 水平居中对齐
3. end 右对齐
4. stretch(默认) 



#### 9.5 align-content属性  (center 单元格整体垂直居中)
1. start 上对齐
2. center 垂直居中对齐
3. end 下对齐
4. stretch(默认) 

#### 9.6 grid-auto-columns 属性 设置溢出列的宽度

**如果有溢出列，则设置其宽度为50px，否则默认会占用容器剩下的所有宽度，高度默认为单元格的高度**

~~~css
        .container{
            grid-auto-columns: 50px;
}
~~~



#### 9.7 grid-auto-rows 属性  设置溢出行的高度

**如果有溢出行，则设置其高度为50px，否则默认会占用容器剩下的所有高度，宽度默认为单元格的宽度**

~~~css
        .container{
            grid-auto-rows: 50px;
}
~~~



#### 9.8 grid-auto-flow 设置元素的排列方式(默认为row 表示横向排列)

~~~css
    .container{
            /* 设置元素纵向排列 */
            grid-auto-flow: column;
}
~~~





### 9 其他css注意事项

#### white-space



~~~css
white-space:nowrap;
~~~

**`white-space` 属性**：该属性控制元素内的空白处理方式，包括如何处理空格和换行符。它有多个可能的值，如 `normal`、`pre`、`nowrap` 等。

**`nowrap`**：这个值指定文本不会换行，所有内容都显示在同一行上。如果内容的宽度超出了父容器的宽度，内容就会溢出容器，除非使用了其他CSS属性来处理溢出的部分。

假设有以下HTML结构：

```html
<div style="width: 100px; white-space: nowrap; border: 1px solid black;">
    这是一个很长的文本示例。
</div>
```

- 如果 `white-space` 设为 `normal`（默认值），文本会在到达容器宽度（100px）时自动换行，因此这个文本会分成多行显示。
- 但是如果 `white-space: nowrap;`，这个文本就会保持在同一行，不会换行。如果文本长度超过了容器宽度，文本会超出容器边界，可能看不全，除非使用了 `overflow` 属性来处理溢出的内容（比如 `overflow: hidden;` 或 `overflow: scroll;`）。



## 项目案例

### 新闻项目效果图如下

具体：E:\Afrontend files vsCode\frontend_item\uniapp-ling_project   中的ReadMe

![image-20240828154104096](E:\alwaysUse\notes\myNotes\frontend note\H,C,J\Css.assets\image-20240828154104096.png)

### 让内容显示在同一行或同一列

#### 1）flex

~~~vue
<template>	
	<view class="newsbox">
		<view class="pic">
			<image :src="item.picurl" mode="aspectFill"></image>
		</view>
		<view class="text">
			<view class="title">
				<view class="con">
					{{item.title}}
				</view>				
			</view>
			<view class="info" v-if="!item.looktime">
				<view class="font">{{item.author}}</view>
				<view class="font">{{item.hits}}浏览</view>
			</view>
			<view class="info" v-else>
				<view class="font">浏览时间：{{item.looktime}}</view>
			</view>
		</view>
		
	</view>	
</template>
~~~



![image-20240828145329459](E:\alwaysUse\notes\myNotes\frontend note\H,C,J\Css.assets\image-20240828145329459.png)

参考：https://www.bilibili.com/video/BV1mT411K7nW?p=52&spm_id_from=pageDriver&vd_source=d6367c1fc21883823f1fb738f86ef26e    p52 9:40

同一列：

![image-20240828151823475](E:\alwaysUse\notes\myNotes\frontend note\H,C,J\Css.assets\image-20240828151823475.png)

效果：



#### 2）inline-block

在此文件搜索：inline-block  转换为行内块元素: 可以把内容显示在同一列上



#### 3）oneRow 与 oneColumn

> 设置元素展示为一行

1

~~~css
	.oneRow {
	    display: flex;               /* 启用 flex 布局 */
	    justify-content: flex-start; /* 子元素在水平方向从左到右排列 */
	    align-items: center;         /* 子元素垂直居中对齐 */
	}

~~~

在这个基础上，如何保证元素都在同一行，不会有元素被挤下来，变成多行?

回答：

这是最直接的方法。添加 `flex-wrap: nowrap;` 可以确保所有子元素在水平方向上都保持在同一行，即使总宽度超过容器宽度，元素也不会换行。

~~~css
.oneRow { 
    display: flex; 
    justify-content: flex-start; 
    align-items: center; 
    flex-wrap: nowrap;  /* 防止元素换行 */
}
~~~



> 设置元素展示为一列



~~~css
	/* 显示成一列 */
	.oneColumn{
		display: flex; /* 启用 flex 布局 */
		flex-direction: column; /* 设置垂直排列 */
		align-items: flex-start; /* 左对齐 */
	}
~~~





### css两行后显示省略号

![image-20240828152457667](E:\alwaysUse\notes\myNotes\frontend note\H,C,J\Css.assets\image-20240828152457667.png)

老师用：

~~~css
/* 溢出2行后进行省略 */
.overflowOmit{
	overflow: hidden;
	text-overflow: -o-ellipsis-lastline;
    overflow:hidden;
    text-overflow: ellipsis;
	display: -webkit-box;
	-webkit-line-clamp: 2;
	line-clamp: 2;
	-webkit-box-orient: vertical;
}
~~~



1 csdn  这个可以使用： 参考：https://blog.csdn.net/Tracy_frog/article/details/77881808

~~~css
/* 溢出一行后使用省略号进行省略*/
.overflowOmit{
	overflow: hidden;
	text-overflow: ellipsis;
	display: -webkit-box;
	-webkit-line-clamp: 1;
	overflow:hidden;
	/*! autoprefixer: off */
	-webkit-box-orient: vertical;
}
~~~

2 https://www.cnblogs.com/tcyweb/p/16739241.html

~~~css
/* 溢出一行后进行省略，显示不出省略号 */
.overflowOmit{
	white-space: nowrap;
	text-overflow: ellipsis;
	overflow: hidden;
	word-break: break-all;
}
~~~



注意：如果不能正常使用：则新开一个标签，把上面的样式写在靠近文字内容的标签上面

![image-20240828152329723](E:\alwaysUse\notes\myNotes\frontend note\H,C,J\Css.assets\image-20240828152329723.png)



#### max-width解决图片显示大小问题

解释js代码：`res.data.content=res.data.content.replace(/<img/gi,'<img style="max-width:100%"')`



这段JavaScript代码的作用是修改 `res.data.content` 中的所有 `<img>` 标签，以便为它们添加一个内联样式，使图像的最大宽度不超过容器的宽度。

代码解析：

```javascript
res.data.content = res.data.content.replace(/<img/gi, '<img style="max-width:100%"')
```

1. **`res.data.content`**：表示从某个API或数据源获取的数据中的 `content` 字段，通常是HTML格式的内容。

2. **`replace(/<img/gi, '<img style="max-width:100%"')`**：
   - **`/<img/gi`**：这是一个正则表达式，用来匹配所有的 `<img>` 标签。
     - **`<img`**：匹配 `<img>` 标签的起始部分。
     - **`g`**：表示全局匹配，即替换所有匹配项，而不仅是第一个。
     - **`i`**：表示不区分大小写，即匹配 `<IMG>`、`<Img>` 等不同写法。

   - **`'<img style="max-width:100%"'`**：这是替换的字符串，它会将 `<img>` 标签替换为 `<img style="max-width:100%">`，从而在 `<img>` 标签中添加或覆盖 `style="max-width:100%"`，使图像的最大宽度限制为其父容器的宽度。

应用场景：

这种替换通常用于使图片在网页中自适应布局，避免图片宽度超过容器，造成布局问题，尤其在响应式设计中。



## 设置两个元素在页面最下面的中间处紧挨着显示



~~~vue
如何设置这两个元素在页面最下面的中间处紧挨着显示：
<text class="pad_LP2">新增报销</text>
<view v-if="isShowReimbursementCategory" class="reimbursementCategory">
    <button size="mini" type="primary" @click="toSubmitAccount(1)">差旅报销</button>
    <button size="mini" type="primary" @click="toSubmitAccount(2)">费用报销</button>
    <button size="mini" type="primary" @click="toSubmitAccount(3)">物料报销</button>
</view>
~~~





要将这两个元素放置在页面底部，并紧挨着显示，可以采用以下步骤来实现布局：

### 1. **使用 `position: fixed` 定位到页面底部**

首先，可以为容器设置 `position: fixed`，将其定位到页面的底部中间位置。通过使用 `display: flex` 配合 `justify-content` 和 `align-items` 来实现水平和垂直方向上的布局。

### 2. **HTML 结构示例**

在这个例子中，我假设你需要的容器元素是 `<div>` 或 `<view>`，你可以根据实际情况调整。修改后的 HTML 结构如下：

```html
<div class="bottom-bar">
    <text class="pad_LP2">新增报销</text>
    <view v-if="isShowReimbursementCategory" class="reimbursementCategory">
        <button size="mini" type="primary" @click="toSubmitAccount(1)">差旅报销</button>
        <button size="mini" type="primary" @click="toSubmitAccount(2)">费用报销</button>
        <button size="mini" type="primary" @click="toSubmitAccount(3)">物料报销</button>
    </view>
</div>
```

### 3. **CSS 样式设置**

通过以下样式将这两个元素固定在页面底部并紧挨显示：

```css
.bottom-bar {
    position: fixed;              /* 固定在页面某个位置 */
    bottom: 0;                    /* 定位到页面底部 */
    left: 50%;                    /* 定位到页面水平中间 */
    transform: translateX(-50%);  /* 水平居中 */
    display: flex;                /* 启用 flex 布局 */
    align-items: center;          /* 子元素垂直居中对齐 */
    justify-content: center;      /* 子元素水平居中对齐 */
    background-color: white;      /* 背景色可以根据需要调整 */
    padding: 10px;                /* 内边距，防止元素贴边 */
}

.pad_LP2 {
    margin-right: 10px;           /* 设置第一个元素与第二个元素之间的间距 */
}

.reimbursementCategory button {
    margin: 0 5px;                /* 调整按钮之间的间隔 */
}
```

### 4. **解释说明**
- `position: fixed; bottom: 0; left: 50%; transform: translateX(-50%);`：将 `.bottom-bar` 容器固定在页面底部，并水平居中显示。
- `display: flex; justify-content: center; align-items: center;`：使用 `flex` 布局，使子元素在容器内水平和垂直居中对齐。
- `margin-right: 10px;`：为 `text` 元素设置右边距，使其与下一个 `view` 元素保持紧凑的间距。

这样就可以确保 `<text>` 和 `<view>` 元素在页面底部中间紧挨着显示，并且不同按钮也有适当的间距。



## 设置子元素在超过容器宽度时自动换行

要使 `search-container` 中的子元素在超过容器宽度时自动换行，可以将 `display: flex` 的 `flex-wrap` 属性设置为 `wrap`。这样，当子元素超过容器宽度时，就会自动换行显示。

修改后的样式：

可以在 `search-container` 的样式中加入 `flex-wrap: wrap;`，如下所示：

```css
.search-container {
  display: flex;
  flex-wrap: wrap; /* 允许子元素超出时换行 */
  justify-content: start;
  align-items: start;
  padding: 10px;
  background-color: #f8f8f8;
  border-radius: 8px;
}
```

### 解释

- **`flex-wrap: wrap;`**：表示当子元素在当前行中排列不下时，会自动换到下一行继续排列。



## 设置文本不换行，居中显示

~~~css
	.uploadPictureButtonMP{
		width: 190rpx !important;
		height: 80rpx;
		color: white;
		white-space: nowrap;            /* 防止文字换行 */
		background-color: rgb(102, 177, 255);
        /* 文字水平居中 */
		text-align: center;
        /* 设置值等于height，实现文字垂直居中 */
		line-height: 80rpx;
		border-radius: 10%;
	}
~~~

