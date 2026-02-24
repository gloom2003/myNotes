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

遍历方式：

~~~html
<script>
    for(let name in cat){
        // name为cat对象的属性名
        // cat[name]为属性值
    }
</script>
~~~



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
      // 类似java版本
      for(let i = 0;i<arr.length;i++){
          consile.log(arr[i]);
      }
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



#### map的使用例子

map的返回值：

~~~js
function joinFieldByComma(arr, field) {
    // map()返回的是数组： [37,44,48] join后返回字符串：37,44,48
    return arr.map(obj => obj[field]).join(',');
}

// 示例使用
const data = [
    { id: 37, name: "Alice" },
    { id: 44, name: "Bob" },
    { id: 48, name: "Charlie" }
];

const result = joinFieldByComma(data, 'id');
console.log(result);  // 输出: "37,44,48"

~~~

**`map` 方法：**

- `arr.map(obj => obj[field])` 遍历数组 `arr` 中的每个对象 `obj`，并对每个对象执行 `obj[field]` 操作。
- `obj[field]` 获取当前对象 `obj` 中的字段 `field` 的值。比如对象 `{ id: 37, name: "Alice" }`，指定字段 `id` 时就会获取值 `37`。
- 最终 `map` 会返回一个新数组，数组中的每个元素是对象中提取出的字段值。

对于例子 `data = [{ id: 37, name: "Alice" }, { id: 44, name: "Bob" }, { id: 48, name: "Charlie" }]`，执行 `arr.map(obj => obj[field])` 会返回 `[37, 44, 48]`。

**`join` 方法：**

- `join(',')` 将数组中的元素以逗号 `,` 作为分隔符拼接成一个字符串。
- 对 `[37, 44, 48]` 调用 `join(',')` 将会返回字符串 `"37,44,48"`。



### forEach遍历不能return中断

1. **`return` 只退出当前函数**
   `return` 只能退出当前的函数，而你在 `forEach` 回调函数中使用了 `return`，它只退出当前的迭代（即当前的 `forEach` 回调函数），不会停止整个 `forEach` 的执行，也不会退出 `subSaveContent()` 函数。
2. **`forEach` 无法被中途终止**
   `forEach` 是数组的一个遍历方法，它设计上不支持通过 `return` 或其他方式中途退出整个遍历。因此，后面的逻辑仍然会继续执行。

------

### 解决方法

为了能够在满足条件时中止遍历并退出 `subSaveContent`，你需要替换 `forEach` 为其他支持中断的遍历方式，比如 `for...of`。



###  **总结 `for...of` 和 `for...in` 的区别**：

- `for...of`

  ：

  - 适用于遍历数组、字符串、Map、Set 等可迭代对象。
  - 直接访问集合中的值。

- `for...in`

  ：

  - 适用于遍历对象的可枚举属性（键）。
  - 访问对象的键名，并通过 `对象[key]` 形式访问对应的值。

例子：

在这段代码中，`for...of` 用于遍历数组中的每个对象，`for...in` 则用于遍历对象中的每个属性。

