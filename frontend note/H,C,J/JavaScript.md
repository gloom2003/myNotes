# JavaScript的使用

## 1 基础知识

**js可以实现用户与浏览器的交互、浏览器与服务器的交互**

ES5（旧版本）,ES2015++(新版本)

### 1.1 引入js的方式

~~~html
<body>
    <script>
        // 显示一个警告弹窗
        alert("hello world");
        //向浏览器的控制台打印一份日志信息
        console.log("hi");
    </script>
</body>
~~~



var:声明变量(ES2015中使用let声明变量)

### 1.2 六种数据类型
#### 1. 数值(Number):100,1,2,3
#### 2. 字符串(String):“hello world”"你好世界'
#### 3. 布尔(Boolean):true,false
#### 4. 未定义(Undefined):undefined

~~~html
<script>
    //没有赋值的变量默认为undefined
	var v;
    console.log(v);
</script>
~~~



#### 5.空(Nul):null  (空对象)

#### 6.对象(Object):{} 不是代码块！

即：属性的无序集合

```html
<script>
    var cat{
	name:"mm",
	age:2
}
    //两种获取属性的方式
    var str1 = cat.name;
    var str2 = cat["name"];
</script>
```



### 1.3 比较运算符 ==

1. ==
2. ===
3. !=
4. !===

~~~html
    <script>
        // 使用==(等于)判断不同的数据类型时，会自动转换为相同数据类型后(导致性能低)，只对值进行比较
        console.log(20 == "20");
        // ===(恒等于)既判断值是否相同也判断数据类型是否相同
        console.log(20 === "20");
        
        // 使用!=(不等于)判断不同的数据类型时，会自动转换为相同数据类型后，只对值进行比较
        console.log(20 != "20");
        // !==(非更等于)既判断值是否相同也判断数据类型是否相同
        console.log(20 !== "20");
    </script>
~~~

### 1.4 函数

普通函数

~~~html
<body>
    <script>
        // 定义函数，不用写返回值（反正都可以使用var来接收）与形参类型
        function cal(num1,num2,str){
            switch(str){
                case "+":
                    return num1+num2;
                case "-":
                    return num1-num2;
                case "*":
                    return num1*num2;
                case "/":
                    return num1/num2;
            }

        }
        console.log(cal(1,2,"*"));
    </script>
</body>
~~~

匿名函数

~~~html
    <script>
		var v = {
            //定义一个匿名函数(没有函数名)并赋值给cal,cal即是v对象的一个方法
            cal:function(num1,num2,str){
                switch(str){
                case "+":
                    return num1+num2;
                case "-":
                    return num1-num2;
                case "*":
                    return num1*num2;
                case "/":
                    return num1/num2;
            }
            }
        }
        console.log(v.cal(1,2,"/"));
    </script>
~~~

### 1.5 数组 Array

定义方式：

~~~html
<body>
    <script>
        //使用构造函数
       var $arr = new Array("1","2","3"); 
        //简写
        var arr = [1,2,3];
    </script>
</body>
~~~

#### 1.5.1遍历方式 for in,for of,map：

~~~html
<body>
    <script>
      var arr = [1,2,3];
        
      //for in 版本
      for(var i in arr){
          //i为索引
        console.log(arr[i]);
      }
        
      //for of 版本
      for(var i of arr){
          //i为数组的元素
        console.log(i);
      }
        
      //使用数组对象自带的map方法，传入参数为一个回调函数
    arr.map(function(value,index){
        console.log("第"+(index+1)+"个元素是"+value);
    })
    </script>
</body>
~~~

#### 1.5.2 数组的常用方法(map,push,sort,filter,join)
- map 遍历
- push 增

~~~html
<body>
    <script>
    var arr = [1,2,3,5,2,8,6];
    arr.push(10);
    console.log(arr);
    </script>
</body>
~~~

- sort 排序
- filter 过滤

~~~html
    <script>
    var arr = [1,2,3,5,2,8,6];
    var newArr = arr.filter(function(item){
        if(item>3){
            return item;
        }
    });
    console.log(newArr);
    </script>
~~~



- join 拼接为字符串

~~~html
    <script>
    var arr = ["a","b","c"];
    //把数组中的元素使用"+"连接起来,默认为","
    var str = arr.join("+");
    console.log(str);
    </script>
~~~

与join相反的方法：string的splite方法 分隔为数组

~~~html
    <script>
    var date = "2022-1-1";
    // 使用分隔符"-"把字符串分隔为数组,""则分隔每一个字符
    var list = date.split("-");
    console.log(list);
    </script>
~~~

### 1.6 常用的内置对象
#### 1.6.1 Array-数组 join,sort,map见上文。



#### 1.6.2 Math.abs(),random()...

- Math.floor():向下取整,（正数使用：去除数字的小数部分，负数使用：小数部分使用1来代替）
- Math.random():0-1的随机数
- Math.abs():绝对值
- Math.sqrt(9):开方
- Math.pow(2,4):乘方

#### 1.6.3 Date

Date对象

```
var d = new Date();
var d_target = new Date("2020-1-1);

d.getFullYear();
d.getMonth();//月份采用类似索引的方式，0表示1月
d.getDate();//获取今天是几号 
d.getDay();//获取今天星期几
d.getHours();
d.getMinutes();
d.getSeconds();
d.getTime(0);//时间戳是指格林威治时间1970年01月01日00时00分00秒起至现在的总毫秒数
```

##### 设置定时任务 setInterval

在控制台输出当前时间，每秒输出一次。

~~~html
<body>
    <script>
        setInterval(function(){
            var d = new Date();
            var hours = d.getHours();
            var minutes = d.getMinutes();
            var seconds = d.getSeconds();
            console.log(hours+":"+minutes+":"+seconds);
        },1000);
    </script>
</body>
~~~



#### 1.6.4 RegExp-正则表达式

在Js中创建正则表达式对象:

```html
<body>
    <script>
        var reg = new RegExp("123");
        //简写：
		var reg = /123/;
    </script>
</body>
```

例子：

~~~html
<body>
    <script>
        //[]:表示范围，占一个位置
        var str = "1";
        var reg = /[a-z]/;
        //测试正则表达式是否匹配成功：test()返回boolean，exec(str)返回匹配的内容
        var result = reg.test(str);
        console.log(result);
    </script>
</body>
~~~

##### 语法:

- ^:开头
- $:结尾
- []：范围 例如：[a-z]:匹配a-z的字母 , [a-z0-9]表示匹配小写字母或者数字
- {}:匹配的数量 例如：{2}：只能匹配两位  {5,8}:匹配5-8位
- ():分组  配合exec(str)方法使用，exec(str)方法可以把使用()分组的内容存放到返回的数组中，从索引1开始
- +:匹配1位或多位，相当于 {1,}
- ?:0位或1位，同{0,1}
- .:匹配除了换行之外的所有字符(匹配一个)
- *:匹配0个或多个字符
- \:转义
- \d:数字 相当于[0-9]
- \w:匹配数字、字母、下划线，相当于[a-z0-9A-Z_]
- \s:空格或换行
- g:表示全局匹配  /[a-zA-Z]/g:表示匹配所有的字母,没有g只会匹配第一个字母

##### 案例：

~~~html
<body>
    <script>
        //验证163邮箱
        var str = "12345@163.com";
        var reg = /^\w{5,12}@163\.com$/;
        var result = reg.test(str);
        console.log(result);
    </script>
</body>
~~~



~~~html
<body>
    <script>
        //清除所有的字母
        var str = "12638wydj193sqoe";
        var reg = /[a-zA-Z]/g;
        var res = str.replace(reg,"");
        console.log(res);
    </script>
</body>
~~~



~~~html
<body>
    <script>
        //获取字符串日期中的年月日
        var str = "2023-09-30";
        var reg = /(\d{4})-(\d{2})-(\d{2})/;
        var res = reg.exec(str);
        console.log(res[1]);
        console.log(res[2]);
        console.log(res[3]);
    </script>