~~~js
			// 判断数组中的每个对象的字段是否都填了值，是否所有信息都填完了
			hasEmptyValues(data) {
			    for (let item of data) {
                    // item就是数组中的每一个对象
			        for (let key in item) {
                        // key就是每个对象属性，字符串类型
			            if (item[key] === null || item[key] === undefined || item[key] === '' || item[key].length == 0) {
			                return true; // 发现空值，返回 true
			            }
			        }
			    }
			    return false; // 所有值都不为空，返回 false
			},
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

  ~~~js
  (res) => {
      if (res.confirm) {
          console.log(`this.mergArryIndex = ${JSON.stringify(this.mergArryIndex)}`);
          // 获取要删除的发票对象降序后,从后向前删除
          this.mergArryIndex.sort((a, b) => b - a);
          for(let i = 0; i < this.mergArryIndex.length; i++){
              let deletedIndex = this.mergArryIndex[i];
              console.log(`要删除的发票对象索引: deletedIndex = ${deletedIndex}`);
              this.allInvoiceMsg.splice(deletedIndex,1);
          }
  }
  ~~~

  

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



#### splice删除：

![image-20240805111347514](C:\Users\Gloom\AppData\Roaming\Typora\typora-user-images\image-20240805111347514.png)

![image-20240805111317613](C:\Users\Gloom\AppData\Roaming\Typora\typora-user-images\image-20240805111317613.png)

`splice` 方法的语法为：`array.splice(start, deleteCount)`，其中 `start` 是开始删除的位置，`deleteCount` 是要删除的元素个数。



#### findIndex 根据条件查询元素的索引

~~~js
function saveHistory(){
				let historyArr= [1,2,3];
				let item={
					id:this.detail.id,
					classid:this.detail.classid,
					picurl:this.detail.picurl,
					title:this.detail.title,
					looktime:parseTime(Date.now())
				}
				
				let index = historyArr.findIndex(i=>{
					return i.id == this.detail.id
				})
				
				if(index>=0){
					historyArr.splice(index,1)
				}
			}
~~~



#### slice

~~~js
				// 最多记录10条历史记录
				historyArr=historyArr.slice(0,10)	
~~~

这段JavaScript代码的作用是对数组 `historyArr` 进行裁剪，只保留数组的前10个元素，删除其余的元素。

代码解析：
```javascript
historyArr = historyArr.slice(0, 10);
```

1. **`historyArr`**：这是一个数组，可能用于存储一些历史记录或其他数据。

2. **`slice(0, 10)`**：
   - `slice()` 是JavaScript中的一个数组方法，用于从数组中提取一个片段，并返回一个新的数组。**它不会修改原始数组。**
   - **`0`**：这是起始索引，表示从数组的第一个元素开始（索引从0开始）。
   - **`10`**：这是结束索引（不包括该索引对应的元素）。所以 `slice(0, 10)` 将提取 `historyArr` 的前10个元素（从索引0到索引9的元素）。

3. **`historyArr =`**：这部分将 `slice()` 返回的新数组重新赋值给 `historyArr`，因此 `historyArr` 现在只包含前10个元素。

应用场景：

这种操作通常用于限制数组的长度。例如，保留最近的10条历史记录，或限制数据集的大小，以避免处理过多的数据或浪费内存。



### 1.6 常用的内置对象

#### 1.6.1 Array-数组 join,sort,map见上文。



#### 1.6.2 Math.abs(),random()...

- Math.floor():向下取整,（正数使用：去除数字的小数部分，负数使用：小数部分使用1来代替）
- Math.random():0-1的随机数
- Math.abs():绝对值
- Math.sqrt(9):开方
- Math.pow(2,4):乘方

在JavaScript中，可以通过多种方式将浮点数2.5转换为整数2。以下是几种常见的方法：

1. 使用`Math.floor()`方法：
   ```javascript
   let number = 2.5;
   let integer = Math.floor(number); // 结果为2
   ```

2. 使用`parseInt()`方法：
   ```javascript
   let number = 2.5;
   let integer = parseInt(number); // 结果为2
   ```

3. 使用`Math.trunc()`方法（ES6引入）：
   ```javascript
   let number = 2.5;
   let integer = Math.trunc(number); // 结果为2
   ```

4. 使用按位运算符`| 0`：
   ```javascript
   let number = 2.5;
   let integer = number | 0; // 结果为2
   ```

5. 使用双重否定运算符`~~`：
   ```javascript
   let number = 2.5;
   let integer = ~~number; // 结果为2
   ```

6. 使用`Math.round()`方法，如果你希望四舍五入：
   ```javascript
   let number = 2.5;
   let integer = Math.round(number); // 结果为3
   ```

前五种方法都会将2.5转换为2，而`Math.round()`方法会将2.5转换为3，因为它是四舍五入的。根据你的需求选择合适的方法。



#### 1.6.3 Date

Date对象

```javascript
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



##### 格式化为字符串

你可以使用 `Date` 对象的 `toLocaleString` 方法或者 `Date` 的 `getFullYear`、`getMonth` 等方法将日期格式化为你需要的字符串格式。假设你想把 `createAt` 字段格式化为 `"yyyy-MM-dd HH:mm:ss"` 格式，可以这样做：

```javascript
this.addAfterServiceFrom.afterServiceMaterialList.push({
    materialName: '',
    materialQuantity: '',
    createAt: formatDate(new Date()),
});

// 格式化函数
function formatDate(date) {
    const year = date.getFullYear();
    const month = (date.getMonth() + 1).toString().padStart(2, '0');
    const day = date.getDate().toString().padStart(2, '0');
    const hours = date.getHours().toString().padStart(2, '0');
    const minutes = date.getMinutes().toString().padStart(2, '0');
    const seconds = date.getSeconds().toString().padStart(2, '0');
    return `${year}-${month}-${day} ${hours}:${minutes}:${seconds}`;
}
```

这样，`createAt` 字段会被格式化为 `yyyy-MM-dd HH:mm:ss` 形式的字符串。

### 解释：

- `padStart(2, '0')` 用来保证每个日期部分（如月、日、小时等）是两位数，前面补充 `0`。
- `formatDate` 函数将日期转换为你需要的格式。





解释代码：	setHours()		

```js
isHandLate(log,create) { 			
    let date1 = new Date(log); 			
    let date2 = new Date(create); 			
    return (date1.setHours(0, 0, 0, 0) == date2.setHours(0, 0, 0, 0));
}
```



1. **设置时间为午夜**:

   ```js
   return (date1.setHours(0, 0, 0, 0) == date2.setHours(0, 0, 0, 0));
   ```

   

   `setHours(0, 0, 0, 0)` 是一个方法，它将 `date1` 和 `date2` 的时间部分设置为当天的午夜（00:00:00.000）。这样做的目的是忽略时间，只比较日期部分。

   - `date1.setHours(0, 0, 0, 0)` 和 `date2.setHours(0, 0, 0, 0)` 都会返回相应日期的时间戳（自1970年1月1日00:00:00 UTC以来的毫秒数）。
   - 最后，通过比较这两个日期的时间戳来判断它们是否在同一天。如果时间戳相等，说明这两个日期在同一天。

2. **返回值**:

   - 如果 `date1` 和 `date2` 的日期部分相同（即在同一天），则返回 `true`。
   - 否则，返回 `false`。

总结：

这个函数 `isHandLate` 判断两个日期是否在同一天。如果它们的日期部分相同（忽略时间部分），则返回 `true`，否则返回 `false`。



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
- **`(\\S+站)`**:
  - `\\S+`：匹配一个或多个非空白字符。
  - `站`：匹配站点关键字。
  - 整体匹配站点名称。
- g:表示全局匹配  /[a-zA-Z]/g:表示匹配所有的字母,没有g只会匹配第一个字母

组合：

~~~java
// .*表示匹配任意的字符串（包括空字符""） 是贪婪的，会尽可能多地匹配
// .*? 表示尽可能少的匹配任意的字符串，非贪婪匹配
// ^$表示匹配空字符""

~~~



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



#### Map的使用

~~~js
			submitForm(){
				let invoice = this.allInvoiceMsg[this.showIncoiceIndex];
				let invoiceType = invoice.invoiceType;
				// 验证必填字段是否填写
				
				// 验证差旅报销下火车票的必填字段
				if(this.reimbursementCategory == 1 && invoiceType == 1){
					
					// 使用 Map 存储待验证字段的英文名（key）和中文名（value）
					let needFieldMap = new Map([
					    ['startTime', '开始时间'],
					    ['endTime', '结束时间'],
					    ['totalMoney', '金额']
					]);
					
					for (let [key, value] of needFieldMap) {
					    if (invoice[key] == undefined || invoice[key] == '' || invoice[key].length == 0) {
							uni.showModal({
								title: '消息提示',
								content: `请填写${value} ！`,
								showCancel : false
							});
							return;
					    }
					}
					
				}
            }
~~~



#### Set的使用

如果需要在删除 `id` 之前进行去重，可以使用 `Set` 数据结构来实现。`Set` 自动去重，是处理数组重复项的简洁方法。

以下是你的代码的优化版本：

### 优化后的代码

```javascript
// 去重后删除
const uniqueIdList = [...new Set(newUploadedIdList)]; // 使用 Set 去重

for (let id of uniqueIdList) {
  await deleteIllegalFile(id); // 执行删除操作
}
```

### 详细说明：

1. **`new Set()`**：
   - 将数组转换为 `Set`，自动去除重复项。
   - 使用 `[...new Set(array)]` 可将 `Set` 再转回数组。
2. **去重后处理**：
   - 保证每个 `id` 只被删除一次，避免重复调用 `deleteIllegalFile`。





当然，这是一个非常好的问题，也是很多初学者（尤其是从Java等强类型、面向对象语言转过来）容易困惑的地方。这涉及到JavaScript的模块系统（ES6 Modules）的一个核心概念。

我将分两部分来解答：

1.  **核心问题**：为什么 `export default service` 可以用 `import request from ...` 来导入？
2.  **文件解析**：`src/api/index.js` 这个文件具体是做什么的。

---

### 笔记：JavaScript ES6模块导入/导出机制

#### 1. 核心问题解答：`export default` 与 `import`

在JavaScript的ES6模块系统中，有两种主要的导出方式：**命名导出 (Named Exports)** 和 **默认导出 (Default Export)**。

**A. 默认导出 (Default Export)**

-   **语法**: `export default 变量/函数/对象;`
-   **特点**:
    -   一个文件（模块）中，**最多只能有一个** `export default`。
    -   它导出的不是名字，而是那个值/对象本身。可以理解为，它在告诉其他文件：“这是我这个模块默认要给你的东西。”
-   **导入**:
    -   当你从一个有 `export default` 的模块导入时，你可以 **为导入的东西指定任意的名字**。
    -   **语法**: `import 你自己起的名字 from './模块路径';`

**这就是你遇到的情况：**

- 在 `src/utils/request.js` 中：

  ```javascript
  const service = axios.create({ ... });
  // ... 拦截器配置 ...
  export default service;
  ```

  这里是说：“我这个 `request.js` 文件，默认导出的就是这个配置好的 `axios` 实例，它的名字叫 `service`。”

- 在 `src/api/index.js` 中：

  ```javascript
  import request from '../utils/request';
  ```

  这里是说：“我要从 `../utils/request` 这个模块导入它 **默认导出** 的那个东西，并且在我当前这个文件里，我决定把它命名为 `request`。”

**结论**：因为 `request.js` 使用了 `export default`，所以在导入时，导入方可以随意命名。`request` 这个名字在这里比 `service` 更贴合“发起请求”这个语义，所以开发者选择了这个名字。你完全可以写成 `import myAxiosInstance from '../utils/request'`，代码也一样能工作。

**B. 命名导出 (Named Exports)** - 作为对比

-   **语法**: `export const a = 1;` 或 `export function myFunction() {};`
-   **特点**:
    -   一个文件中可以有 **任意多个** `export`。
    -   它导出的是变量/函数本身，并且 **名字是固定的**。
-   **导入**:
    -   导入时 **必须使用花括号 `{}`**，并且 **名字必须和导出的名字完全匹配**。
    -   **语法**: `import { a, myFunction } from './模块路径';`
    -   如果你想用别的名字，需要使用 `as` 关键字：`import { myFunction as myFunc } from './模块路径';`

**Java类比**:
`export default` 没有一个完美的Java对应。你可以把它想象成一个类的“主要”静态工厂方法。
而 `export` （命名导出）则更像Java中的 `public static` 成员或方法，你可以通过 `import static com.mypackage.MyClass.myFunction;` 来导入，名字必须匹配。



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

### 4.1 获取dom节点的方式

1 		let timer = document.getElementByld("time");  **根据id属性**获取整个节点
2        let arr = document.getElementByTagName("li") **根据标签名字**从document对象中**获取所有的**li标签(包括li和里面的文本内容)，存放在数组中进行返回，相当于document.querySelectorAll()的功能但是兼容老版本
3        let boxs = document.getElementsByClassName('box'); **根据类名**获取dom节点的集合,以数组的方式进行存储
4       let box = document.querySelector('.box');  **根据css选择器**获取节点,只返回第一个dom节点
5       document.querySelectorAll();

特殊获取方式：

~~~html
    <script>
        let bodyEle = document.body; // 获取body标签
        let htmlEle = document.documentElement; // 获取html标签
        console.log(bodyEle);
        console.dir(htmlEle); // dir方法可以查看对象的各种属性
    </script>
~~~



### 4.2 监听事件
#### 4.2.1 监听事件类型 click,move...

**鼠标**类：

1. onclick:鼠标点击事件

2. onmouseenter:鼠标移入元素

3. onmouseleave:鼠标移出元素

4. onmousemove:鼠标移动时

5. window.onscroll:监听鼠标滚轮的移动

6. 输入框获得焦点时：onfucus

7. 输入框失去焦点时：onblur

如：仿京东输入框：

~~~html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>test</title>
    <style>
        input{
            color: #999;
        }
    </style>
</head>
<body>
    <input type="text" value="手机">
    <script>
        let text = document.querySelector("input");
        text.onfocus = function(){
            if(text.value === '手机'){
                text.value = '';
            }
            text.style.color = '#333';
        }
        text.onblur = function(){
            if(text.value === ''){
                text.value = '手机';
            }
            text.style.color = '#999';
        }
    </script>
</body>
</html>
~~~



**键盘**类：
1 onkeydown: 监听键盘的输入

**触屏**类：

1 ontouchstart:监听触屏的点击事件

2 ontouchend:监听触屏点击后的抬起事件

3 ontouchmove:监听触屏的点击后的抬起之前的滑动事件

   

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
            imgList[i].onmouseenter = function(){ // 使用反引号+ ${}，添加变量到字符串中
                bigPictureContainer.innerHTML = `<img src="${this.src}" alt="">`;
            }

            imgList[i].onmouseleave = function(){
                bigPictureContainer.innerHTML = ``;
            }
        }
        // 实现大图跟随着鼠标的移动而移动
        pictureListContainer.onmousemove = function(e){
                bigPictureContainer.style.top = e.clientY + 10 + "px";
                bigPictureContainer.style.left = e.clientX + 10 + "px";
            }

    </script>
</body>
</html>
~~~



### 4.3 事件高级

#### (1)注册事件（绑定事件）

注册事件有两种方式：传统方式 和 方法监听注册方式

**传统注册方式** 

 1 利用 on 开头的事件 onclick 

  

```html
<button onclick=“alert('hi~')”> < /button> 
```

 2 btn.onclick = function() {} 

  特点： 注册事件的**唯一性** 

即：同一个元素同一个事件只能设置一个处理函数，最 后注册的处理函数将会覆盖前面注册的处理函数

**方法监听注册方式** 

- 使用addEventListener()方法
- 同一个元素同一个事件可以注册多个监听器 
- 按注册顺序依次执行

 

```html
<script>
    eventTarget.addEventListener(type, listener[, useCapture])  
</script>
```

eventTarget.addEventListener()方法将指定的监听器注册到 eventTarget（目标对象）上，当该对象触发指定的事件时，就会执行事件处理函数。

该方法接收三个参数： 

type：事件类型是字符串，比如 click 、mouseover ，**注意**这里不要带 on 

listener：事件处理函数，事件发生时，会调用该监听函数

useCapture：可选参数，是一个布尔值，默认是 false表示在冒泡阶段运行

例如：

~~~html
<body>
    <button>传统注册事件</button>
    <button>方法监听注册事件</button>
    <button>ie9 attachEvent</button>
    <script>
        var btns = document.querySelectorAll('button');
        // 1. 传统方式注册事件
        btns[0].onclick = function() {
            alert('hi');
        }
        btns[0].onclick = function() {
                alert('hao a u');
            }
            // 2. 事件侦听注册事件 addEventListener 
            // (1) 里面的事件类型是字符串 必定加引号 而且不带on
            // (2) 同一个元素 同一个事件可以添加多个侦听器（事件处理程序）
        btns[1].addEventListener('click', function() {
            alert(22);
        })
        btns[1].addEventListener('click', function() {
                alert(33);
            })
            // 3. attachEvent ie9以前的版本支持
        btns[2].attachEvent('onclick', function() {
            alert(11);
        })
    </script>
</body>

~~~



#### (2)删除事件（解绑事件）

1 传统方式
eventTarget.onclick = null; 

2 方法监听方式 

①   eventTarget.removeEventListener(type, listener[, useCapture]); 

②   eventTarget.detachEvent(eventNameWithOn, callback); 

例如：

~~~html
<body>
    <div>1</div>
    <div>2</div>
    <div>3</div>
    <script>
        var divs = document.querySelectorAll('div');
        divs[0].onclick = function() {
                alert(11);
                // 1. 传统方式删除事件
                divs[0].onclick = null;
            }
            // 2. removeEventListener 删除事件
        divs[1].addEventListener('click', fn) // 里面的fn 不需要调用加小括号

        function fn() {
            alert(22);
            divs[1].removeEventListener('click', fn);
        }
        // 3. detachEvent
        divs[2].attachEvent('onclick', fn1);

        function fn1() {
            alert(33);
            divs[2].detachEvent('onclick', fn1);
        }
    </script>
</body>
~~~



#### (3)DOM事件流 

![](img/事件流.png)

1 JS 代码中只能执行捕获或者冒泡其中的一个阶段。
2 onclick 和 attachEvent（ie） 只能得到冒泡阶段

3 addEventListener(type, listener[, useCapture])第三个参数如果是 true，表示在事件捕获阶段调用事件处理程序；

如果是 false（不写**默认就是false**），表示在事件冒泡阶段调用事件处理程序。 

4 实际开发中我们很少使用事件捕获，我们**更关注事件冒泡**。 

5 有些事件是没有冒泡的，比如 onblur、onfocus、onmouseenter、onmouseleave 

6 事件冒泡有时候会带来麻烦，有时候又会帮助很巧妙的做某些事件

 例如：

~~~html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
    <style>
        .father {
            overflow: hidden;
            width: 300px;
            height: 300px;
            margin: 100px auto;
            background-color: pink;
            text-align: center;
        }
        
        .son {
            width: 200px;
            height: 200px;
            margin: 50px;
            background-color: purple;
            line-height: 200px;
            color: #fff;
        }
    </style>
</head>

<body>
    <div class="father">
        <div class="son">son盒子</div>
    </div>
    <script>
        // dom 事件流 三个阶段
。
        // 3. 捕获阶段 如果addEventListener 第三个参数是 true 那么则处于捕获阶段  document -> html -> body -> father -> son
        var son = document.querySelector('.son');
        son.addEventListener('click', function() {
            alert('son');
        }, true);
        var father = document.querySelector('.father');
        father.addEventListener('click', function() {
            alert('father');
        }, true); // 结果：此时点击son,会先执行father的点击事件再执行son的点击事件
        
        // 4. 冒泡阶段 如果addEventListener 第三个参数是 false 或者 省略 那么则处于冒泡阶段  son -> father ->body -> html -> document
        var son = document.querySelector('.son');
        son.addEventListener('click', function() {
            alert('son');
        }, false);
        var father = document.querySelector('.father');
        father.addEventListener('click', function() {
            alert('father');
        }, false);
        document.addEventListener('click', function() {
            alert('document');
        })// 结果：此时点击son,会先执行son的点击事件再执行father的点击事件,最后执行document的点击事件
    </script>
</body>

</html>
~~~



#### (4)事件对象 e

1. event 就是一个事件对象 写到我们侦听函数的 小括号里面 当形参来看

2. 事件对象只有有了事件才会存在，它是系统给我们自动创建的，不需要我们传递参数
3. 事件对象 是 我们事件的一系列相关数据的集合 跟事件相关的 比如鼠标点击里面就包含了鼠标的相关信息，鼠标坐标啊，如果是键盘事件里面就包含的键盘事件的信息 比如 判断用户按下了那个键

4. 这个事件对象我们可以自己命名 比如 event 、 evt、 e

5. 事件对象也有兼容性问题 ie678 通过 window.event 兼容性的写法  e = e || window.event;

例如：

~~~html
<body>
    <div>123</div>
    <script>
        // 事件对象
        var div = document.querySelector('div');
        div.onclick = function(e) {
                // console.log(e);
                // console.log(window.event);
                // // 解决浏览器的兼容性问题,ie浏览器中识别不了e会返回undefined,相当于false,然后就会去使用能够识别的window.event
                // e = e || window.event;
                console.log(e);


            }
            div.addEventListener('click', function(e) {
                    console.log(e);
            })
    </script>
</body>
~~~

##### e的常用属性和方法:

![](img/e常用属性.jpg)

##### e.target 指向触发事件的对象

e.target与this的区别：

1. e.target 返回的是触发事件的对象（元素）在点击事件中就是被点击的哪一个元素
2. this：返回的是绑定事件的对象（元素）

例如：

~~~html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
    <style>
        div {
            width: 100px;
            height: 100px;
            background-color: pink;
        }
    </style>
</head>

<body>
    <div>123</div>
    <ul>
        <li>abc</li>
        <li>abc</li>
        <li>abc</li>
    </ul>
    <script>
        // 常见事件对象的属性和方法
        // 区别 ： e.target: 点击了那个元素，就返回那个元素  this :那个元素绑定了这个点击事件，那么就返回谁
        var div = document.querySelector('div');
        div.addEventListener('click', function(e) {
            console.log(e.target);
            console.log(this);

        })
        var ul = document.querySelector('ul');
        ul.addEventListener('click', function(e) {
                // 我们给ul 绑定了事件  那么this 就指向ul  
                console.log(this);
            	// 2. 了解 跟 this 有个非常相似的属性 e.currentTarget  ie678不认识
                console.log(e.currentTarget);

                // e.target 指向我们点击的那个对象 谁触发了这个事件 我们点击的是li e.target 指向的就是li
                console.log(e.target); // 结果： 点击li时，由于默认为事件冒泡，所以li->ul->body->html->document
            // 最后输出: ul ul li

            })
            // 了解兼容性
            // div.onclick = function(e) {
            //     e = e || window.event;
            //     var target = e.target || e.srcElement;
            //     console.log(target);

        // }

    </script>
</body>

</html>
~~~

##### e.preventDefault() 阻止默认行为

~~~html
<body>
    <div>123</div>
    <a href="http://www.baidu.com">百度</a>
    <form action="http://www.baidu.com">
        <input type="submit" value="提交" name="sub">
    </form>
    <script>
        
        // 1. 返回事件类型
        var div = document.querySelector('div');
        div.addEventListener('click', fn);
        div.addEventListener('mouseover', fn);
        div.addEventListener('mouseout', fn);

        function fn(e) {
            console.log(e.type); // 结果：click mouseover mouseout

        }
        // 2. 阻止默认行为（事件） 让链接不跳转 或者让提交按钮不提交
        var a = document.querySelector('a');
        a.addEventListener('click', function(e) {
                e.preventDefault(); //  dom 标准写法
            })
            // 3. 传统的注册方式
        a.onclick = function(e) {
            // 普通浏览器 e.preventDefault();  方法
            // e.preventDefault();
            // 低版本浏览器 ie678  returnValue  属性
            // e.returnValue;
            // 我们可以利用return false 也能阻止默认行为 没有兼容性问题 特点： return 后面的代码不执行了， 而且只限于传统的注册方式
            return false;
            alert(11);
        }
    </script>
</body>
~~~



#### (5)阻止事件冒泡 

dom 推荐的标准 : e.stopPropagation()

例如：

~~~html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
    <style>
        .father {
            overflow: hidden;
            width: 300px;
            height: 300px;
            margin: 100px auto;
            background-color: pink;
            text-align: center;
        }
        
        .son {
            width: 200px;
            height: 200px;
            margin: 50px;
            background-color: purple;
            line-height: 200px;
            color: #fff;
        }
    </style>
</head>

<body>
    <div class="father">
        <div class="son">son儿子</div>
    </div>
    <script>
        // 常见事件对象的属性和方法
        // 阻止冒泡  dom 推荐的标准 stopPropagation() 
        var son = document.querySelector('.son');
        son.addEventListener('click', function(e) {
            alert('son');
            e.stopPropagation(); // stop 停止  Propagation 传播
            window.event.cancelBubble = true; // 非标准 cancel 取消 bubble 泡泡
        }, false);

        var father = document.querySelector('.father');
        father.addEventListener('click', function() {
            alert('father');
        }, false);
        document.addEventListener('click', function() {
            alert('document');
        })
    </script>
</body>

</html>
~~~



 #### (6)事件委托（代理、委派）

**原理**：想要给每个子节点设置监听事件的效果时时，不是每个子节点单独设置事件监听器，而是将事件监听器设置在其父节点上，然后利用冒泡原理影响设置每个子节点。

**作用：** 

我们只操作了一次 DOM ，提高了程序的性能。 

例如：

~~~html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
</head>

<body>
    <ul>
        <li>知否知否，点我应有弹框在手！</li>
        <li>知否知否，点我应有弹框在手！</li>
        <li>知否知否，点我应有弹框在手！</li>
        <li>知否知否，点我应有弹框在手！</li>
        <li>知否知否，点我应有弹框在手！</li>
    </ul>
    <script>
        // 事件委托的核心原理：给父节点添加侦听器， 利用事件冒泡影响每一个子节点
        var ul = document.querySelector('ul');
        ul.addEventListener('click', function(e) {
            // alert('知否知否，点我应有弹框在手！');
            // e.target 这个可以得到我们点击的对象
            e.target.style.backgroundColor = 'pink';


        })
    </script>
</body>

</html>
~~~



#### (7)常用的鼠标事件  

![](img/鼠标事件.jpg)

- contextmenu：鼠标右键后触发
- selectstart：选中文字后触发

例如：

~~~html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
</head>

<body>
    我是一段不愿意分享的文字
    <script>
        // 1. contextmenu 我们可以禁用鼠标右键后显示的菜单
        document.addEventListener('contextmenu', function(e) {
                e.preventDefault();
            })
            // 2. 禁止选中文字 selectstart
        document.addEventListener('selectstart', function(e) {
            e.preventDefault();

        })
    </script>
</body>

</html>
~~~

现阶段我们主要是用

鼠标事件对象 MouseEvent 和键盘事件对象 KeyboardEvent。

如：

~~~html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
    <style>
        body {
            height: 3000px;
        }
    </style>
</head>

<body>
    <script>
        // 鼠标事件对象 MouseEvent
        document.addEventListener('click', function(e) {
            console.log(e); // 老师结果：MouseEvent 我的结果：PointerEvent
            // 1. client 鼠标在可视区的x和y坐标 当前可视区左上角为原点
            console.log(e.clientX);
            console.log(e.clientY);
            console.log('---------------------');

            // 2. page 鼠标在页面文档的x和y坐标 当前页面文档左上角为原点
            console.log(e.pageX);
            console.log(e.pageY);
            console.log('---------------------');
            

            // 3. screen 鼠标在电脑屏幕的x和y坐标 当前电脑屏幕左上角为原点
            console.log(e.screenX);
            console.log(e.screenY);

        })
    </script>
</body>

</html>
~~~

mousemove事件：

案例：跟随鼠标的天使

~~~html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    <style>
        img{
            position: absolute;
        }
    </style>
</head>
<body>
    <img src="../images/angel.gif" alt="">
    <script>
        let pic = document.querySelector("img");
        // 设置当鼠标在document里面移动时触发事件
        document.addEventListener('mousemove',function(e){
            let x = e.pageX;
            let y = e.pageY;
            pic.style.top = y + 'px';
            pic.style.left = x + 'px';
        })
    </script>
</body>
</html>
~~~



#### (8)常用的键盘事件

##### 按下与松开键盘的事件

- onkeydown 
- onkeypress 
- onkeyup 

①  注意： keydown 和 keypress 在文本框里面的特点： 他们两个事件触发的时候，文字还没有落入文本框中。

②  keyup事件触发的时候， 文字已经落入文本框里面了 

应用：

~~~html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
</head>

<body>
    <script>
        // 常用的键盘事件
        //1. keyup 按键弹起的时候触发 
        // document.onkeyup = function() {
        //         console.log('我弹起了');

        //     }
        document.addEventListener('keyup', function() {
            console.log('我弹起了');
        })

        //3. keypress 按键按下的时候触发  不能识别功能键 比如 ctrl shift 左右箭头啊
        document.addEventListener('keypress', function() {
                console.log('我按下了press');
            })
            //2. keydown 按键按下的时候触发  能识别功能键 比如 ctrl shift 左右箭头啊
        document.addEventListener('keydown', function() {
                console.log('我按下了down');
            })
            // 4. 三个事件的执行顺序  keydown -- keypress -- keyup,与添加监听事件的顺序无关
    </script>
</body>

</html>
~~~

##### e.keyCode的使用

~~~html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
</head>

<body>
    <script>
        // 键盘事件对象中的keyCode属性可以得到相应键的ASCII码值
        // 1. 我们的keyup 和keydown事件不区分字母大小写  a 和 A 得到的都是65
        // 2. 我们的keypress 事件 区分字母大小写  a  97 和 A 得到的是65
        document.addEventListener('keyup', function(e) {
            // console.log(e);
            console.log('up:' + e.keyCode);
            // 我们可以利用keycode返回的ASCII码值来判断用户按下了那个键
            if (e.keyCode === 65) {
                alert('您按下的a键');
            } else {
                alert('您没有按下a键')
            }

        })
        document.addEventListener('keypress', function(e) {
            // console.log(e);
            console.log('press:' + e.keyCode);

        })
    </script>
</body>

</html>
~~~

##### 例子：

模拟京东按s快捷键输入内容

~~~html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
</head>

<body>
    <input type="text">
    <script>
        // 核心思路： 检测用户是否按下了s 键，如果按下s 键，就把光标定位到搜索框里面
        // 使用键盘事件对象里面的keyCode 判断用户按下的是否是s键
        
        var search = document.querySelector('input');
        // 使用keyup事件（更加常用，输入完成后再进行相关的操作），键盘松起时才获得焦点，使用keydown容易触发多次然后把s输入到获得焦点后的输入框中
        document.addEventListener('keyup', function(e) {
            // console.log(e.keyCode);
            if (e.keyCode === 83) {
                // 搜索框获得焦点： 使用 js 里面的 focus() 方法
                search.focus();
            }
        })
    </script>
</body>

</html>
~~~

例子：模拟京东快递单号查询案例

~~~html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    <style>
        * {
            margin: 0;
            padding: 0;
        }
        
        .search {
            position: relative;
            width: 178px;
            margin: 100px;
        }
        
        .con {
            display: none;
            position: absolute;
            top: -40px;
            width: 171px;
            border: 1px solid rgba(0, 0, 0, .2);
            box-shadow: 0 2px 4px rgba(0, 0, 0, .2);
            padding: 5px 0;
            font-size: 18px;
            line-height: 20px;
            color: #333;
        }
        
        .con::before {
            content: '';
            width: 0;
            height: 0;
            position: absolute;
            top: 28px;
            left: 18px;
            border: 8px solid #000;
            border-style: solid dashed dashed;
            border-color: #fff transparent transparent;
        }
    </style>
</head>
<body>
    <div class="search">
        <div class="con"></div>
        <input type="text" placeholder="请输入您的快递单号" class="jd">
    </div>
    <script>
        let text = document.querySelector(".jd");
        let con = document.querySelector(".con");
        // 在文本框中输入字符时就显示上面的大字体,内容为空时则隐藏
        text.addEventListener("keyup",function(){
            if(text.value !== ''){
                con.innerHTML = text.value;
                con.style.display = 'block';
            }else{
                con.style.display = 'none';
            }
        });
        // 文本框失去焦点则隐藏大字体
        text.addEventListener("blur",function(){
            con.style.display = 'none';
        })
        // 文本框获得焦点且内容不为空则显示大字体
        text.addEventListener('focus',function(){
            if(text.value !== ''){
                con.style.display = 'block';
            }
        })
    </script>
</body>
</html>
~~~





### 4.4 使用JS添加行内样式

（1）设置的样式比较少时：

1. element.style.color = '#999' 相当于 

   ~~~html
   <div style="color:#999"></div>
   ~~~

   

2. element.style.backgroundColor   在JS中设置样式时使用的是驼峰命名法

3. element.src

4. element.id

通过click、mouseenter、mouseleave事件控制样式

~~~html
<body>
    <div id="title">111</dic>
    <script>
        let title = document.querySelector("#title");
        title.onmouseenter = function(){
            // 注意1：在JS中设置样式时使用的是驼峰命名法
            // 注意2：使用JS设置样式本质上其实是设置了行内样式，即：直接把设置后的样式添加到标签的style属性上。
            this.style.backgroundColor = "blue";
        }
        title.onmouseleave = function(){
            this.style.backgroundColor = "red";
        }
    </script>
</body>
~~~



（2）设置的样式比较多时：

使用：  element.className = 'a' 相当于设置 class='a'

**注意**： element.className = 'a' 会给class属性进行赋值，会覆盖原来的类名，不会保留原来的类名。

~~~html
    <script>
        let text = document.querySelector("input");
        // 想要保留原本设置好的first类并且添加新的类时：
        text.className = 'first a';
    </script>
~~~

##### 仿新浪注册页面:

~~~html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
    <style>
        div {
            width: 600px;
            margin: 100px auto;
        }
        
        .message {
            display: inline-block;
            font-size: 12px;
            color: #999;
            background: url(images/mess.png) no-repeat left center;
            padding-left: 20px;
        }
        
        .wrong {
            color: red;
            background-image: url(images/wrong.png);
        }
        
        .right {
            color: green;
            background-image: url(images/right.png);
        }
    </style>
</head>

<body>
    <div class="register">
        <input type="password" class="ipt">
        <p class="message">请输入6~16位密码</p>
    </div>
    <script>
        // 首先判断的事件是表单失去焦点 onblur
        // 如果输入正确则提示正确的信息颜色为绿色小图标变化
        // 如果输入不是6到16位，则提示错误信息颜色为红色 小图标变化
        // 因为里面变化样式较多，我们采取className修改样式
        // 1.获取元素
        var ipt = document.querySelector('.ipt');
        var message = document.querySelector('.message');
        //2. 注册事件 失去焦点
        ipt.onblur = function() {
            // 根据表单里面值的长度 ipt.value.length
            if (this.value.length < 6 || this.value.length > 16) {
                // console.log('错误');
                message.className = 'message wrong';
                message.innerHTML = '您输入的位数不对要求6~16位';
            } else {
                message.className = 'message right';
                message.innerHTML = '您输入的正确';
            }
        }
    </script>
</body>

</html>
~~~



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

### 4.5 修改标签的内容

1 innerHTML 识别html标签，不会去除空格，换行，是标准

~~~html
    <script>
        let itemFir = document.querySelector(".itemFirst");
        itemFir.innerHTML = "<a href='http://www.baidu.com'></a>";
        console.log(itemFir.innerHTML); // 结果： <a href="http://www.baidu.com"></a>
    </script>
~~~



2 innerText 不识别html标签，会去除空格，换行，不是标准

3 修改表单元素： 

如：

~~~html
<input type="text">
<script>
        let input = document.querySelector("input");
        input.value = "123"; // 使用value属性进行修改
</script>
~~~





### 4. 4DOM节点分类

1、元素节点（获取元素节点：querySelector;querySelectorAll) nodeType为 1
2、文本节点(innerHTML) nodeType为 3（文本节点包含文字、空格、换行等）
3、属性节点(element.src;element.id)  nodeType为 2

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

#### 4.4.2 增删改查dom节点汇总

**增**：

1. 创建元素节点：document.createElement("li")
2. 创建文本节点：document.createTextNode("111")
3. 创建各种节点：element.innerHTML 
4. 追加节点：domObject.appendChild()
5. 在某个节点之前添加节点：obj.insertBefore(child, 指定插入到哪一个元素的前面)

**删**：父节点.removeChild(子节点)  即： node.parentNode.removeChild(node)

**改**：删除后增加，或者element.innerHTML 覆盖原来的内容

查：无

如：

创建、添加节点

~~~html
<script>
        // 1. 创建节点元素节点
        var li = document.createElement('li');
        // 2. 添加节点 node.appendChild(child)
        var ul = document.querySelector('ul');
        ul.appendChild(li);
        // 3. 添加节点 node.insertBefore(child, 指定插入到哪一个元素的前面);
        var lili = document.createElement('li');
        ul.insertBefore(lili, ul.children[0]);
    </script>
~~~

##### 父子节点：

1 parentNode 

~~~html
	<div data-index="1" id="index"></div>

    <script>
        let index = document.querySelector("#index");
        // 获取最近的父节点(返回元素节点：标签)，找不到则返回null
        console.log(index.parentNode);
    </script>
~~~

2 childNodes 获取所有的子节点存放到集合中进行返回，**包括文本节点** 

~~~html
    <script>
        
        var ul = document. querySelector('ul');
        for(var i = 0; i < ul.childNodes.length;i++) {
            if (ul.childNodes[i].nodeType == 1) {
            // 筛选出元素节点
            console.log(ul.childNodes[i]);
            }
        }
    </script>
~~~

3 children 获取所有的子元素节点存放到集合中进行返回，**不包括文本节点** 

~~~
1 如果想要第一个子元素节点，可以使用 parentNode.chilren[0] 
2 如果想要最后一个子元素节点，可以使用 parentNode.chilren[parentNode.chilren.length - 1]
~~~

##### 兄弟节点：

4  获取兄弟节点

node.nextElementSibling 获取下一个兄弟元素节点

node.previousElementSibling 返回当前元素上一个兄弟元素节点，找不到则返回null。

##### 增删改查节点详情：

增：

- element.innerHTML 
- document.createElement() 

删：tr.parentNode.removeChild(tr);

5 创建、添加节点

**三种动态创建元素区别**

- document.write() 
- element.innerHTML 
- document.createElement() 

区别：

1 document.write("< div>123< /div>")  是直接将内容写入页面的内容流，但是文档流执行完毕后(即html页面从上到下代码执行完毕后，如：点击按钮后执行的代码或者window.onload中页面加载完毕后执行的js代码)执行write代码时，会导致页面全部重绘

 2 innerHTML 是将内容写入某个 DOM 节点，不会导致页面全部重绘 

3 innerHTML 创建多个元素效率更高（不使用拼接字符串的形式 str += "< a>< /a>"的前提下，采取数组形式拼接 arr.push("< a>< /a>")），结构稍微复杂 

4 createElement() 创建多个元素效率稍低一点点，但是结构更清晰 

总结：不同浏览器下，innerHTML 效率要比 creatElement 高

~~~html
<script>
        // 1. 创建元素节点
        var li = document.createElement('li');
        // 2. 添加节点 node.appendChild(child)
        var ul = document.querySelector('ul');
        ul.appendChild(li);
        // 3. 添加节点 node.insertBefore(child, 指定插入到哪一个元素的前面);
        var lili = document.createElement('li');
        ul.insertBefore(lili, ul.children[0]);
    </script>
~~~

案例：添加评论、删除评论

~~~html
<body>
    <textarea name="" id="text" cols="30" rows="10"></textarea>
    <button>发布</button>
    <ul>

    </ul>
    <script>
        let btn = document.querySelector("button");
        let ul = document.querySelector("ul");
        let text = document.querySelector("textarea");
        btn.onclick = function (){
            console.log(text.value);
            if(text.value != ''){
                // 创建li元素
                let li = document.createElement("li");
                // 填充内容
                li.innerHTML = text.value + "<a href='javascript:;'>删除</a>";
                // 添加元素
                ul.insertBefore(li,ul.children[0]);
                
            }else{
                alert("输入不能为空！");
                return;
            }
            let aList = document.querySelectorAll("a");
            for(let i = 0;i<aList.length;i++){
                aList[i].onclick = function() {
                    // 删除节点
                    ul.removeChild(this.parentNode);
                }
            }

        }
    </script>
</body>
~~~



6 克隆节点

node.cloneNode() 方法返回调用该方法的节点的一个副本。 也称为克隆节点/拷贝节点

参数：fasle,true

1如果括号参数为空或者为 false ，则是浅拷贝，即只克隆复制节点本身，不克隆里面的子节点。 

2如果括号参数为 true ，则是深度拷贝，会复制节点本身以及里面所有的子节点

#####  例子(添加水果，点击可以删除)：

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

##### 例子：动态表格，点击可删除

~~~html
<body>
    <table cellspacing="0" border="1">
        <thead>
            <th>姓名</th>
            <th>科目</th>
            <th>成绩</th>
            <th>操作</th>
        </thead>
        <tbody>

        </tbody>
    </table>
    <script>
        // 1.模拟从数据库中查询出来的数据,使用集合中的对象进行存储
        var datas = [{
            name: '魏璎珞',
            subject: 'JavaScript',
            score: 100
        }, {
            name: '弘历',
            subject: 'JavaScript',
            score: 98
        }, {
            name: '傅恒',
            subject: 'JavaScript',
            score: 99
        }, {
            name: '明玉',
            subject: 'JavaScript',
            score: 88
        }, {
            name: '大猪蹄子',
            subject: 'JavaScript',
            score: 0
        }];
        // 展示数据
        let tBody =  document.querySelector("tbody");
        // 添加每一行tr
        for(let i = 0;i<datas.length;i++){
            let tr = document.createElement("tr");
            // 遍历每一个对象,给每一行的每个单元格td填充对象中的内容
            for(let name in datas[i]){
                let td = document.createElement("td");
                td.innerHTML = datas[i][name];
                tr.appendChild(td);
            }
            // 添加删除单元格
            let del = document.createElement("td");
            del.innerHTML = "<a href='javascript:;'>删除</a>"
            tr.appendChild(del);
            tBody.appendChild(tr);
        }
        // 给删除添加点击事件
        let aList = document.querySelectorAll("a");
        for(let i = 0;i<aList.length;i++){
            aList[i].onclick = function(){
                tBody.removeChild(this.parentNode.parentNode);
            }
        }
    </script>
</body>
~~~

##### 例子：js动态生成表格plus

my版本：

~~~html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>js实现动态生成表格</title>
</head>
<body>
    <button id="addBtn" onclick="addRow()">添加</button>
    <table id="myTable" border="1">
        <thead>
            <tr>
                <th>编号</th>
                <th>姓名</th>
                <th>年龄</th>
                <th>操作</th>
            </tr>
        </thead>
        <tbody>
        
        </tbody>
    </table>
    <script>
        let id = 1;
        let tBody = document.getElementById("myTable").getElementsByTagName("tbody")[0];

        function addRow(){
            // 创建tr对象
            let tr = document.createElement("tr");
            let tds = [];
            let count = 4;
            // 创建td对象，使用数组存储
            for(let i = 0;i < count;i++){
                let td = document.createElement("td");
                tds.push(td);
            }
            // 设置td对象的内容
            tds[0].innerHTML = id;
            id++;
            tds[1].innerHTML = "<input type='text'></input>";
            tds[2].innerHTML = "<input type='text'></input>";
            // saveRow(this)中的this指向了 保存button对象
            tds[3].innerHTML = "<button onclick='save(this)'>保存</button>";
            // 添加到表格中
            for(let i = 0;i < tds.length;i++){
                tr.appendChild(tds[i]);
            }
            tBody.appendChild(tr);
        }

        function save(button){
            let tr = button.parentNode.parentNode;
            // tr.cells 返回tr中的td数组
            let tds = tr.cells;
            // 获取输入框中的值
            let nameInput = tds[1].getElementsByTagName("input")[0];
            let name = nameInput.value;
            let ageInput = tds[2].getElementsByTagName("input")[0];
            let age = ageInput.value;
            // 使用值替换输入框
            tds[1].innerHTML = "" + name;
            tds[2].innerHTML = "" + age;
            tds[3].innerHTML = "<button onclick='edit(this)'>编辑</button>" 
            + "<button onclick='remove(this)'>删除</button>"
        }

        function edit(button){
            let tr = button.parentNode.parentNode;
            let tds = tr.cells;
            // 获取td中的值
            let name = tds[1].innerHTML;
            let age = tds[2].innerHTML;
            // 插入输入框并添加原来的值
            tds[1].innerHTML = "<input type='text' value = '" + name + "'></input>";
            tds[2].innerHTML = "<input type='text' value = '" + age + "'></input>";
            // 按钮改为保存
            tds[3].innerHTML = "<button onclick='save(this)'>保存</button>";
        }

        function remove(button){
            // 获取tr对象
            let tr = button.parentNode.parentNode;
            // 删除tr
            tr.parentNode.removeChild(tr);
        }
    </script>
</body>
</html>
~~~

阿伟给的参考答案版本：

~~~html
<!DOCTYPE  html>
<html>
<head>
    <meta  charset="UTF-8">
    <title>动态表格  </title>
</head>

<body>
    <button onclick="addRow()">添加</button>
	<table id="myTable" border="1">
                <thead>
                        <tr>
                                <th>ID  </th>
                                <th>Name  </th>
                                <th>Age  </th>
                                <th>操作  </th>
                        </tr>
                </thead>

                <tbody>
                </tbody>
	</table>
	<script>
	// 获取表格元素
	let  table  = document.getElementById("myTable").getElementsByTagName('tbody')[0];
	// 定义ID用于自增
	let  id  =   1;

	// 添加一行
	function  addRow()  {
                // 使用insertRow（）创建要插入行的tr对象:向索引为0的位置添加一行(tr)并且返回这个tr对象
                let  newRow  =   table.insertRow(table.rows.length);
                // 使用insertCell（）创建tr中的每个td对象
                let  idCell  =   newRow.insertCell(0);// mark1
                let  nameCell  =   newRow.insertCell(1);
                let  ageCell  =   newRow.insertCell(2);
                let  operationCell  =   newRow.insertCell(3); 
                idCell.innerHTML  =   id;
                nameCell.innerHTML  =   '<input  type="text">';
                ageCell.innerHTML  =   '<input  type="text">';
                // saveRow(this)中的this指向了 保存button对象
                operationCell.innerHTML =  '<button  onclick="saveRow(this)">保存</button>';//mark2
                id++;

	}

	// 保存一行
	function  saveRow(button)  {
                // 直接this获得的是window对象 mark
                let test = this;
                // 根据保存按钮获取按钮所在行的tr对象
                let  row  =   button.parentNode.parentNode;
                // 根据tr对象获取里面cells存储的td对象
                let  nameCell  =   row.cells[1];// mark3
                let  ageCell  =   row.cells[2];
                let  operationCell  =   row.cells[3];
                let  nameInput  =   nameCell.getElementsByTagName('input')[0];
                let  ageInput  =   ageCell.getElementsByTagName('input')[0];
                let  name  =   nameInput.value;
                let  age  =   ageInput.value; 
                nameCell.innerHTML  =   name;
                ageCell.innerHTML  =   age;
                operationCell.innerHTML  =
                '<button  onclick="editRow(this)">编辑  </button><button onclick="deleteRow(this)">删除  </button>';
	}

	// 编辑一行 参数button指向了编辑按钮(是一个button对象)
	function  editRow(button)  {
                let  row  =   button.parentNode.parentNode;
                let  nameCell  =   row.cells[1];
                let  ageCell  =   row.cells[2];
                let  operationCell  =   row.cells[3];
                let  name  =   nameCell.innerHTML;
                let  age  =   ageCell.innerHTML;
                nameCell.innerHTML  =   '<input  type="text"  value="'  +   name  +   '">';
                ageCell.innerHTML  =   '<input  type="text"  value="'  +   age  +   '">';
                operationCell.innerHTML  =   '<button  onclick="saveRow(this)">保存</button>';
        }
	// 删除一行
        function  deleteRow(button)  {
                let  row  =   button.parentNode.parentNode;
                row.parentNode.removeChild(row);
        }
	</script>
	</body>
</html>
~~~



### 5 计时器方法



`setInterval` 和 `setTimeout` 都是 JavaScript 中用于定时执行代码的函数，但它们在行为上有一些关键区别：

### 5.0 setTimeout与setInterval的主要区别

1. **执行次数**
   - `setTimeout`: 只执行一次定时器中的代码。
   - `setInterval`: 按固定的时间间隔反复执行代码，直到被清除。
2. **清除机制**
   - `setTimeout`: 如果不再需要，可以通过 `clearTimeout(timeoutId)` 来清除。
   - `setInterval`: 如果不再需要，可以通过 `clearInterval(intervalId)` 来清除。
3. **触发间隔**
   - `setTimeout`: 间隔时间后执行一次。
   - `setInterval`: 间隔时间是任务完成间隔时间，而不是任务开始时间的间隔。

------

### 示例代码

#### 使用 `setTimeout`

以下是一个延迟 2 秒后执行的代码示例：

```javascript
console.log("开始...");
setTimeout(() => {
  console.log("2 秒后执行");
}, 2000);
console.log("结束...");
```

**输出**：

```
开始...
结束...
2 秒后执行
```

------

#### 使用 `setInterval`

以下是每隔 1 秒打印一次的代码示例：

```javascript
let count = 0;
const intervalId = setInterval(() => {
  console.log(`每隔 1 秒执行：第 ${++count} 次`);
  if (count === 5) {
    console.log("清除定时器");
    clearInterval(intervalId); // 清除定时器
  }
}, 1000);
```

**输出**：

```
每隔 1 秒执行：第 1 次
每隔 1 秒执行：第 2 次
每隔 1 秒执行：第 3 次
每隔 1 秒执行：第 4 次
每隔 1 秒执行：第 5 次
清除定时器
```

------

### 应用场景

- **`setTimeout`**: 用于延时执行某些任务。例如：
  - 页面加载后延迟弹出提示框。
  - 某些任务需要在指定时间后执行一次。
- **`setInterval`**: 用于定时重复执行任务。例如：
  - 实现一个时钟，定时更新时间显示。
  - 轮询服务器状态，定时刷新页面内容。

### 注意事项

1. 如果任务执行时间比定时器的间隔时间长，`setInterval` 会有任务堆积问题，可能导致性能问题。
2. 如果需要保证间隔时间严格控制，推荐用 `setTimeout` 实现循环代替 `setInterval`：

```javascript
function repeatTask() {
  console.log("执行任务");
  setTimeout(repeatTask, 1000); // 再次设置延迟
}
repeatTask();
```



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

使用 **typeof**(1),返回number



二、引用数据类型检测：值 **instanceof** 类型，返回boolean

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



#### `async/await` 的工作机制

##### 1） 使用await时，async要加在最近的函数上（包括箭头函数）



##### 2） 调用async函数就要await，保证代码同步执行：

注意：

~~~js
async onLoad(e){
    this.folderId = e.folderId;
    if(e.isOnlyRead == "true"){
        this.isOnlyRead = true;
    }else if(e.isOnlyRead == "false"){
        this.isOnlyRead = false;
    }else{
        this.isOnlyRead = false;
    }
    console.log(`this.allInvoiceMsg = ${JSON.stringify(this.allInvoiceMsg)}`);
    // 这里要使用await，不然代码就不是同步执行的了
    await this.getInvoiceFolderContentAndSet(this.folderId);
    if(this.allInvoiceMsg[0] == undefined){
				
				console.log(`当前文件夹没有内容了！`);
				return;
			}
}
// methods{}中
async getInvoiceFolderContentAndSet(folderId){
				
				let getFolderParam = {
					folderId : folderId
				}
				// 查询发票文件夹的内容 以及 所属的报销类别   (网络请求天然就是异步async的)
				let res = await getInvoicesByFolderId(getFolderParam);
				// console.log(`res = ${JSON.stringify(res)}`);
				let isSuccess = res.body.success;
				let message = res.body.message;
				if(!isSuccess){
					uni.showToast({
						title: message,
						icon: 'error'
					});
					return;
				}
				console.log(`res.body.data = ${JSON.stringify(res.body.data)}`);
				this.allInvoiceMsg = res.body.data;
				this.allInvoiceMsg.sort((a,b) => {
					let aType = a.invoiceType;
					let bType = b.invoiceType;
					if(aType != bType){
						return aType - bType;
					}
					return b.totalMoney - a.totalMoney;
				});
				this.allInvoiceForShow = this.allInvoiceMsg;
				store.setAllInvoiceMsg(this.allInvoiceMsg);
			}
~~~



#### uni.showLoading 一闪而过问题



改动前：

~~~js
			takePhoto() {
				let that = this;
				
				// #ifdef H5
				uni.chooseImage({
					count: 100,
					sizeType: ['original', 'compressed'], //可以指定是原图还是压缩图，默认二者都有
					camera: 'back',
					// 设置后的效果为：默认展示全部的(*.*)文件
					extension : ['*.pdf','*.png','*.jpg'],
					success: async function(res) {
						uni.showLoading({ // 一闪而过
							mask:true,
							title:'文件上传中',
						});
						console.log(`文件上传中`);
						for (var i = 0; i < res.tempFilePaths.length; i++) {
							try{
								await that.uploadImage(i,res);
							}catch(e){
								console.error(`第 ${i + 1} 个文件上传失败: ${e.message}`);
								console.log("takePhoto()::uni.chooseImage失败!========"+e);
							}
						};
						console.log(`文件上传完成`);
						uni.hideLoading();
					},
					fail: (err) => {
						console.log(err);
					}
				
				});
				// #endif
            }

// uploadImage的代码实现是这样子的：			
uploadImage(index,res){
				let that = this;
				const adminToken = "Bearer " + uni.getStorageSync("auth").token;
				// uni.showLoading({
				// 	mask:true,
				// 	title:'文件上传中',
				// });
				uni.uploadFile({
				    url: utils.uriToUrl("/infra-service/files/list"), //服务器接口地址
					
					// #ifdef H5
				    filePath: res.tempFilePaths[index], //要上传的文件路径
					// #endif
					
					// #ifdef MP
					filePath: res.filePaths[index], //要上传的文件路径
					// #endif
					
				    name: 'file',
					header:{
						Authorization:adminToken,
					},
				    formData: { //上传的其他参数
				        docType: "发票附件-图片",
						
						// #ifdef H5
				        docName: res.tempFiles[index].name,
						// #endif
						
						// #ifdef MP
						docName: res.files[index].path,
						// #endif
						
				        upLoaderId: uni.getStorageSync("auth").user.id,
				        access: '',
						// 生成策略
						thumbnailStrategy : 'allPages',
						
				    },
				    success: async function (uploadRes) {
						if (uploadRes!=null && uploadRes!="" && !(uploadRes instanceof Array)) {
							uni.hideLoading();
							let data = JSON.parse(uploadRes.data);
							// 只支持上传图片与pdf格式的附件
							if(!data.mimeType.includes('image') && !data.mimeType.includes('pdf')){
								uni.hideLoading();
								uni.showModal({
									title:'文件格式不支持',
									content:'只支持上传图片格式与pdf格式的附件',
									showCancel:false,
								})
								// 删除不支持的文件
								await deleteIllegalFile(data.id);
								return;
							}
							// 默认使用缩略图
							let resUrl = data.thumbnail;
							// 根据大小（460KB） 选择使用原图还是缩略图
							if(data.bytes <= 460 * 1024){
								resUrl = data.url;
							}
							// pdf格式则一定使用缩略图
							if(data.mimeType == 'application/pdf'){
								resUrl = data.thumbnail;
							}
							let attachmentObj = {
								id : data.id,
								fileName : data.docName,
								url : resUrl,
							};
							that.covertInvoiceEnclosureUrl(attachmentObj);
							// 对附件根据文件名称进行排序
							that.invoiceAttachmentList.sort((a, b) => a.fileName.localeCompare(b.fileName));
							// uni.hideLoading();
						} else {
							uni.showToast({
							    title: '图片uploadRes为空!',
							    icon: 'none',
								duration:700
							});
						}
				    },
				    fail: function (e) {
						// uni.hideLoading();
						console.log(e);
				        uni.showToast({
				            title: '上传失败，请联系管理员',
				            icon: 'none',
							duration:700
				        });
				    }
				});
			},
~~~



从提供的代码来看，`uploadImage` 中的问题主要在于它并没有返回一个 `Promise`，因此调用该方法时，`await` 不会起到阻塞效果，导致 `uni.hideLoading()` 提示框提前关闭。

以下是改进的 `uploadImage` 方法代码：

### 改进后的 `uploadImage`

将 `uni.uploadFile` 包装为一个 `Promise`，以便在调用时能够正确使用 `await`：

```javascript
uploadImage(index, res) {
    let that = this;
    const adminToken = "Bearer " + uni.getStorageSync("auth").token;

    return new Promise((resolve, reject) => {
        uni.uploadFile({
            url: utils.uriToUrl("/infra-service/files/list"), // 服务器接口地址
            
            // #ifdef H5
            filePath: res.tempFilePaths[index], // H5 要上传的文件路径
            // #endif
            
            // #ifdef MP
            filePath: res.filePaths[index], // 小程序要上传的文件路径
            // #endif
            
            name: 'file',
            header: {
                Authorization: adminToken,
            },
            formData: { // 上传的其他参数
                docType: "发票附件-图片",
                
                // #ifdef H5
                docName: res.tempFiles[index].name,
                // #endif
                
                // #ifdef MP
                docName: res.files[index].path,
                // #endif
                
                upLoaderId: uni.getStorageSync("auth").user.id,
                access: '',
                thumbnailStrategy: 'allPages',
            },
            success: async function(uploadRes) {
                try {
                    if (uploadRes && uploadRes.data) {
                        let data = JSON.parse(uploadRes.data);

                        // 检查文件格式
                        if (!data.mimeType.includes('image') && !data.mimeType.includes('pdf')) {
                            await deleteIllegalFile(data.id); // 删除不支持的文件
                            uni.showModal({
                                title: '文件格式不支持',
                                content: '只支持上传图片格式与pdf格式的附件',
                                showCancel: false,
                            });
                            reject(new Error('Unsupported file format.'));
                            return;
                        }

                        // 根据逻辑处理 URL
                        let resUrl = data.thumbnail;
                        if (data.bytes <= 460 * 1024) {
                            resUrl = data.url;
                        }
                        if (data.mimeType === 'application/pdf') {
                            resUrl = data.thumbnail;
                        }

                        // 构造附件对象
                        let attachmentObj = {
                            id: data.id,
                            fileName: data.docName,
                            url: resUrl,
                        };
                        that.covertInvoiceEnclosureUrl(attachmentObj);
                        that.invoiceAttachmentList.sort((a, b) => a.fileName.localeCompare(b.fileName));
                        resolve(attachmentObj); // 成功时 resolve 附件信息
                    } else {
                        uni.showToast({
                            title: '上传结果为空!',
                            icon: 'none',
                            duration: 700,
                        });
                        reject(new Error('Empty upload result.'));
                    }
                } catch (err) {
                    console.error('文件上传处理失败:', err);
                    reject(err);
                }
            },
            fail: function(err) {
                console.error('文件上传失败:', err);
                uni.showToast({
                    title: '上传失败，请联系管理员',
                    icon: 'none',
                    duration: 700,
                });
                reject(err); // 上传失败时 reject
            },
        });
    });
}
```

### 改动说明

1. **包装 `uni.uploadFile` 为 `Promise`**
   - 使用 `new Promise`，并在成功时调用 `resolve`，在失败或异常时调用 `reject`。
   - 这样可以确保 `await` 能正确阻塞，等待上传完成。
2. **处理文件格式检查逻辑**
   - 检查文件格式，如果格式不符合要求，删除文件并弹窗提示，同时调用 `reject` 以便跳过错误文件。
3. **明确的错误处理**
   - 在上传失败或处理逻辑出错时，清晰地抛出错误，便于上层调用捕获问题。
4. **返回上传结果**
   - 成功时通过 `resolve` 返回上传结果，供调用者进一步使用。

### 调用改进后的 `uploadImage`

你在主逻辑中可以这样调用：

```javascript
for (var i = 0; i < res.tempFilePaths.length; i++) {
    try {
        await that.uploadImage(i, res); // 等待每个文件上传完成
    } catch (e) {
        console.error(`第 ${i + 1} 个文件上传失败: ${e.message}`);
    }
}
```

改进后，文件上传完成后，`uni.hideLoading()` 才会被调用，从而解决提示“一闪而过”的问题。





`resolve` 和 `reject` 是用于创建和控制 JavaScript 中 **Promise** 的两个核心函数，它们分别代表**完成（fulfilled）**和**拒绝（rejected）**的状态。

当你需要处理异步操作（如网络请求、文件上传等）时，`Promise` 是一种可以更好地组织代码并确保异步逻辑流畅的机制。以下是它们的作用和为什么要使用它们的解释：

------

### 1. **什么是 `resolve` 和 `reject`？**

#### `resolve(value)`

- **作用**：表示异步操作成功，并将结果（`value`）传递给后续处理函数（`.then()`）。
- **用法**：当异步操作完成且没有错误时调用。

#### `reject(reason)`

- **作用**：表示异步操作失败，并将错误原因（`reason`）传递给后续的错误处理函数（`.catch()`）。
- **用法**：当异步操作出现错误或需要中止时调用。

------

### 2. **为什么要用 `resolve` 和 `reject`？**

在没有 `Promise` 的情况下，你需要通过回调函数来处理异步操作的成功或失败，但回调函数可能会导致 **回调地狱** 或 **代码混乱** 的问题。

使用 `resolve` 和 `reject` 能让你：

1. **更易读的代码结构**：通过 `.then()` 和 `.catch()` 统一管理成功和失败的处理逻辑。
2. **清晰的错误处理**：`Promise` 会捕获异步操作中的错误，你可以统一用 `.catch()` 来处理。
3. **支持 `await` 语法**：`Promise` 是异步处理的基础，配合 `async/await` 让代码逻辑更接近同步执行的写法。

------

### 3. **如何使用 `resolve` 和 `reject`？**

以下是一个创建 `Promise` 的简单示例：

#### 示例 1：手动创建一个 `Promise`

```javascript
function asyncOperation(success) {
    return new Promise((resolve, reject) => {
        setTimeout(() => {
            if (success) {
                resolve("操作成功！");
            } else {
                reject("操作失败！");
            }
        }, 1000); // 模拟 1 秒延迟
    });
}

// 调用
asyncOperation(true)
    .then((result) => {
        console.log(result); // 输出 "操作成功！"
    })
    .catch((error) => {
        console.error(error); // 如果失败，会输出 "操作失败！"
    });
```

#### 示例 2：配合 `async/await`

```javascript
async function run() {
    try {
        const result = await asyncOperation(true); // 等待操作完成
        console.log(result); // 输出 "操作成功！"
    } catch (error) {
        console.error(error); // 如果失败，会输出 "操作失败！"
    }
}

run();
```

------

### 4. **你的场景中为什么需要 `resolve` 和 `reject`？**

在你的代码中，`uni.uploadFile` 是一个异步操作（文件上传），它不会直接返回一个 `Promise`，而是依赖回调函数（`success` 和 `fail`）来处理上传结果。为了让你能够在调用 `uploadImage` 时使用 `await` 或链式调用 `.then()` 和 `.catch()`，你需要用 `Promise` 包装它。

- **`resolve` 的作用**：当文件上传成功时，将结果返回给调用者。
- **`reject` 的作用**：当文件上传失败或出现问题时，将错误信息传递给调用者。

------

### 5. **你的代码中的 `resolve` 和 `reject` 用法**

#### 改进后的代码

```javascript
uploadImage(index, res) {
    return new Promise((resolve, reject) => {
        uni.uploadFile({
            url: utils.uriToUrl("/infra-service/files/list"), // 上传接口
            filePath: res.tempFilePaths[index], // 上传文件路径
            name: 'file',
            success: function(uploadRes) {
                try {
                    let data = JSON.parse(uploadRes.data);
                    resolve(data); // 成功时返回数据
                } catch (err) {
                    reject(new Error('解析上传结果失败')); // JSON 解析失败
                }
            },
            fail: function(err) {
                reject(err); // 上传失败
            },
        });
    });
}
```

#### 调用时的代码

```javascript
try {
    const uploadResult = await uploadImage(0, res); // 等待上传完成
    console.log("上传成功：", uploadResult);
} catch (error) {
    console.error("上传失败：", error);
}
```

------

### 6. **总结**

- **`resolve`**：用来返回异步操作的成功结果。
- **`reject`**：用来返回异步操作的错误信息。
- **好处**：统一异步逻辑管理，支持 `async/await` 写法，更清晰的错误处理机制。

通过 `resolve` 和 `reject` 包装异步回调，可以让你的代码更加简洁和易于维护。





# 11 JavaScript Promise 核心用法笔记



## TypeScript 异步编程要点

### 1. **async/await 必须配对使用**
- 调用 `async` 函数时，必须使用 `await` 等待 Promise 解析
- 不使用 `await` 会得到 `Promise` 对象，而不是实际值

```typescript
// ❌ 错误：返回 Promise 对象
personInfo = realToFaceIdentify(dialogObj)  // 类型是 Promise<PersonInfoConfig | null>

// ✅ 正确：等待 Promise 解析
personInfo = await realToFaceIdentify(dialogObj)  // 类型是 PersonInfoConfig | null
```

### 2. **类型声明要匹配实际值**
- 如果变量可能为 `null`，类型声明必须包含 `| null`
- 否则 TypeScript 会报类型不匹配错误

```typescript
// ❌ 错误：类型不匹配
let personInfo: PersonInfoConfig = null;

// ✅ 正确：允许 null 值
let personInfo: PersonInfoConfig | null = null;
```

### 3. **Promise 类型理解**
- `async` 函数返回 `Promise<T>`
- `await` 会解包 Promise，得到 `T` 类型
- 不使用 `await` 时，变量类型是 `Promise<T>`，不是 `T`

**记忆口诀**：异步函数调用要 `await`，类型声明要包含 `null`。



`Promise` 是 JavaScript 中处理异步操作的标准方式，它将复杂的异步代码（如回调函数）转化为更易读、更易维护的线性逻辑。

### 1. 什么是 Promise？

可以把 `Promise` 理解为一个**“承诺”**或一个**“异步操作的容器”**。它代表一个尚未完成但最终会完成的操作。这个承诺最终会有两种结果：

*   **成功 (Fulfilled / Resolved)**: 操作成功完成。
*   **失败 (Rejected)**: 操作因某种原因失败。

一个 `Promise` 对象在其生命周期中会经历以下三种状态：
1.  **`pending` (进行中)**: 初始状态，既未成功也未失败。
2.  **`fulfilled` (已成功)**: 操作成功完成。
3.  **`rejected` (已失败)**: 操作失败。

### 2. `resolve` 与 `reject`：改变承诺状态

在 `new Promise` 的执行函数中，你会得到两个参数：`resolve` 和 `reject`。它们是两个函数，用来手动改变 Promise 的状态。

*   **`resolve(value)`**:
    *   将 Promise 的状态从 `pending` 变为 `fulfilled`。
    *   将 `value` 作为成功的结果传递出去。

*   **`reject(error)`**:
    *   将 Promise 的状态从 `pending` 变为 `rejected`。
    *   将 `error` 作为失败的原因传递出去。

**关键点：`resolve()` 和 `reject()` 不会中断代码执行！**

调用这两个函数仅仅是改变了 Promise 的状态。它们**不会**像 `return` 语句一样终止当前函数的执行。因此，**最佳实践**是在调用它们之后，如果后面没有其他逻辑，应立即使用 `return` 来明确地结束函数。

```javascript
// 推荐写法
if (isSuccess) {
    return resolve("成功了");
} else {
    return reject(new Error("失败了"));
}
```

### 3. 如何“消费”一个 Promise？ `async/await`

`async/await` 是消费 Promise 的现代化、推荐语法。

*   在一个 `async` 函数内部，你可以使用 `await` 关键字来“等待”一个 Promise 的结果。
*   `await` 会暂停函数的执行，直到 Promise 变为 `fulfilled` 或 `rejected`。

### 4. 错误处理：`try...catch` 与 `reject` 的关系

这是 `Promise` 错误处理的核心机制。

*   **`reject` 的作用**: `reject(error)` 本身并不会在页面上显示任何错误。它的唯一作用是**“抛出”一个异步错误**，将 Promise 的状态标记为失败。

*   **“未捕获的 Promise Rejection”**: 如果一个 Promise 被 `reject` 了，而你的代码没有任何机制去处理这个失败，浏览器为了帮助调试，就会在控制台打印一个红色的错误。

*   **`try...catch` 的作用**: 这是捕获并处理被 `reject` 的 Promise 的标准方法。
    *   将 `await` 表达式包裹在 `try` 块中。
    *   如果 `await` 等待的 Promise 成功 (`resolve`)，代码继续在 `try` 块中执行。
    *   如果 `await` 等待的 Promise 失败 (`reject`)，`await` 表达式会**抛出一个异常**，代码执行会立即跳转到 `catch(error)` 块。

**示例：**

```javascript
async function doSomethingAsync() {
    try {
        // 'await' 等待 Promise 的结果
        let result = await someFunctionThatReturnsAPromise();
        console.log("成功:", result);

    } catch (error) {
        // 如果 Promise 被 reject，'error' 参数会接收 reject(value) 中的 value
        // 在这里，我们可以自定义如何向用户展示错误
        console.error("捕获到错误:", error.message);
        showFriendlyErrorToUser("操作失败，请稍后重试。");
    }
}
```

### 5. `reject` 传递什么？

*   你可以 `reject` 任何值（字符串、数字、对象）。
*   **强烈推荐**的做法是 `reject(new Error("错误信息"))`。
    *   **标准化**: 这是 JavaScript 的标准约定。
    *   **包含堆栈信息**: `Error` 对象（`error.stack`）包含了错误发生时的函数调用堆栈，这对于调试至关重要。
    *   在 `catch(error)` 块中，你可以通过 `error.message` 来获取你设置的“错误信息”字符串。



# 12 回调风格转换为 Promise 风格

~~~js
// 辅助函数：将 QWebChannel 的回调风格转换为 Promise 风格
const callQtAsync = (method, ...args) => {
  return new Promise(resolve => {
    // QWebChannel 的方法，最后一个参数是回调函数
    method(...args, resolve);
  });
};
// 初始化 QWebChannel（等待页面加载完成后执行）
window.addEventListener('load', async () => {
  // Qt 会在 window 上注入 qt.webChannelTransport 对象
  if (window.qt && window.qt.webChannelTransport) {
    // 获取 C++ 注册的 "myChannel" 对象（需与 C++ 端注册的名称一致）    
    new window.QWebChannel(qt.webChannelTransport, async (channel) => {
      myChannel = channel.objects.myChannel;
      console.log('QWebChannel init ok!!');

      // 将通信对象挂载到 Vue 全局属性
      app.config.globalProperties.$myChannel = myChannel;
      window.$myChannel = myChannel;

      // 生产环境配置加载和覆盖
      if (myChannel && process.env.NODE_ENV === 'production') {
        try {
          // 需要从 C++ 端加载的配置字段列表
          const configFieldsToLoad = [
            'MasterServerUrls', 'FaceServiceUrls', 'CardMachineServiceUrls', 
            'RfidServiceUrls', 'AudioServiceUrls', 'FaceSysRtspStream',
            'HttpReqDelay', 'RecentShowRecordNum', 'IsUseLocalAudio', 
            'AudioDevID', 'DispatchSyncAudioDevID', 'SysAuthorType',
            'IsCardBandPerson', 'IsCfgCardMachine', 'AuthorFequancy',
            'AuthorTime', 'DefaultAuthorType', 'IsSizePermission',
            'IsLineType', 'IsMultipleWorkshop', 'IsAuthorType',
            'Logging', 'LedgerPageParm', 'InterLockConditionList', 'AppInfo', 'LocalLogConfig'
          ];

          for (const fieldName of configFieldsToLoad) {
            // 使用 callQtAsync 包装 Promise
            let val = await callQtAsync(myChannel.getConfigByField, fieldName);
            let parsedVal = val;

            // 尝试解析 JSON 字符串（处理C++返回的带引号字符串或复杂对象）
            try {
              // 尝试解析 JSON，如果失败则使用原始值
              const tempParsedVal = JSON.parse(val);
              // 确保解析结果不是 null 并且是对象或数组
              if (tempParsedVal !== null && (typeof tempParsedVal === 'object' || Array.isArray(tempParsedVal))) {
                parsedVal = tempParsedVal;
              } else if (typeof tempParsedVal === 'string' && val.startsWith('"') && val.endsWith('"')) {
                // 如果是包裹在双引号里的字符串，就取其值
                parsedVal = tempParsedVal;
              }
            } catch (e) {
              // 如果 JSON.parse 失败，或者 C++ 返回的是普通字符串（非 JSON 格式），则保留原始 val
              console.warn(`Failed to parse config field ${fieldName} as JSON, using original value:`, e);
            }

            // 直接覆盖 BUSINESS_CONFIG 中的同名字段
            BUSINESS_CONFIG[fieldName] = parsedVal;
            console.log(`Config ${fieldName} loaded and applied:`, parsedVal);
          }
          // 使用确认框显示最终配置
          ElMessageBox.confirm('所有生产环境配置加载完成并已应用。最终配置：' + JSON.stringify(BUSINESS_CONFIG,null,4), '提示', {
            confirmButtonText: '确认',
            cancelButtonText: '取消',
            type: 'warning',
          }).then(() => {

          }).catch(() => {});
          console.log('所有生产环境配置加载完成并已应用。resultConfig = ：', JSON.stringify(BUSINESS_CONFIG,null,4));
        } catch (error) {
          ElMessageBox.confirm('加载生产环境配置失败: error.message = ' + error.message + ' error.stack = ' + error.stack, '提示', {
            confirmButtonText: '确认',
            cancelButtonText: '取消',
            type: 'warning',
          }).then(() => {

          }).catch(() => {});

          ElMessageBox.confirm('加载生产环境配置失败: JSON.stringify(error,null,4) =' + JSON.stringify(error,null,4), '提示', {
            confirmButtonText: '确认',
            cancelButtonText: '取消',
            type: 'warning',
          }).then(() => {

          }).catch(() => {});

        }
      }

      // 示例：监听 C++ 发送的信号（全局监听，也可在组件内单独监听）
      myChannel.cppSignalToJs.connect((data) => {
        console.log('C++ Send Signals:' + data.toString());
        // 可在此处触发 Vue 全局事件，供组件监听
        //app.config.globalProperties.$emit('cpp-signal', data);
      });
    });
  } else {
    console.error('QWebChannel init failed: cannot find qt.webChannelTransport');
  }
});
~~~

~~~
let val = await callQtAsync(myChannel.getConfigByField, fieldName);到return new Promise，函数是怎么执行的，流程是怎么样的，“myChannel.getConfigByField”这样子写的话，没有括号就没有开始调用，那callQtAsync函数只是返回了一个Promise对象，然后被await解析，这个解析一定是成功的，然后再执行“method(...args, resolve);”真正的使用括号执行“myChannel.getConfigByField”函数获取值吗，那这个时候回调函数resolve是什么
~~~





好的，这是一个非常深入且关键的问题，涉及到 JavaScript 变量、函数引用、Promise 以及 `await` 的工作机制。让我们一步步来梳理 `let val = await callQtAsync(myChannel.getConfigByField, fieldName);` 到 `return new Promise(...)` 的完整执行流程。

### 1. `let val = await callQtAsync(myChannel.getConfigByField, fieldName);`

**第一步：`callQtAsync(myChannel.getConfigByField, fieldName)` 被调用**

*   **`myChannel.getConfigByField`**: 这里没有括号，所以它不是立即调用 `getConfigByField` 函数，而是将 `getConfigByField` 函数本身作为一个**引用（reference）**传递给 `callQtAsync`。
    *   `myChannel` 是一个对象。
    *   `myChannel.getConfigByField` 是这个对象上的一个方法。当你不带括号地引用一个方法时，你得到的是该方法的函数体本身，它仍然知道它所属的 `myChannel` 对象（通过闭包或 `this` 绑定机制）。
*   **`fieldName`**: 这是一个字符串变量，包含要查询的配置字段名。这个字符串的值被作为普通参数传递。

所以，在这一步，`callQtAsync` 接收到：
*   `method` = `myChannel.getConfigByField` (一个函数的引用)
*   `args` = `['MasterServerUrls']` (一个包含字段名的数组，如果 `fieldName` 是 `'MasterServerUrls'`)

### 2. 进入 `callQtAsync` 函数内部

```javascript
const callQtAsync = (method, ...args) => {
  return new Promise(resolve => { // (A) Promise 构造函数开始执行
    // (B) Promise 的执行器函数被立即执行
    // (C) method(...args, resolve); 将在这里被调用
  });
};
```

*   **`(A) `new Promise(resolve => { ... })`**:
    *   JavaScript 运行时立即创建一个新的 `Promise` 对象。
    *   `Promise` 构造函数接收一个执行器函数，这个执行器函数会被**同步立即执行**。
    *   在执行器函数内部，`resolve` 是由 Promise 构造函数提供的一个特殊函数。它的作用是：当调用 `resolve(value)` 时，它会改变这个 Promise 的状态为“已解决 (fulfilled)”，并将 `value` 作为最终结果传递给所有等待这个 Promise 的 `then()` 回调或 `await` 表达式。
*   **`(B) Promise 的执行器函数被立即执行`**:
    *   此时，`resolve` 函数被捕获到执行器函数的作用域中。
*   **`(C) `method(...args, resolve);` 开始执行**：
    *   这里的 `method` 是我们从外面传进来的 `myChannel.getConfigByField` 函数引用。
    *   `...args` 展开为 `'MasterServerUrls'`。
    *   **`resolve` (Promise 的 `resolve` 函数)** 被作为**最后一个参数**传递给 `myChannel.getConfigByField`。

### 3. `myChannel.getConfigByField('MasterServerUrls', resolve)` 被“真正的”调用

现在，`myChannel.getConfigByField` 方法被执行了，它的参数是：
*   `'MasterServerUrls'` (这是 `fieldName` 的值)
*   `resolve` (这是 Promise 的 `resolve` 函数，现在它充当了 Qt WebChannel 的回调函数)

**在这个时刻，`myChannel.getConfigByField` 函数的 JavaScript 代理代码开始与底层 Qt WebChannel 进行交互：**

*   Qt WebChannel 将 `'MasterServerUrls'` 参数传递给 C++ 端的 `MyChannel::getConfigByField` 方法。
*   Qt WebChannel 同时记录下这个特殊的 `resolve` 函数，它知道当 C++ 方法有返回值时，应该调用这个 `resolve` 函数来传递结果。

**此时，`myChannel.getConfigByField` 函数本身**（在 JavaScript 代理层）**已经完成了它的同步执行部分。**它已经把调用请求和回调函数都“发送”给了 Qt WebChannel 的异步机制。

### 4. `callQtAsync` 函数返回 Promise 对象

因为 `Promise` 的执行器函数（包括 `method(...args, resolve);` 这一行）是同步执行的，所以 `new Promise(...)` 表达式会立即创建一个 `Promise` 对象并返回。

所以，`let val = await callQtAsync(myChannel.getConfigByField, fieldName);` 这行代码的同步部分到此结束，`await` 关键字现在**接收到了这个新创建的 `Promise` 对象**。

### 5. `await` 开始“等待”

*   当 `await` 关键字看到它操作数是一个 `Promise` 对象时，它会暂停当前 `async` 函数（`window.addEventListener` 的回调）的执行。
*   `await` 会一直等待这个 `Promise` 对象的状态变为“已解决 (fulfilled)”或“已拒绝 (rejected)”。

### 6. C++ 端执行，Qt WebChannel 调用回调

*   在后台，C++ 端的 `MyChannel::getConfigByField` 方法正在执行，并最终返回一个 `QString` 值（例如 `\"http://fake.masterserver:5000\"`）。
*   Qt WebChannel 收到 C++ 的返回值。
*   Qt WebChannel 发现之前它记录了 `resolve` 作为回调函数。
*   **Qt WebChannel 异步地调用这个 `resolve` 函数，并将 C++ 的返回值作为参数传递给它：`resolve("\"http://fake.masterserver:5000\"")`**。

### 7. Promise 被解决，`await` 继续执行

*   当 `resolve` 函数被调用时，它会将之前创建的 `Promise` 对象的状态从“待定 (pending)”变为“已解决 (fulfilled)”，并将 `\"http://fake.masterserver:5000\"` 作为解决值。
*   此时，`await` 关键字“感知”到它等待的 `Promise` 已经解决。
*   `await` 从 `Promise` 中提取出解决值 (`\"http://fake.masterserver:5000\"`)。
*   当前 `async` 函数的执行从暂停处继续。
*   `let val` 被赋值为 `\"http://fake.masterserver:5000\"`。

### 总结

整个流程的关键在于：

*   `myChannel.getConfigByField` 在 `callQtAsync` 内部被调用，但它的**实际工作（与 C++ 通信并等待结果）是异步的**。
*   `callQtAsync` 通过 `new Promise(...)` 提供了一个 `resolve` 函数，这个 `resolve` 函数被巧妙地用作了 Qt WebChannel 的 JavaScript 回调函数。
*   `await` 关键字等待的是 `callQtAsync` 返回的 `Promise` 对象，而不是 `myChannel.getConfigByField` 的同步返回值。
*   真正的返回值是在 C++ 执行完毕后，通过 Qt WebChannel 调用 `resolve` 函数，才传递回 JavaScript，并最终被 `await` 捕获。

这个机制非常强大，它将一个低级别的回调模式抽象成了高级的 Promise/async/await 模式，使得异步代码的编写和阅读变得更加直观。

**补充：**

- return new Promise(resolve => { ... });:

- 这是 callQtAsync 的核心。它返回一个新的 Promise 对象。

- Promise 构造函数接收一个执行器函数 (resolve, reject) => { ... }。在这里，我们只需要 resolve，因为 Qt WebChannel 的成功回调直接给出结果，没有标准的错误回调机制（错误通常通过 try-catch 或外部逻辑处理）。

- resolve 函数是一个回调，当异步操作成功完成时，您会调用 resolve(value) 来传递结果。



## 13 使用Promise 实现统一初始化承诺流程

好的，我来详细解释一下“统一初始化承诺”这个流程，以及 `new Promise((resolve) => {})` 和 `await window.__APP_INIT__` 在其中的作用。

### 统一初始化承诺流程解释

这个方案的核心思想是：创建一个全局的、表示“应用是否已完成基础初始化”的信号。所有依赖于这些基础初始化的组件，在它们自己的初始化过程中，都必须等待这个信号发出后才能继续执行。

这样做的目的是解决一个常见的异步编程问题：**竞态条件（Race Condition）**。在你的场景中，`main.js` 负责加载 C++ 配置和初始化服务（这是一个异步过程），而 `SafetyLockInterface.vue` 的 `onMounted` 钩子也同时在执行（可能比 `main.js` 的配置加载更快），导致组件尝试访问尚未加载完成的配置数据，从而报错。通过“统一初始化承诺”，我们确保 `SafetyLockInterface.vue` 总是等到 `main.js` 的所有基础初始化完成后再开始工作。

### `new Promise((resolve) => {})` 的使用 (`main.js`)

在 `main.js` 中，我们添加了如下代码：

```javascript
let appInitResolver = null;
window.__APP_INIT__ = new Promise((resolve) => {
  appInitResolver = resolve;
});
```

1.  **`new Promise((resolve) => {})`**：
    *   **Promise 是什么？** 在 JavaScript 中，`Promise` 对象代表一个异步操作的最终完成（或失败）及其结果值。它是一个“未来会有一个结果”的占位符。
    *   **`resolve` 函数**：当你创建一个 `Promise` 时，会传入一个执行器函数，这个函数会接收两个参数：`resolve` 和 `reject`。
        *   `resolve` 函数用于将 Promise 的状态从“待定”（pending）改为“已完成”（fulfilled），并可以传递一个值作为结果。
        *   `reject` 函数用于将 Promise 的状态从“待定”改为“已拒绝”（rejected），并可以传递一个错误信息。
    *   **`appInitResolver = resolve;` 的目的**：在这里，我们没有立即调用 `resolve`。而是将这个 `resolve` 函数赋值给了一个全局变量 `appInitResolver`。这样做的目的是，我们可以在 `Promise` 创建之后，但在 `main.js` 中的其他异步操作（如 `loadConfigByCpp` 和 `initServices`）完成之后，**手动地**调用 `appInitResolver()` 来通知所有等待这个 Promise 的地方：“应用的基础初始化已经完成了！”

2.  **`window.__APP_INIT__ = ...`**：
    *   我们将这个新创建的 Promise 赋值给了 `window` 对象下的一个属性 `__APP_INIT__`。
    *   `window` 对象是浏览器环境中的全局对象，任何在全局作用域中定义的变量和函数，以及挂载到 `window` 上的属性，都可以在应用程序的任何地方被访问到。
    *   所以，`window.__APP_INIT__` 就成为了一个全局可访问的“应用初始化完成”的信号。

**`main.js` 中的完整流程：**

```javascript
// ... 其他代码 ...

// 创建全局初始化 Promise，并保存其 resolve 函数
let appInitResolver = null;
window.__APP_INIT__ = new Promise((resolve) => {
  appInitResolver = resolve;
});

window.addEventListener('load', async() => {
  try {
    if (window.qt && window.qt.webChannelTransport) {
      new window.QWebChannel(qt.webChannelTransport, async (channel) => {
        // ... QWebChannel 初始化 ...

        if (myChannel && process.env.NODE_ENV === 'production') {
          await loadConfigByCpp(myChannel); // 1. 加载配置（异步）
        }

        await initServices(); // 2. 初始化服务（异步）

        // 3. 所有基础初始化完成后，调用 resolve 函数
        appInitResolver(); // --> 此时，window.__APP_INIT__ 状态变为“已完成”
      });
    } else {
      // 开发环境
      if (process.env.NODE_ENV == 'development') {
        await initServices(); // 初始化服务
        appInitResolver(); // --> 此时，window.__APP_INIT__ 状态变为“已完成”
      }
    }
  } catch (error) {
    console.error('应用初始化过程中发生错误:', error);
    appInitResolver(); // 即使出错也要 resolve，避免组件无限等待
  }
});

// ... 其他代码 ...
```
这段代码确保了在 `loadConfigByCpp` 和 `initServices` 这两个关键的异步操作都完成之后，才会调用 `appInitResolver()`。

### `await window.__APP_INIT__` 的使用 (`SafetyLockInterface.vue`)

在 `SafetyLockInterface.vue` 的 `onMounted` 钩子中，我们添加了如下代码：

```javascript
onMounted(async () => {
  // ... 其他代码 ...

  // 等待应用初始化完成（配置加载和服务初始化）
  console.log('组件等待应用初始化完成...')
  await window.__APP_INIT__ // <-- 在这里等待
  console.log('应用初始化完成，开始执行需要配置的操作')
  
  // 获取股道数据
  fetchTrackData()
  
  // 初始化WebSocket连接
  initWebSocket()

  // ... SysLnkThread 相关的初始化逻辑 ...
})
```

1.  **`async` 关键字**：`onMounted` 函数被标记为 `async`，这使得我们可以在函数内部使用 `await` 关键字。
2.  **`await` 关键字**：
    *   `await` 只能在 `async` 函数中使用。
    *   当 `await` 后面跟着一个 `Promise` 对象时，它会**暂停** `async` 函数的执行，直到那个 `Promise` 的状态变为“已完成”（fulfilled）或“已拒绝”（rejected）。
    *   如果 Promise 成功完成，`await` 会返回 Promise 解析后的值，然后 `async` 函数从暂停的地方继续执行。
    *   如果 Promise 被拒绝，`await` 会抛出错误，你可以用 `try...catch` 来捕获。
3.  **`await window.__APP_INIT__` 的目的**：
    *   当 `SafetyLockInterface.vue` 组件被挂载时，`onMounted` 钩子会被调用。
    *   在执行到 `await window.__APP_INIT__` 这一行时，`onMounted` 函数会暂停。
    *   它会一直等待，直到 `main.js` 中调用的 `appInitResolver()` 被执行，从而使 `window.__APP_INIT__` 这个 Promise 的状态变为“已完成”。
    *   一旦 `window.__APP_INIT__` Promise 完成，`onMounted` 函数就会从 `await` 处恢复执行，然后继续执行下面的代码，例如 `fetchTrackData()`、`initWebSocket()` 和 `sysLnkThread()`。

### 总结整个流程：

1.  **`main.js` 准备信号**：`main.js` 创建一个全局 Promise (`window.__APP_INIT__`)，并保存其 `resolve` 函数。这个 Promise 一开始处于“待定”状态。
2.  **`main.js` 执行初始化**：`main.js` 接着执行加载 C++ 配置 (`loadConfigByCpp`) 和初始化系统服务 (`initServices`) 等所有关键的异步初始化任务。
3.  **`main.js` 发出信号**：当所有这些初始化任务都**成功完成**后，`main.js` 调用之前保存的 `resolve` 函数 (`appInitResolver()`)。这时，`window.__APP_INIT__` 这个 Promise 的状态就从“待定”变成了“已完成”。
4.  **组件等待信号**：在 `SafetyLockInterface.vue` 的 `onMounted` 钩子中，它使用 `await window.__APP_INIT__` 来等待这个全局 Promise 完成。
5.  **组件继续执行**：只有当 `main.js` 发出“初始化完成”的信号后（即 `window.__APP_INIT__` Promise 完成），`SafetyLockInterface.vue` 的 `onMounted` 钩子才会从 `await` 处恢复执行，并安全地开始调用依赖于已加载配置和已初始化服务的函数。

通过这种方式，我们确保了 `SafetyLockInterface.vue` 不会在配置尚未就绪时就尝试使用它们，从而避免了“getAllClassTypeConfig 总控系统URL未配置”的错误。这是一个非常有效且常用的处理应用启动时异步依赖关系的模式。



### 14  Promise.reject对于异常的处理

好的，不写代码，只回答您的问题。这是一个非常好的问题，它触及了 `Promise` 和 `async/await` 结合 `axios` 拦截器工作的核心机制。

您的问题是：为什么 `secondaryLoginRequest.ts` 拦截器里的 `return Promise.reject(...)` 之后，`InstructMsgBox.vue` 里的 `catch` 块还会执行？

答案是：**这正是 `Promise` 链和 `async/await` 语法糖被设计成的工作方式。`return Promise.reject` 的作用不是“终止”错误，而是将错误“传递”下去。**

让我们一步步分解这个流程：

1.  **调用发生 (在 `InstructMsgBox.vue`):**
    您的代码在 `try` 块中执行了 `await secondaryLogin(loginData)`。`await` 关键字会“暂停”函数的执行，等待 `secondaryLogin` 返回的 Promise 解决（resolve）或拒绝（reject）。

2.  **网络请求与失败 (Axios):**
    `secondaryLogin` 函数里的 `axios` 实例发起了网络请求。服务器返回了 `401 Unauthorized` 状态码。对于 `axios` 来说，任何非 2xx 的状态码都会导致它返回的 Promise 进入 **rejected** (拒绝) 状态。

3.  **响应拦截器介入 (在 `secondaryLoginRequest.ts`):**
    因为 Promise 被拒绝了，`axios` 不会触发响应拦截器的成功回调（第一个参数），而是会触发 **错误回调**（第二个参数），也就是您写了 `console.log` 的那个函数。

4.  **拦截器的工作 (关键点):**
    *   您的 `console.log` 被执行，这是您看到的第一个日志。
    *   然后，代码执行了 `return Promise.reject({ status: ..., message: ..., data: ... })`。
    *   **这一步至关重要：** 这个 `return` 语句并没有“处理掉”或“吞掉”这个错误。相反，它做的是：
        a.  对原始的 `error` 对象进行了一次“包装”或“转换”，把它变成了您自定义的 `{ status, message, data }` 格式。
        b.  然后它将这个新的、包装过的错误对象，作为 **拒绝原因**，继续向调用链的上游抛出。
    *   所以，拦截器的错误处理函数返回了一个**新的、处于 rejected 状态的 Promise**。

5.  **`await` 的反应 (回到 `InstructMsgBox.vue`):**
    *   `await` 关键字一直在等待 `secondaryLogin` 函数的 Promise 结果。现在，它等到的结果是——一个被 **拒绝（rejected）** 的 Promise。
    *   `async/await` 的设计就是，当 `await` 等待的 Promise 被拒绝时，它会**立即将拒绝的原因（在这里是您包装过的 `{ status, ... }` 对象）作为异常抛出**。

6.  **`catch` 块捕获:**
    *   因为 `await` 抛出了一个异常，而这个 `await` 语句又恰好在 `try...catch` 块的 `try` 部分。
    *   所以，这个异常被 `catch (error: any)` 块捕获了。
    *   此时，`catch` 块中的 `error` 变量，**其值正是您在拦截器中 `Promise.reject` 的那个自定义对象**。
    *   因此，`console.error('二次确认登录异常:', error)` 被执行，这是您看到的第二个日志。

### 简单比喻：

您可以把这个过程想象成一个“报告问题”的链条。

1.  **一线员工 (Axios)** 发现一个问题（服务器返回401）。
2.  他把问题报告给他的**小组长 (响应拦截器)**。
3.  **小组长** 看了原始报告，觉得太乱，于是重新整理成一个标准格式的报告（创建了新的 `{status, message}` 对象），然后说：“这个问题我解决不了，继续上报给经理！” (`return Promise.reject`)。
4.  **经理 (`await` 关键字)** 收到了这份标准格式的问题报告，他一看就知道这是个需要处理的异常，于是立即启动了应急预案（跳转到 `catch` 块）。

总结来说，拦截器里的 `return Promise.reject` 是一个**传递**动作，而不是一个**终止**动作。它让您有机会在错误向上传播的过程中，检查、记录或修改这个错误，然后再把它交给上层调用者（`try...catch`）去最终处理。





### 9 自定义属性

#### 常用api

- getAttribute("属性")
- setAttribute("属性"，"值")
- removeAttribute("属性")

使用data-作为自定义属性的前缀来命名

如：

~~~html
    <div data-index="1" id="index"></div>

    <script>
        let index = document.querySelector("#index");
        // 使用getAttribute获取自定义的属性 兼容性好，ie各各版本都支持
        let res = index.getAttribute("data-index");
        console.log(res);
        // 使用setAttribute来添加自定义的属性
        index.setAttribute('data-time',20);
        console.log(index.getAttribute("data-time"));
    </script>
~~~

H5新增的获取自定义属性的方法：ie 11才支持

只能获取data-开头的属性

1 element.dataset.自定义属性 

2 element.dataset['自定义属性']

如：

~~~html
    <div data-index="1" id="index"></div>

    <script>
        let index = document.querySelector("#index");
        console.log(index.dataset.index);
        console.log(index.dataset['index']);
    </script>
~~~

#### 应用案例：点击切换tab栏和内容

~~~html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    <style>
        * {
            margin: 0;
            padding: 0;
        }
        
        li {
            list-style-type: none;
        }
        
        .tab {
            width: 978px;
            margin: 100px auto;
        }
        
        .tab_list {
            height: 39px;
            border: 1px solid #ccc;
            background-color: #f1f1f1;
        }
        
        .tab_list li {
            float: left;
            height: 39px;
            line-height: 39px;
            padding: 0 20px;
            text-align: center;
            cursor: pointer;
        }
        
        .tab_list .current {
            background-color: #c81623;
            color: #fff;
        }
        
        .item_info {
            padding: 20px 0 0 20px;
        }
        
        .item {
            display: none;
        }
    </style>
</head>
<body>
    <div class="tab">
        <div class="tab_list">
            <ul>
                <li class="current">商品介绍</li>
                <li>规格与包装</li>
                <li>售后保障</li>
                <li>商品评价（50000）</li>
                <li>手机社区</li>
            </ul>
        </div>
        <div class="tab_con">
            <div class="item" style="display: block;">
                商品介绍模块内容
            </div>
            <div class="item">
                规格与包装模块内容
            </div>
            <div class="item">
                售后保障模块内容
            </div>
            <div class="item">
                商品评价（50000）模块内容
            </div>
            <div class="item">
                手机社区模块内容
            </div>

        </div>
    </div>
    <script>
        let lis = document.querySelector(".tab_list").querySelectorAll("li");
        let items = document.querySelectorAll(".item");
        for(let i = 0;i<lis.length;i++){
            // 给每个li设置自定义属性来映射对应的item
            lis[i].setAttribute("index",i);// index=1时 表示 这个li对应的是items[1]
            lis[i].onclick = function(){
                //清除全部li的样式
                for(let i = 0;i<lis.length;i++){
                    lis[i].className = '';
                }
                // 给自己(被点击的)设置样式
                this.className = 'current';
                // 隐藏所有的内容模块
                for(let i = 0;i<items.length;i++){
                    items[i].style.display = 'none';
                }
                // 显示自己的内容模块(难点)
                let index = this.getAttribute("index");
                items[index].style.display = 'block';
            }
        }
    </script>
</body>
</html>
~~~



### 10 常用字符串api

#### slice 截取字符串

例：

~~~js

				if(invoiceType == 1){
                      // nvoice.startTime = "2023-11-20 18:00:39"

					invoice.onlyDateStart = invoice.startTime.split(" ")[0];// "2023-11-20"
					invoice.onlyTimeStart = invoice.startTime.split(" ")[1].slice(0, 5);// "18:00"
					invoice.onlyDateEnd = invoice.arrivalTime.split(" ")[0];
					invoice.onlyTimeEnd = invoice.arrivalTime.split(" ")[1].slice(0, 5);
                }
~~~



### 11 处理浮点数

1

~~~js
// parseFloat: 将字符串转换为浮点数。
// isNaN: 检查是否转换成功，确保输入是一个有效的数字。
// toFixed(2): 将数字格式化为保留两位小数的字符串。
formatNumber(numberDesc) {
			    // 将 numberDesc 转换为浮点数
			    let number = parseFloat(numberDesc);
			    
			    if (isNaN(number)) {
			        throw new Error("输入必须是一个有效的数字");
			    }
			
			    // 使用 toFixed 来保留两位小数并返回字符串
			    return number.toFixed(2);
			},
~~~

2

~~~js
// 计算总金额
			computedTotalNum() {
				let total = this.allInvoiceArray.reduce((accumulator, currentItem) => {
				    // 使用 BigNumber 进行精确的浮点运算
					if(currentItem.totalMoney == undefined || currentItem.totalMoney.length == 0){
						return new BigNumber(accumulator).plus(new BigNumber(0));
					}
				    return new BigNumber(accumulator).plus(new BigNumber(currentItem.totalMoney));
				  }, new BigNumber(0));
				  // 返回保留两位小数的字符串
				  return total.toFixed(2);
			}
~~~



#### 保留两位小数

1

~~~js
					// 总金额保留两位小数
					// invoice.totalMoney = this.formatNumber(invoice.totalMoney);
					invoice.totalMoney = new BigNumber(invoice.totalMoney).toFixed(2);
~~~






### 12 **`localStorage` 和 `sessionStorage` 介绍**

`localStorage` 和 `sessionStorage` 都是 **Web Storage API** 的一部分，用于在浏览器中存储数据。它们的主要区别如下：

| 存储类型         | 作用域           | 过期时间                                         | 适用场景                                                |
| ---------------- | ---------------- | ------------------------------------------------ | ------------------------------------------------------- |
| `localStorage`   | 整个网站（同源） | **永久存储**，即使刷新或关闭浏览器后数据仍然存在 | 适用于需要长期存储的用户数据，如 **用户设置、记住密码** |
| `sessionStorage` | 单个标签页       | **会话级别**，关闭浏览器或标签页后数据会自动清除 | 适用于临时数据，如 **验证码、临时表单数据**             |

------

## **`localStorage` 例子：记住用户偏好设置**

这个例子演示如何使用 `localStorage` 让用户切换 **深色模式**，并在刷新页面后仍然保持选择。

### **1️⃣ HTML + Vue 代码**

```vue
<template>
  <div :class="{ dark: isDarkMode }" class="container">
    <h2>LocalStorage 示例 - 记住深色模式</h2>
    <button @click="toggleDarkMode">
      {{ isDarkMode ? "切换到亮色模式" : "切换到深色模式" }}
    </button>
  </div>
</template>

<script>
export default {
  data() {
    return {
      isDarkMode: false
    };
  },
  created() {
    // 页面加载时检查 localStorage 中是否有保存的模式
    const savedMode = localStorage.getItem("darkMode");
    this.isDarkMode = savedMode === "true"; // 转换为布尔值
  },
  methods: {
    toggleDarkMode() {
      this.isDarkMode = !this.isDarkMode;
      localStorage.setItem("darkMode", this.isDarkMode); // 存储到 localStorage
    }
  }
};
</script>

<style scoped>
.container {
  padding: 20px;
  text-align: center;
  transition: background 0.3s, color 0.3s;
}
.dark {
  background: black;
  color: white;
}
button {
  padding: 10px 20px;
  cursor: pointer;
  margin-top: 10px;
}
</style>
```

------

### **2️⃣ 功能说明**

1. **当用户点击按钮** 切换深色模式，并将状态保存到 `localStorage`。
2. **刷新页面后**，仍然保持上次选择的模式（**localStorage 持久化存储**）。
3. **样式动态绑定**：Vue 通过 `:class="{ dark: isDarkMode }"` 绑定深色模式。

------

## **`sessionStorage` 例子：存储临时表单数据**

如果希望 **用户填写表单后刷新页面不会丢失**，但关闭浏览器后自动清除数据，可以使用 `sessionStorage`。

```vue
<template>
  <div>
    <h2>SessionStorage 示例 - 记住表单数据</h2>
    <input type="text" v-model="username" placeholder="输入用户名">
    <button @click="saveData">保存</button>
  </div>
</template>

<script>
export default {
  data() {
    return {
      username: ""
    };
  },
  created() {
    // 页面加载时从 sessionStorage 读取数据
    this.username = sessionStorage.getItem("username") || "";
  },
  methods: {
    saveData() {
      sessionStorage.setItem("username", this.username);
      alert("数据已保存（刷新后仍在，但关闭浏览器会清除）");
    }
  }
};
</script>

<style scoped>
input {
  padding: 8px;
  margin-right: 10px;
}
</style>
```

------

### **总结**

| 存储方式     | `localStorage`            | `sessionStorage`         |
| ------------ | ------------------------- | ------------------------ |
| 数据范围     | 整个网站（跨页面）        | 仅当前标签页             |
| 数据存活时间 | 永久                      | 仅当前会话，关闭页面清除 |
| 适用场景     | 用户设置、记住密码、Token | 临时表单数据、验证码     |

如果你希望 **长期存储数据**（如深色模式、用户设置），使用 `localStorage`。
 如果你只需要 **临时存储**（如表单数据），使用 `sessionStorage` 更合适。

🚀 **快试试看吧！**







当然可以！这里是**简洁版的 Moment.js 常用操作笔记**，已经配了**使用案例**，适合直接用在项目文档里👇

------

# 实际使用
## 一、 📚 Moment.js 操作时间 最常用操作笔记

### 1. 创建时间对象

```javascript
// 当前时间
const now = moment();

// 指定时间
const someTime = moment('2025-04-27 14:00:00');
```

------

### 2. 加时间 / 减时间

```javascript
// 当前时间加3天
const threeDaysLater = moment().add(3, 'days');

// 指定时间减2小时
const twoHoursBefore = moment('2025-04-27 14:00:00').subtract(2, 'hours');
```

------

### 3. 格式化时间

```javascript
// 转为'年-月-日 小时:分钟:秒'格式
const formatted = moment().format('YYYY-MM-DD HH:mm:ss'); 
// 示例输出: "2025-04-27 16:45:12"
```

------

### 4. 比较时间

```javascript
// 判断某时间是否在另一个时间之前
const isBefore = moment('2025-04-27').isBefore('2025-05-01');  // true

// 判断某时间是否在另一个时间之后
const isAfter = moment('2025-04-27').isAfter('2025-04-01');     // true
```

------

### 5. 计算时间差

```javascript
// 两个时间相差的天数
const daysDiff = moment('2025-05-01').diff('2025-04-27', 'days');  // 4
```

------

### 6. 转换为原生对象 / 时间戳

```javascript
// 转为JavaScript的Date对象
const dateObj = moment().toDate();

// 获取时间戳（毫秒）
const timestamp = moment().valueOf();
```

------

# 📌 小提示

- 单位常用：`years`、`months`、`days`、`hours`、`minutes`、`seconds`
- 格式常用：`'YYYY-MM-DD'`、`'YYYY-MM-DD HH:mm:ss'`
- 记得用 `.isValid()` 检查日期是否合法

------

要不要我也顺便给你整理一版“适合面试/速查”的更短表格版？那种一页纸看完的👀？如果想要的话告诉我～✨



## 二、清空数组：AppState._sClassTypeConfigs.length = 0

是的，`AppState._sClassTypeConfigs.length = 0` 是一种**完全正确且非常高效**的清空数组的方法，在 JavaScript 和 TypeScript 中被广泛使用。

### 解释：

1.  **修改 `length` 属性**：在 JavaScript 中，数组的 `length` 属性不仅可以读取，还可以写入。当你将一个数组的 `length` 属性设置为一个小于当前长度的值时，数组会被截断到新的长度，超出新长度的所有元素都会被删除。当你将其设置为 `0` 时，数组中的所有元素都会被删除，数组就变为空数组。

2.  **原地修改（In-place Modification）**：
    *   这种方法会修改原数组对象本身，而不是创建一个新的数组对象。这意味着，如果你的代码中其他地方持有对 `AppState._sClassTypeConfigs` 数组的引用，那些引用仍然会指向这个被清空了的数组。
    *   例如：
        ```typescript
        let arr1 = [1, 2, 3];
        let arr2 = arr1; // arr2 引用 arr1
        arr1.length = 0; // 清空 arr1
        console.log(arr1); // 输出 []
        console.log(arr2); // 输出 [] （arr2 也被清空了，因为它们指向同一个数组对象）
        ```
    *   这与 `AppState._sClassTypeConfigs = []` 不同，后者会创建一个全新的空数组，并使 `AppState._sClassTypeConfigs` 指向这个新数组。如果其他变量仍然引用旧数组，它们将不会看到这个清空操作。在你的 `AppState` 场景中，通常希望 `AppState._sClassTypeConfigs` 始终指向同一个全局数组，并更新其内容，所以 `length = 0` 是非常合适的。

3.  **性能优势**：对于大型数组，`array.length = 0` 通常比其他清空方法（如 `array = []` 或 `array.splice(0, array.length)`）具有更好的性能，因为它涉及的操作最少。

因此，你的写法 `AppState._sClassTypeConfigs.length = 0` 是完全符合预期和最佳实践的，特别是在你需要清空一个现有数组并随后重新填充其内容时。



## 三、批量更新对象的内部属性，Object.assign(target, ...sources)函数的使用

~~~
Object.assign(BUSINESS_CONFIG, configObj);是什么意思，举例子进行说明，为什么不使用BUSINESS_CONFIG = configObj来进行覆盖，然后让其他人导入这个配置来获取信息
~~~



好的，我来详细解释 `Object.assign(BUSINESS_CONFIG, configObj);` 的含义，并说明为什么在这里选择 `Object.assign` 而不是直接赋值。

### `Object.assign(BUSINESS_CONFIG, configObj);` 的含义及示例

`Object.assign()` 是 JavaScript 中用于将所有可枚举的自有属性的值从一个或多个源对象复制到目标对象的方法。它会返回目标对象。

**语法：**
`Object.assign(target, ...sources)`

- `target`：目标对象。
- `sources`：源对象（一个或多个）。

**作用：**
它将 `sources` 对象中的所有属性（包括其值）复制到 `target` 对象。如果 `target` 和 `sources` 中有相同的属性名，`sources` 中的属性会覆盖 `target` 中的属性。

**举例说明：**

假设 `BUSINESS_CONFIG` 是一个全局配置对象，在你的 `business.config.js` 文件中可能定义了一些默认值，或者一些固定的配置项。

```javascript
// business.config.js
export const BUSINESS_CONFIG = {
  // 默认值或固定配置
  baseApiUrl: 'http://localhost:8080/api',
  timeout: 5000,
  debugMode: false,
  language: 'zh-CN'
};
```

现在，通过 Qt 接口获取到的 `configObj` 包含了一些需要覆盖或新增的生产环境配置：

```javascript
// 假设从 Qt 获取到的 configObj
const configObj = {
  // 覆盖现有属性
  baseApiUrl: 'http://production.example.com/api',
  // 新增属性
  MasterServerUrls: ['http://master1.example.com', 'http://master2.example.com'],
  IsUseLocalAudio: true
};
```

**如果使用 `Object.assign(BUSINESS_CONFIG, configObj);`：**

```javascript
Object.assign(BUSINESS_CONFIG, configObj);

/*
此时 BUSINESS_CONFIG 将变为：
{
  baseApiUrl: 'http://production.example.com/api', // 被覆盖
  timeout: 5000,                                 // 保留
  debugMode: false,                              // 保留
  language: 'zh-CN',                             // 保留
  MasterServerUrls: ['http://master1.example.com', 'http://master2.example.com'], // 新增
  IsUseLocalAudio: true                          // 新增
}
*/
```
`Object.assign` 会将 `configObj` 中的属性合并到 `BUSINESS_CONFIG` 中。如果 `BUSINESS_CONFIG` 中已经存在同名属性，则会被 `configObj` 中的值覆盖；如果 `configObj` 中有新属性，则会被添加到 `BUSINESS_CONFIG` 中。这实现了**增量更新**和**覆盖**的效果，保留了 `BUSINESS_CONFIG` 中 `configObj` 未提供的其他属性。

### 为什么不使用 `BUSINESS_CONFIG = configObj`？

不直接使用 `BUSINESS_CONFIG = configObj;` 的原因，主要涉及到 JavaScript 模块导入的机制以及对象引用的问题。

1.  **模块导入的是引用，而不是副本：**
    在 JavaScript 的 ES Modules (ESM) 规范中，当你在一个文件中 `export` 一个变量（例如 `export const BUSINESS_CONFIG`），并在另一个文件中 `import` 它时，导入的是对原始变量的**引用**。

    这意味着，其他文件导入的 `BUSINESS_CONFIG` 变量，实际上是指向 `main.js` 中 `BUSINESS_CONFIG` 变量所指向的内存地址。

    *   **如果使用 `BUSINESS_CONFIG = configObj;`：**
        这会将 `BUSINESS_CONFIG` 这个变量本身重新赋值为一个新的对象 (`configObj`)。此时，`main.js` 中 `BUSINESS_CONFIG` 变量的内存地址改变了。
        然而，其他已经导入 `BUSINESS_CONFIG` 的模块，它们的 `BUSINESS_CONFIG` 变量仍然指向**旧的**内存地址（也就是 `business.config.js` 中最初定义的对象）。它们不会感知到 `main.js` 中 `BUSINESS_CONFIG` 的引用已经改变了。
        因此，其他模块获取到的配置信息将是**过时**的，而不是从 Qt 端加载的最新配置。

    ```javascript
    // business.config.js
    export const BUSINESS_CONFIG = { a: 1, b: 2 };
    
    // otherModule.js
    import { BUSINESS_CONFIG } from './business.config.js';
    console.log(BUSINESS_CONFIG); // { a: 1, b: 2 } (初始值)
    
    // main.js
    import { BUSINESS_CONFIG } from './business.config.js';
    const newConfig = { c: 3, d: 4 };
    BUSINESS_CONFIG = newConfig; // 这里尝试重新赋值
    
    // 在 main.js 中，BUSINESS_CONFIG 现在是 { c: 3, d: 4 }
    // 但是在 otherModule.js 中，BUSINESS_CONFIG 仍然是 { a: 1, b: 2 }
    ```

2.  **保持单例模式的配置对象：**
    通常，全局配置对象（如 `BUSINESS_CONFIG`）在应用程序中应该是一个单例。这意味着所有模块都应该访问同一个配置实例，以确保数据的一致性。

    `Object.assign()` 的作用是修改**目标对象**的属性，而不是重新分配**目标变量**的引用。通过这种方式，`BUSINESS_CONFIG` 变量始终指向最初在 `business.config.js` 中定义的对象，只是该对象的内部属性被更新了。所有导入 `BUSINESS_CONFIG` 的模块，都会通过它们持有的引用访问到这个**已被更新的同一对象**。

    ```javascript
    // business.config.js
    export const BUSINESS_CONFIG = { a: 1, b: 2 };
    
    // otherModule.js
    import { BUSINESS_CONFIG } from './business.config.js';
    console.log(BUSINESS_CONFIG); // { a: 1, b: 2 } (初始值)
    
    // main.js
    import { BUSINESS_CONFIG } from './business.config.js';
    const newConfig = { b: 20, c: 30 };
    Object.assign(BUSINESS_CONFIG, newConfig); // 修改 BUSINESS_CONFIG 指向的对象的属性
    
    // 在 main.js 中，BUSINESS_CONFIG 现在是 { a: 1, b: 20, c: 30 }
    // 在 otherModule.js 中，BUSINESS_CONFIG 也变为 { a: 1, b: 20, c: 30 }
    // 因为它们都指向同一个被修改的对象
    ```

因此，使用 `Object.assign(BUSINESS_CONFIG, configObj);` 是正确的做法，它确保了所有导入 `BUSINESS_CONFIG` 的模块都能获取到从 Qt 加载的最新且完整的配置信息，并且与模块导入的引用机制兼容。



## 四、some函数的使用（测试数组中是否至少有一个元素能通过）

~~~
只回答问题：“const faceList = AppState._sPersonInfoConfigs
        .filter(left => AppState._sClassTypeConfigs.some(right => right.ClassType === left.PersonClass))
        .map(left => {
          const classInfo = AppState._sClassTypeConfigs.find(right => right.ClassType === left.PersonClass)
          return {
            ID: left.ID,
            GroupName: classInfo ? classInfo.ClassName : '',
            PersionName: left.PersonName,
            PersonID: left.SearchId.toString(),
            IsTrackRegister: faceRegisterList.includes(left.SearchId.toString()) ? '√' : ' ',
            IsTrackGoonTop: faceGoonTopList.includes(left.SearchId.toString()) ? '√' : ' '
          }
        })
        .filter(person => person.IsTrackGoonTop === '√' || person.IsTrackRegister === '√')
      
      resultList.push(...faceList)”中的some函数是什么作用
~~~



在您提供的这段代码中，`some` 函数是一个 JavaScript 数组方法，它的作用是**测试数组中是否至少有一个元素能通过由提供的函数实现的测试**。

具体来说：

1.  `some` 函数会遍历 `AppState._sClassTypeConfigs` 数组。
2.  对于数组中的每一个元素（这里用 `right` 表示），它都会执行箭头函数 `right => right.ClassType === left.PersonClass`。
3.  这个箭头函数会检查 `_sClassTypeConfigs` 数组中的当前元素 `right` 的 `ClassType` 属性是否等于 `_sPersonInfoConfigs` 数组中当前元素 `left` 的 `PersonClass` 属性。
4.  如果在遍历过程中，**只要有一个** `right` 元素的 `ClassType` 与 `left.PersonClass` 相匹配（即箭头函数返回 `true`），`some` 函数就会立即停止遍历并返回 `true`。
5.  如果遍历完整个 `_sClassTypeConfigs` 数组都没有找到匹配的元素，`some` 函数才会返回 `false`。

**总结一下在这段代码里的作用：**

`filter` 函数正在筛选 `AppState._sPersonInfoConfigs` 数组。`some` 函数作为 `filter` 的判断条件，其目的是**确保只有当一个人员信息（`left`）的 `PersonClass` 存在于 `AppState._sClassTypeConfigs` 数组中时，该人员信息才会被保留下来**。换句话说，它过滤掉了所有 `PersonClass` 无效或不存在的人员。



## 五、Object.keys的使用

这段代码的核心作用是**将一个以“股道名称”为 Key 的对象（字典结构），转换成一个由“股道信息对象”组成的数组（列表结构），并补充相关的配置信息和作业状态。**

这是一种常见的数据格式转换操作，目的是为了让前端模板（例如 `v-for` 循环）更容易渲染股道列表。

以下是详细的解释：

### 1. `Object.keys(trackGrouped).map(...)` 是什么意思？

这是 JavaScript 中将**对象（Object）**转换为**数组（Array）**的常用组合技：

1.  **`Object.keys(trackGrouped)`**:
    *   它会提取 `trackGrouped` 对象中所有的 **Key（键名）**。
    *   **结果**：返回一个字符串数组，例如 `["D48道", "D49道"]`。

2.  **`.map((trackName) => { ... })`**:
    *   它会遍历上面的字符串数组（即遍历每一个股道名称）。
    *   对每一个 `trackName` 执行大括号里的逻辑，生成一个新的对象。
    *   **结果**：最终返回一个包含这些新对象的数组。

**简单来说：** 它把 `{"A": data, "B": data}` 变成了 `[ {name: "A", ...data}, {name: "B", ...data} ]`。

---

### 2. 具体的输入输出例子

假设从后端 API 获取数据并经过初步分组后，**输入数据** (`trackGrouped`) 是这样的：

```json
// 输入：trackGrouped (对象结构)
{
  "D48道": {
    "hasAvailablePosition": true,
    "positions": [
      { 
        "trackId": 101, 
        "name": "D48道1列位", 
        "workTaskId": 666,  // 注意：这个列位有作业ID
        "status": "unavailable" 
      },
      { 
        "trackId": 101, 
        "name": "D48道2列位", 
        "workTaskId": null, 
        "status": "available" 
      }
    ]
  },
  "D49道": {
    "hasAvailablePosition": false,
    "positions": [
      { 
        "trackId": 102, 
        "name": "D49道1列位", 
        "workTaskId": null, 
        "status": "unavailable" 
      }
    ]
  }
}
```

假设配置列表 (`PublicVarFeat._sTrackInfoConfigList`) 中有对应的信息：
```json
[
  { "ID": 101, "TrackNo": 48, "LineNum": 2, "DoorNum": 4, "TrackServiceUrl": "http://..." },
  { "ID": 102, "TrackNo": 49, "LineNum": 1, "DoorNum": 2, "TrackServiceUrl": "http://..." }
]
```

经过这段代码处理后，**输出数据** (`allTracks.value`) 会是这样的数组：

```json
// 输出：allTracks.value (数组结构)
[
  {
    "id": 101,
    "trackNo": 48,           // 从 PublicVarFeat 配置中找到的
    "trackName": "D48道",    // 来自 Object.keys 的键名
    "name": "D48道1列位",     // 取了第一个列位的名字作为显示名
    "hasAvailablePosition": true,
    "trackServiceUrl": "http://...",
    "lineNum": 2,
    "doorNum": 4,
    "jobId": 666             // 关键点：因为 positions 里有一个有 workTaskId=666，所以这里提取出来了
  },
  {
    "id": 102,
    "trackNo": 49,
    "trackName": "D49道",
    "name": "D49道1列位",
    "hasAvailablePosition": false,
    "trackServiceUrl": "http://...",
    "lineNum": 1,
    "doorNum": 2,
    "jobId": null            // D49道的列位都没有作业，所以是 null
  }
]
```

### 3. 代码逻辑逐行解析

*   **提取第一条数据**:
    `const firstPosition = trackGrouped[trackName].positions[0]`
    因为同一个股道下的所有列位，其 `trackId` 都是一样的，所以取第一个列位的数据作为代表。

*   **查找配置信息**:
    `const trackInfo = PublicVarFeat._sTrackInfoConfigList.find(...)`
    根据 ID 去全局配置列表里查找更详细的硬件参数（如几号线、有几个门）。

*   **查找作业ID (重要)**:
    `const positionWithJob = trackGrouped[trackName].positions.find(pos => pos.workTaskId)`
    这段代码遍历该股道下的**所有列位**。只要发现**任意一个**列位有 `workTaskId`（正在进行作业），就认为这个股道当前关联了这个作业。
    *   **目的**：这就是为了实现我们之前做的功能——**按钮状态恢复**。只有知道这个股道对应哪个 `jobId`，切换股道时才能去数据库查按钮状态。

*   **构造返回对象**:
    最后 `return { ... }` 组装成前端 UI 所需的最终数据格式。



## 待定

### 1

### 2 零散的知识

1 阻止a标签中的链接跳转需要在href中添加 javascript:void(0); 或者 javascript:;