</body>
~~~



## 2 ES2015+(ES6) 新特性

### 2.1 变量

使用let代替var。替换后：
1.let有了局部作用域的概念。
2.不存在变量提升。(变量不能使用后再定义)
3.不允许重复声明。
总之，让变量更加规范。



### 2.2 常量

1.const定义常量；
2.定义之后不可以修改；
3.不变的值用常量声明：
4.函数表达式可以使用常量；

~~~html
<body>
    <script>
        // 定义函数，标准使用const，而不是var与let
        const fun = function(){
            console.log("111");
        }
        fun();
    </script>
</body>
~~~



5.对象声明可以使用常量；
6.引入外部模块使用常量，后续讲解。

### 2.3 模板字符串(反引号的使用)

~~~html
<body>
    <script>
        // 使用反引号可以支持字符串换行
        let str = `hello 
world`;
        console.log(str);
    </script>
</body>
~~~



~~~html
<body>
    <script>
        let year = "2023";
        let month = "09";
        let day = "30";
        // 使用反引号配合${}
        console.log(`现在时间为：${year}年${month}月${day}日`);
    </script>
</body>
~~~



### 2.4 解构赋值

1.数组的解构赋值

~~~html
<body>
    <script>
        let month = "09";
        let day = "30";
        //交换两个变量而不使用中间变量
        [month,day] = [day,month];
        console.log(`month = ${month},day = ${day}`);
    </script>
</body>
~~~

2.对象的解构赋值

~~~html
<body>
    <script>
        // 根据变量名进行赋值
        let {name,age} = {age:10,name:"我"};
        console.log(`name = ${name},age = ${age}`);
    </script>
</body>
~~~



3.通过解构赋值传递参数

~~~html
<body>
    <script>
        let xm = {name:"小明",age:10};
        // {name}要求传入的是一个对象，它会自动获取传入对象的name属性
        function f({name}){
            return name;
        }
        let res = f(xm);
        console.log(res);
    </script>
</body>
~~~

### 2.5 函数进阶

对象中的方法可以这样定义：

~~~html
<body>
    <script>
        //原来是：sayName:function(){console.log(this.name);}
        let xm = {name:"小明",sayName(){
            console.log(this.name);
        }};
        xm.sayName();
    </script>
</body>
~~~



#### 2.5.1 设置默认参数值

   

#### 2.5.2 立即执行函数 (function...)()

两个括号：()()

   (function(){console.log("hello");})()

#### 2.5.3 闭包

   ...封装？

#### 2.5.4 箭头函数 ()=>{}

注意：箭头函数与this一起使用时，this的指向会与平常的不同

~~~html
<body>
    <script>
        //箭头函数和普通函数的this指向不同
        //普通函数指向的是调用该函数的对象。
        //箭头函数：在哪里定义，this就指向谁。
        let cat = {
            name:"miaomiao",
            sayName(){
                window.setInterval(() => {
                    // this指向的是cat对象而不是window对象
                    console.log(this.name);
                }, 1000);
            }
        }
        cat.sayName();

    </script>
</body>
~~~



## 3 面向对象

### 3.1 ES5语法：

~~~html
<body>
    <script>
        //通过构造函数为对象设置属性
        function Dog(name,age){
            this.name = name;
            this.age = age;
        }
        //通过原型对象prototype为对应的构造器设置方法，只有使用对应的构造器生成的对象才能调用此方法
        Dog.prototype.sayName = function(){
            console.log(this.name);
        }
        var dog =  new Dog("jojo",3);
        dog.sayName();
    </script>
</body>
~~~

### 3.2 ES6语法：

~~~html
<body>
    <script>
        //有了类的概念
        class Dog{
            constructor(name,age){
                this.name = name;
                this.age = age;
            }
            sayName(){
                console.log(`名字为：${this.name}`);
            }
        }
        let dog = new Dog("jojo",3);
        dog.sayName();
    </script>
</body>
~~~

继承与java一致:extends



## 4 Dom基础

### 4.1 获取dom节点(html元素)

1. ​		document.getElementByld();  根据id属性获取整个节点
2. ​        document.getElementsByClassName(); 根据类名获取dom节点的集合,以数组的方式进行存储
3. ​        document.querySelector();// 根据css选择器获取节点,只返回第一个dom节点
4. ​        document.querySelectorAllO;

### 4.2 监听事件
#### 4.2.1 监听事件类型 click,move...
1. onclick:点击事件

2. onmouseenter:鼠标移入元素
3. onmouseleave:鼠标移出元素
4. onmousemove:鼠标移动时
5. onkeydown: 监听键盘的输入
6. ontouchstart:监听触屏的点击事件
7. ontouchend:监听触屏点击后的抬起事件
8. ontouchmove:监听触屏的点击后的抬起之前的滑动事件
9. window.onscroll:监听鼠标滚轮的移动

~~~html
<body>
    <div id="title">111</dic>
    <script>
        let title = document.querySelector("#title");
        title.onmouseenter = function(){
            console.log("enter");
        }
        title.onmouseleave = function(){
            console.log("leave");
        }
    </script>
</body>
~~~

#### 4.2.2 监听函数的参数：e

1. e.clientX 获取点击的x坐标

2. e.clientY 获取点击的y坐标

3. e.preventDefault() 阻止html标签本身的行为(如点击a标签后的跳转),相当于return false;

4. e.stopPropagation();  阻止事件冒泡(点击li: li->ul->body);
5. e.keyCode 获取键盘输入的字符对应的数字

~~~html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>使用键盘控制来移动的盒子</title>
    <style>
        .box{
            width: 200px;
            height: 200px;
            background-color: red;
            position: absolute;
            top: 100px;
            left: 100px;
        }
    </style>
</head>
<body>
    <div class="box">

    </div>
    <script>
        let box = document.querySelector(".box");
        //注意：是document.onkeydown而不是box.onkeydown
        document.onkeydown = function(e){
            let num = e.keyCode;
            console.log(num);
            switch(num){
                case 37: box.style.left = box.offsetLeft - 5 + "px";break;
                case 38: box.style.top = box.offsetTop - 5 + "px";break;
                case 39: box.style.left = box.offsetLeft + 5 + "px";break;
                case 40: box.style.top = box.offsetTop + 5 + "px";break;
            }
        }
    </script>
</body>
</html>
~~~



#### 4.2.3 例子(移动鼠标，查看大图的功能)：

~~~html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>移动鼠标，查看大图的功能</title>
    <style>
        .picture-list img{
            width:320px;
            height:160px;
        }
        .big-picture-container img{
            width:640px;
            height:320px;
        }
        .big-picture-container{
            position: absolute;
        }
    </style>
</head>
<body>
    <div class="picture-list">
        <img src="images/1.png" alt="">
        <img src="images/2.png" alt="">
        <img src="images/3.png" alt="">
    </div>
    <div class="big-picture-container">

    </div>
    <script>
        let imgList = document.querySelectorAll(".picture-list img");
        let pictureListContainer = document.querySelector(".picture-list");
        let bigPictureContainer = document.querySelector(".big-picture-container");
        for(let i in imgList){
            imgList[i].onmouseenter = function(){
                bigPictureContainer.innerHTML = `<img src="${this.src}" alt="">`;
            }

            imgList[i].onmouseleave = function(){
                bigPictureContainer.innerHTML = ``;
            }
        }

        pictureListContainer.onmousemove = function(e){
            	//加上默认的外边距10px
                bigPictureContainer.style.top = e.clientY + 10 + "px";
                bigPictureContainer.style.left = e.clientX + 10 + "px";
            }

    </script>
</body>
</html>
~~~

**设置监听事件的两种方式:**
1.addEventListener("eventType",fun,boolean)
2.element.onEventType = fun
区别：
1.addEventListener在同一元素上的同一事件类型**添加多个事件，不会被覆盖**，而onEventType会覆盖。
2.addEventListenerT可以设置元素**在捕获阶段触发事件**(默认为false:事件捕获(由外到内 body->div)时触发，true:事件冒泡（由内到外 div->body)时触发)，而onEventType不能,并且默认为在捕获冒泡时触发事件



### 4.3 设置css样式

1. element.style.color
2. element.style.backgroundColor

通过click、.mouseenter、mouseleave事件控制样式

~~~html
<body>
    <div id="title">111</dic>
    <script>
        let title = document.querySelector("#title");
        title.onmouseenter = function(){
            this.style.backgroundColor = "blue";
        }
        title.onmouseleave = function(){
            this.style.backgroundColor = "red";
        }
    </script>
</body>
~~~

1. element.src
2. element.id
3. element.className



### 案例：点击按钮，切换图片demo

~~~html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>切换图片demo</title>
    <style>
        img{
            width: 640px;
            height: 320px;
        }
    </style>
</head>
<body>
    <img src="images/1.png" alt="图片加载失败">
    <br>
    <button class="btn">1</button>
    <button class="btn">2</button>
    <button class="btn">3</button>
    <script>
        let imgs = ["images/1.png","images/2.png","images/3.png"];
        let btns = document.querySelectorAll(".btn");
        let img = document.querySelector("img");
        for(let i in btns){
            btns[i].onclick = function(){
                img.src = imgs[i];
            }
        }
    </script>
    
</body>
</html>
~~~

进阶版：

~~~html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>点击按钮，轮播图片效果</title>
    <style>
        .demo{
            width: 640px;
            height: 320px;
            overflow: hidden;
            position: relative;
        }
        .img-container{
            width: 1920px;
            height: 320px;
            display: flex;
            transition: transform 0.3s;
        }
        .img-container>img{
            width: 640px;
            height: 320px;
        }
        .btn-list{
            position: absolute;
            bottom: 0px;
        }
    </style>
</head>
<body>
    <div class="demo">
        <div class="img-container">
            <img src="images/1.png" alt="图片加载失败">
            <img src="images/2.png" alt="图片加载失败">
            <img src="images/3.png" alt="图片加载失败">
        </div>
        <div class="btn-list">
            <button>1</button>
            <button>2</button>
            <button>3</button>
        </div>
    </div>
    
    <script>
        let btns = document.querySelectorAll(".btn-list button");
        let img = document.querySelector(".img-container");
        for(let i in btns){
            btns[i].onclick = function(){
                //移动的是width:为1920px的图片容器，而不是一张图片
                img.style.transform = `translate(${-640 * i}px)`;
            }
        }
    </script>

</body>
</html>
~~~

### 4. 4DOM节点分类

1、元素节点（获取元素节点：querySelector;querySelectorAll)
2、文本节点(innerHTML)
3、属性节点(element.src;element.id)

#### 4.4.1 dom节点的属性

domObject.innerHTML: innerHTML属性指向了节点的内容

```html
<body>
    <h1 id="title">111</h1>
    <script>
        let title = document.querySelector("#title");
        title.innerHTML = "hello world";
        //还可以直接写入html代码
        title.innerHTML = `<a>111</a>`
    </script>
</body>
```

domObject.offsetLeft:获取dom节点距离左边的偏移量为多少px

domObject.offsetTop:获取dom节点距离上面的偏移量为多少px

#### 4.4.2 dom节点的方法(添加、删除...)

1. 创建元素节点：document.createElement("li")
2. 创建文本节点：document.createTextNode("111")
3. 添加节点：domObject.appendChild()
4. 删除节点：domObject.removeChild()

##### 4.4.2.1 例子(添加水果，点击可以删除)：

~~~html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>js Study</title>
</head>
<body>
    <input type="text">
    <button>添加</button>
    <ul class="fruit-list">
        <li>111</li>
        <li>222</li>
        <li>333</li>
    </ul>
    <script>
        let button = document.querySelector("button");
        let ul = document.querySelector(".fruit-list");
        let input = document.querySelector("input");

        button.onclick = function(){
            let value = input.value;
            //创建li元素节点
            let li = document.createElement("li");
            //创建文本节点
            let textNode = document.createTextNode(value);
            //把文本节点添加到元素节点中
            li.appendChild(textNode);
            //把li元素节点添加的ul元素节点中
            ul.appendChild(li);
        }
		// 这个写法不能删除新添加的元素：
        // let liList = document.querySelectorAll(".fruit-list li");
        // for(let i in liList){
            // liList[i].onclick = function(){
                // ul.removeChild(this);
            // }
        // }
        
        //事件委托
        // 通过e.target将子元素(li)的事件委托给父级(ul)处理。I
        ul.onclick = function(e){
            //e.target指向了事件捕获过程中(点击li:body->ul->li)的最后的元素
            ul.removeChild(e.target);
        }


    </script>
</body>
</html>
~~~



### 5 计时器方法

#### 5.1 setInterval 与 clearlnterval 例子:(一个简单的计时器)：

设置定时任务与取消定时任务

~~~html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>一个简单的计时器</title>
</head>
<body>
    <button class="start">开始</button>
    <button class="pause">暂停</button>
    <button class="end">结束</button>
    <h1 class="time"></h1>
    <script>
        let minute = 0;
        let second = 0;
        let h1 = document.querySelector(".time");
        h1.innerHTML = `${minute}:${second}`;
        
        let startBtn = document.querySelector(".start");
        let timer = null;
        startBtn.onclick = function(){
            //先清除所有的定时任务,防止多次点击按钮时有多个定时任务同时运行,保证唯一性
            clearInterval(timer);
            timer = setInterval(() => {
                if(second === 9){
                    minute++;
                    second = -1;
                }
                second++;
                h1.innerHTML = `${minute}:${second}`;
                //每隔100ms执行一次定时任务
            }, 100);
        }

        let pauseBtn = document.querySelector(".pause");
        pauseBtn.onclick = function(){
            clearInterval(timer);
        }

        let endBtn = document.querySelector(".end");
        endBtn.onclick = function(){
            clearInterval(timer);
            minute = 0;
            second = 0;
            h1.innerHTML = `${minute}:${second}`;
        }


    </script>
        
</body>
</html>
~~~



#### 5.2 setTimeout 与 clearTimeout

设置只执行一次的定时任务与取消此定时任务

#### 5.3 使用防抖与节流来提升性能

~~~html
<script>
    // 防抖动函数，传入核心逻辑代码，返回包装好防抖动代码的函数
        // 功能：鼠标停止滚动后才会开始执行定时任务
        // 使用闭包实现封装
        function debounce(fun){
            let timer = null;
            function eventFun(){
                //如果在等待定时任务开始的0.1s内，再次触发了定时任务，则直接清除，防止多次执行
                if(timer !== null){
                    clearTimeout(timer);
                }
                //鼠标停止滚动达到0.1s时。则定时任务开始执行
                timer = setTimeout(()=>{
                    fun();
                    timer = null;
                },100);
            }
            return eventFun;
            
        }
    
    //节流函数，传入核心逻辑代码，返回包装好节流代码的函数
        // 功能：鼠标滚动时代码执行的次数会大大减少
        function throttle(fun){
            let mark = true;
            return function(){
                if(mark){
                    setTimeout(()=>{
                        fun();
                        mark = true;
                    },500)
                }
                mark = false;
            }
        }
</script>
~~~

#### 5.4 例子：防抖与节流优化返回底部按钮

其中：

1. document.documentElement.scrollTop:页面滚动位置距离顶部的距离，0为没有滚动
2. window.scrollTo(0,0):让页面滚动条返回至顶部。

~~~html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>防抖与节流优化返回底部按钮</title>
    <style>
        body{
            height: 2000px;
        }
        button{
            position: fixed;
            right: 100px;
            bottom: 100px;
            display: none;
        }
    </style>
</head>
<body>
    <h1>hello world</h1>
    <button>返回顶部</button>
    <script>
        let btn = document.querySelector("button");

        btn.onclick = function(){
            //设置水平与垂直的滚动条都为初始状态0
            window.scrollTo(0,0);
        }

        function fun(){
            console.log("scroll");
            btn.style.display = "block";
            //如果成功返回到了顶部
            if(document.documentElement.scrollTop === 0){
                btn.style.display = "none";
            }
        }
        window.onscroll = throttle(fun);

        // 防抖动函数，传入核心逻辑代码，返回包装好防抖动代码的函数
        // 功能：鼠标停止滚动后才会开始执行定时任务
        // 使用闭包实现封装
        function debounce(fun){
            let timer = null;
            function eventFun(){
                //如果在等待定时任务开始的0.1s内，再次触发了定时任务，则直接清除，防止多次执行
                if(timer !== null){
                    clearTimeout(timer);
                }
                //鼠标停止滚动达到0.1s时。则定时任务开始执行
                timer = setTimeout(()=>{
                    fun();
                    timer = null;
                },100);
            }
            return eventFun;
            
        }
        //节流函数，传入核心逻辑代码，返回包装好节流代码的函数
        // 功能：鼠标滚动时代码执行的次数会大大减少
        function throttle(fun){
            let mark = true;
            return function(){
                if(mark){
                    setTimeout(()=>{
                        fun();
                        mark = true;
                    },500)
                }
                mark = false;
            }
        }
    </script>
</body>
</html>
~~~





### 6 BOM对象 浏览器

详细api的使用可见:

 https://www.w3cschool.cn/javascript/yji712hr.html



#### window对象（全局对象）

windowx对象是全局对象，所有在浏览器可以直接使用的方法，都是windowx对象的方法。
1.计时器方法
2.弹出框方法

- alert("提示") 消息提示框,没有返回值，只有确认选项
- prompt("问题","回答占位符") 输入框，返回用户输入的内容
- confirm("是否删除选择的数据") 确认框，有确认与取消的按钮，返回boolean类型

#### screen对象

包含有关用户屏幕的信息。

#### location对象

用于获得当前页面的地址(URL),并把浏览器重定向到新的页面。

- location.href-属性返回当前页面的URL-"https:/www.baidu.com/"
- location.hostname-主机的域名-"www.baidu.com"
- location.pathname-当前页面的路径和文件名“/s”
- location.port-端口-"8080”
- location..protocol-协议-“https:"

页面跳转location.href="http:/baidu.com

#### history对象

包含浏览器的历史。

#### navigator对象

包含有关访问者浏览器的信息。



### 7 原始类型与引用类型

#### 7.1 类型检测:
**一、原始数据类型**检测：

1. number
2. string
3. boolean
4. null  **typeof(null),返回object,typeof(对象)，也是返回object,但是null是原始数据类型**
5. undefined

使用 typeof(1),返回number



二、引用数据类型检测：值 instanceof 类型，返回boolean

原始类型 instanceof Object,返回false

其他与java一致，可以判断是否属于这个类或者这个类的子类



### 8 异步编程(多线程)

异步获取数据的方式：

1.使用回调函数，把函数作为参数进行传递

![https://image.itbaima.net/images/173/image-20231007169024791.png](https://image.itbaima.net/images/173/image-20231007169024791.png)

2.使用promise对象获取数据

~~~html
<body>
    <script>
        let target = "hello world";

        let p = new Promise((resolve)=>{
            //setTimeout是异步执行的,resolve是一个函数
            setTimeout(()=>{
                resolve(target);
            },500);
        });
        //使用then方法设置参数d接收异步的数据并且进行消费
        p.then((d)=>{
            console.log(d);
        })

    </script>
</body>
~~~



3.async函数解决异步问题

~~~html
<body>
    <script>
        let target = "hello world";

        function getData(){
            return new Promise((resolve)=>{
            //setTimeout是异步执行的
            setTimeout(()=>{
                resolve(target);
            },500);
        });
        }
        //定义 async函数
        async function f(){
            //可以使用await,等待getData()函数获取到值后再进行执行下去
            let data = await getData();
            console.log(data);
        }
        f();

    </script>
</body>
~~~

