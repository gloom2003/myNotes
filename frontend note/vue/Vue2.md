

# Vue2.0

## 入门案例

### node环境配置，npm下载依赖：

vscode插件：https://blog.csdn.net/weixin_38203203/article/details/93124390

使用cnpm替换npm：https://juejin.cn/post/6983231659378212877   

npm对应cnpm版本:https://wenku.csdn.net/answer/1eax3i4qdw

得出命令：

~~~bash
npm install -g cnpm@5.1 --registry=http://registry.npm.taobao.org
~~~



安装node：略

使用nvm管理node版本：https://blog.csdn.net/m0_62173728/article/details/131992891  ，https://blog.csdn.net/ThisEqualThis/article/details/125893548



## 下载依赖速度慢处理(镜像或代理)

管理员运行后：配置镜像、配置代理（使用规则模式快很多）、使用cnpm替换npm

~~~
只回答我的问题，“E:\BRI\SafetyLinkNovoItem\SafetyLinkNovo\SafetyLinkNovo\DoorSysPlatform\web-client-admin>npm config list
; cli configs
metrics-registry = "https://registry.npmmirror.com/"
scope = ""
user-agent = "npm/6.14.18 node/v14.21.3 win32 x64"

; userconfig C:\Users\Gloom\.npmrc
proxy = "http://127.0.0.1:7890/"
registry = "https://registry.npmmirror.com/"
sass_binary_site = ""

; node bin location = C:\Program Files\nodejs\node.exe
; cwd = E:\BRI\SafetyLinkNovoItem\SafetyLinkNovo\SafetyLinkNovo\DoorSysPlatform\web-client-admin
; HOME = C:\Users\Gloom
; "npm config ls -l" to show all defaults.

”这样子是没有设置镜像源是吗，如何设置镜像源
~~~



根据你提供的 `npm config list` 输出信息，你 **已经成功设置了镜像源**。

请看这一行：

```
registry = "https://registry.npmmirror.com/"
```

`https://registry.npmmirror.com/` 就是淘宝 NPM 镜像源的地址。当你执行 `npm install` 等命令时，npm 会从这个镜像地址下载包，而不是从国外的官方源 `https://registry.npmjs.org/` 下载，这通常会大大提高下载速度。

所以，你不需要再进行额外的设置了。

如果你想更换为其他的镜像源，可以使用以下命令：

例如，换回官方源：

```bash
npm config set registry https://registry.npmjs.org/
```

再例如，换回淘宝镜像源（和你当前的一样）：

```bash
npm config set registry https://registry.npmmirror.com/
```

另外，我注意到你还设置了代理：

```
proxy = "http://127.0.0.1:7890/"
```

请确保这个代理服务正在你的电脑上运行，否则即使设置了正确的镜像源，npm 也可能因为无法通过代理连接到网络而导致安装失败。如果你没有在使用代理，或者不确定它是什么，可以考虑将其移除：

```bash
npm config delete proxy
```





**Vue语法检编译查规则配置**：

注意：

- Mixed spaces and tabs no-mixed-spaces-and-tabs解决方法：https://blog.csdn.net/weixin_45771601/article/details/123518944
- 组件名"School"应该使用多个单词拼接横线组成: https://blog.csdn.net/Vest_er/article/details/126456572  并且package.json中不能有注释!



![image-20240801145505599](C:\Users\Gloom\AppData\Roaming\Typora\typora-user-images\image-20240801145505599.png)

![image-20240801150351157](C:\Users\Gloom\AppData\Roaming\Typora\typora-user-images\image-20240801150351157.png)

### Vue的绑定功能

**重点：**

- 绑定文本：{{  }}
- 绑定属性：v-bind  简写为:
- 绑定事件: v-on 简写为@

new Vue创建一个Vue实例

注意：

![image-20240822175019995](E:\alwaysUse\notes\myNotes\frontend note\vue\vue.assets\image-20240822175019995.png)



#### 快速入门

基础：

- 函数中使用js data中定义的变量时需要使用this进行指示，默认没有使用this时只会在本函数找变量，找不到就是undefined报错，不会去data中找
- 在html标签中就不需要使用this进行指示，会自动去data中找



其实所有的组件其实都是Vue实例

~~~html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
</head>
<body>
    
    <!-- <h1>hello world</h1> -->
    <div id="app">
         <!-- 绑定文本：{{  }} 意思是表达式 -->
        <h1>{{ message + "!" }}</h1>
        <!-- 绑定属性：v-bind:src 简写为:src，表示使用url这个key绑定了img标签的src属性，使用时去Vue实例data中获取真正的值value -->
        <img :src="url">
        <!-- 绑定事件: v-on:click="sayHi" 简写为@click 表示绑定一个点击事件，点击后调用Vue实例methods中的sayHi（）函数 -->
        <button @click="sayHi">测试</button>
        <button @click="changeImage">更换图片</button>
    </div>

    <script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>

    <script>
        // 创建Vue实例，传输一个自定义对象进去(el,data,methods是固定的)
        new Vue({
            // 选项
            // 相当于选择器：选择了id=app的element
            el:"#app",
            // 一个数据map集合，来这里根据key获取value数据
            data:{
                message: "hello world",
                url:"img/1.jpg"
            },
            methods:{
                sayHi(){
                    alert("Hi!")
                },
                changeImage(){
                    // this获取当前的Vue实例，获取数据都是访问data选项里面的数据
                    this.url = "img/2.jpg";
                }
            }
        })
    </script>
</body>
</html>
~~~



#### 计数器例子：

~~~html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
</head>
<body>
    <div id="app">
        <button @click="sub">-</button>
        <span>{{ number }}</span>
        <button @click="add">+</button>
    </div>
    <script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
    <script>
        new Vue({
        el:"#app",
        data:{
            number:0
        },
        methods:{
            add(){
                this.number++;
            },
            sub(){
                if(this.number > 0){
                    this.number--;
                }
            }
        }
    })
    </script>

</body>
</html>
~~~



#### 事件修饰符：



##### .stop 阻止事件冒泡:



![image-20240907172312805](E:\alwaysUse\notes\myNotes\frontend note\vue\vue.assets\image-20240907172312805.png)



##### .prevent 阻止事件的默认行为

![image-20240907173110365](E:\alwaysUse\notes\myNotes\frontend note\vue\vue.assets\image-20240907173110365.png)



##### .self 只触发自己标签上的事件

> 只关心自己标签上触发的事件，不监听事件冒泡传过来的事件
>
> 与.stop作用类似



##### .once 设置事件只触发一次



![image-20240907174218349](E:\alwaysUse\notes\myNotes\frontend note\vue\vue.assets\image-20240907174218349.png)



#### 按键修饰符：

https://cn.vuejs.org/guide/essentials/event-handling.html#key-modifiers

##### 按键别名

Vue 为一些常用的按键提供了别名：

- `.enter`
- `.tab`
- `.delete` (捕获“Delete”和“Backspace”两个按键)
- `.esc`
- `.space`
- `.up`
- `.down`
- `.left`
- `.right`



##### keyup.enter 点击回车后触发的事件



![image-20240907175705771](E:\alwaysUse\notes\myNotes\frontend note\vue\vue.assets\image-20240907175705771.png)



### 3级 Vue/cli创建Vue项目

#### 目录结构解析

![image-20240801162728772](C:\Users\Gloom\AppData\Roaming\Typora\typora-user-images\image-20240801162728772.png)

命令：

~~~bash
cnpm install -g @vue/cli
vue create hello
npm run serve
~~~

##### 5级 **package.json:**

![image-20240801171916383](C:\Users\Gloom\AppData\Roaming\Typora\typora-user-images\image-20240801171916383.png)

##### **App.vue:**

组件化开发：

![image-20240801171728576](C:\Users\Gloom\AppData\Roaming\Typora\typora-user-images\image-20240801171728576.png)

**只能有一个一级标签**：

![image-20240801173641555](C:\Users\Gloom\AppData\Roaming\Typora\typora-user-images\image-20240801173641555.png)



**使用export把App.vue暴露出去**给别人进行import:

在node环境运行的Vue项目中，**data选项比较特殊**（如图），其他选项与直接引入Vue.js相同：

![image-20240801174205237](C:\Users\Gloom\AppData\Roaming\Typora\typora-user-images\image-20240801174205237.png)

例子：

计时器App.vue:

~~~vue
<template>
  <div id="app">
    <button @click="sub">-</button>
    <span>{{ number }}</span>
    <button @click="add">+</button>
  </div>
</template>

<script>

export default {
  // 注意：这里不需要 el:"#app"了，在main.js中已经做了
  data(){
    return {
      number:0
    }
  },
  methods:{
    add(){
      this.number++;
    },
    sub(){
      if(this.number > 0){
          this.number--;
      }
    }
  }
}
</script>

<style>

</style>

~~~



##### **main.js:**

入口文件

![image-20240801172619892](C:\Users\Gloom\AppData\Roaming\Typora\typora-user-images\image-20240801172619892.png)

##### components

一般是存放自定义组件的地方

#### 自定义组件

##### 使用原生的事件



注意：

![image-20240825161459088](E:\alwaysUse\notes\myNotes\frontend note\vue\vue.assets\image-20240825161459088.png)



###### 组件命名：

一般采用大驼峰命名(大写字母开头)，防止与html自带的组件冲突（默认会优先使用html的组件，这样子的话就会出现自定义的同名组件没有使用的错误）

###### 注册组件的方式：

1）传统方式

~~~vue
<template>
  <div id="app">
	  <!-- 3.引入外部组件时，是不区分大小写的，并且每个单词可以使用-进行分割 -->
	<hello></hello>
    
  </div>
</template>

<script>
	// 1.导入hello组件
import hello from "./components/hello.vue"

export default {
	// 2.注册hello组件
	components:{
		// 组件名：组件值 ，相同时可简写为hello
		hello:hello
	}
}
</script>

<style>

</style>

~~~

注意：引入的组件一定要使用，不然会报错。

2）uniapp的easycom方式：

![image-20240825124830120](E:\alwaysUse\notes\myNotes\frontend note\vue\vue.assets\image-20240825124830120.png)



###### 引用外部组件



引用外部组件时，是不区分大小写的，并且每个单词可以使用-进行分割：

例如：

![image-20240802110839228](C:\Users\Gloom\AppData\Roaming\Typora\typora-user-images\image-20240802110839228.png)



### Vue 中组件（Component）
组件作用：用来减少 Vue 实例对象中代码量，日后在使用 Vue 开发过程中，可以根据不同业务功能将页面中划分不同的多个组件，然后由多个组件去完成整个页面的布局，便于日后使用 Vue 进行开发时页面管理，方便开发人员维护。

#### 全局组件的开发
全局组件注册给 Vue 实例，可以在任意 Vue 实例的范围内使用该组件。

全局组件的开发：
~~~html

<!DOCTYPE html>
<html>
<head>
  <meta charset='utf-8'>
  <title>全局组件的开发</title>
</head>
<body>
  <div id="app">
    <!-- 使用全局组件 -->
    <login></login>
    <!-- 使用局部组件 -->
    <user-login></user-login>
  </div>
  <script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
  <script>
    // 开发全局组件
    // 参数1: 组件名称
    // 参数2: 组件配置对象 template:用来书写组件的html代码(注意:在template中必须存在一个容器)
    Vue.component('login', {
      template : '<div><h1>用户登录</h1></div>'
    });
    // 驼峰命名法的组件会被特殊处理, userLogin 使用时必须写成 user-login
    Vue.component('userLogin', {
      template : '<div><input type="button" value="登录"></div>'
    });
    const app = new Vue({
      el: "#app",
      data: {},
      methods: {},
    });
  </script>
</body>
</html>
~~~



#### 局部组件的开发

通过将组件注册给对应 Vue 实例中一个 components 属性来完成组件注册，这种方式不会对 Vue 实例造成累加。

第一种开发方式：
~~~html

<!DOCTYPE html>
<html>

<head>
  <meta charset='utf-8'>
  <title>局部组件的开发</title>
</head>

<body>
  <div id="app">
    <login></login>
    <login></login>
    <login></login>
  </div>
  <script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
  <script>
    // 定义变量用来保存模板配置对象
    const login = {
      template: '<div><h2>用户登录</h2></div>'
    };
    const app = new Vue({
      el: "#app",
      data: {},
      methods: {},
      components: { // 局部组件
        login: login // 注册局部组件
      }
    });
  </script>
</body>

</html>
~~~
32
第二种开发方式：

~~~html

<!DOCTYPE html>
<html lang="en" xmlns:v-on="http://www.w3.org/1999/xhtml">

<head>
  <meta charset="UTF-8">
  <title>局部组件的开发2</title>
</head>

<body>
  <div id="app">
    <login></login>
    <login></login>
    <login></login>
  </div>

  <!--声明局部组件模板  template标签 注意:在 Vue 实例作用范围外声明-->
  <template id="loginTemplate">
    <h2>用户登录</h2>
  </template>

  <script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
  <script>
    // 定义变量用来保存模板配置对象
    const login = { // 具体局部组件名称
      template: '#loginTemplate' // 定义template标签选择器即可
    };
    const app = new Vue({
      el: "#app",
      data: {},
      methods: {},
      components: { // 局部组件
        login: login // 注册局部组件
      }
    });
  </script>
</body>

</html>
~~~



### Vue的指令

#### 注意：

![image-20241015145055005](E:\alwaysUse\notes\myNotes\frontend note\vue\vue.assets\image-20241015145055005.png)



指令(Directives)是带有`v-`前缀的特殊属性：



#### v-if等 条件判断：

- v-if   为true则显示，为false则不显示（可以配合v-else使用）![image-20240828160305638](E:\alwaysUse\notes\myNotes\frontend note\vue\vue.assets\image-20240828160305638.png)
- 和一般的if语句一样的使用：![image-20240830141824456](E:\alwaysUse\notes\myNotes\frontend note\vue\vue.assets\image-20240830141824456.png)
- v-show

两者的区别：

![image-20240801181025296](C:\Users\Gloom\AppData\Roaming\Typora\typora-user-images\image-20240801181025296.png)

例子：

~~~vue
<template>
  <div id="app">
    <!-- 这里不需要写{{ }}} -->
    <p v-if="isLogin">欢迎你，小明</p>
    <!-- 父类隐藏时子类也会隐藏 -->
    <p v-if="!isLogin">
      <a href="">请登录</a>
    </p>
    
  </div>
</template>

<script>

export default {
  data(){
    return {
      isLogin:true
    }
  }
}
</script>

<style>

</style>

~~~

#### v-for循环显示列表：

- v-for

例子：

~~~vue
<template>
  <div id="app">
    <ul>
		<!-- 相当于for循环生成多个li标签，记得使用索引作为唯一标识来绑定key属性 -->
		<li v-for="(fruit,index) of fruits" :key="index">
			<p>水果序号:{{ index + 1 }}</p>
			<p>水果名称:{{ fruit }}</p>
		</li>
		
		<thead>
			<th>序号</th>
			<th>姓名</th>
			<th>年龄</th>
		</thead>
		<tbody>

			<tr v-for="(v,i) of students" :key="i">
				<td>{{ i + 1 }}</td>
				<td>{{ v.name }}</td>
				<td>{{ v.age }}</td>
			</tr>
		</tbody>
	</ul>
    
  </div>
</template>

<script>

export default {
  data(){
    return {
	  fruits:["苹果","香蕉","莉"],
	  students:[
		  {name:"小明",age:18},
		  {name:"小灰",age:18},
		  {name:"小王",age:18}
	  ]
    }
  }
}
</script>

<style>

</style>

~~~



![image-20240823110625375](E:\alwaysUse\notes\myNotes\frontend note\vue\vue.assets\image-20240823110625375.png)



与of同样的绑定索引：

![image-20240828162823250](E:\alwaysUse\notes\myNotes\frontend note\vue\vue.assets\image-20240828162823250.png)



#### v-html 



![image-20240825172446482](E:\alwaysUse\notes\myNotes\frontend note\vue\vue.assets\image-20240825172446482.png)



#### v-text

![image-20240907165857563](E:\alwaysUse\notes\myNotes\frontend note\vue\vue.assets\image-20240907165857563.png)



### 组件传值

根据组件之间的关系，有三种传值方式：

#### 父级向子级传递数据：绑定标签的自定义属性 + props实现

父级组件:

~~~vue
  <template>
    <div id="app">
  	 <!-- 2.把要传递的数据绑定给子组件hello的自定义属性msg -->
  	<hello :msg="message"></hello>
      
    </div>
  </template>
  
  <script>
  
  import hello from "./components/hello.vue"
  
  export default {
  	components:{
  		hello:hello
  	},
  	// 1.父级定义数据
  	data(){
  		return {
  			message:"app.vue 的值",
  			}
  	}
  }
  </script>
  
  <style>
  
  </style>
  
~~~
子级组件:

~~~vue
<template>
	<!-- 4. 使用属性中的数据-->
	<h1>{{ msg }}</h1>
</template>

<script>
	// 3.子级接收自定义的属性，里面含有数据
	export default{
		props:["msg"]
	}
</script>

<style>
</style>
~~~



##### 默认值设置

1）

![image-20240825170303435](E:\alwaysUse\notes\myNotes\frontend note\vue\vue.assets\image-20240825170303435.png)

2）

![image-20240825171214396](E:\alwaysUse\notes\myNotes\frontend note\vue\vue.assets\image-20240825171214396.png)



##### 单向数据流（子不要改变父传的值）

https://uniapp.dcloud.net.cn/tutorial/vue-components.html#%E5%8D%95%E5%90%91%E6%95%B0%E6%8D%AE%E6%B5%81

注意：

![image-20240825163213429](E:\alwaysUse\notes\myNotes\frontend note\vue\vue.assets\image-20240825163213429.png)



###### 一般解决方式：

使用子组件向父组件传值的方式，把子组件要改变的值通过自定义事件传给父组件，然后父组件根据这个值在父组件中的函数进行改变。



###### 一种简化的解决方式.sync：

1

![image-20240825165117387](E:\alwaysUse\notes\myNotes\frontend note\vue\vue.assets\image-20240825165117387.png)

2

![image-20240825165328883](E:\alwaysUse\notes\myNotes\frontend note\vue\vue.assets\image-20240825165328883.png)

原理：

![image-20240825165751644](E:\alwaysUse\notes\myNotes\frontend note\vue\vue.assets\image-20240825165751644.png)

相当于：

![image-20240825165816816](E:\alwaysUse\notes\myNotes\frontend note\vue\vue.assets\image-20240825165816816.png)



#### ref使用子组件的方法来实现

1）父组件

~~~vue
<!-- 故障相册 -->
<el-dialog title="提示" :visible.sync="isShowFaultAlbum" width="80%"  :append-to-body="true">
    <div style="height: 600px;">
        <FaultPhotoAlbum ref="faultAlbumRef"></FaultPhotoAlbum>
    </div>
</el-dialog>
~~~

2）

~~~js
// 跳转到故障列表页面
        skipFault(index, row, tag) {
            this.isShowFaultAlbum = true;
            this.$nextTick(() => {
                // 等待组件渲染完成，每次打开都主动调用
                this.$refs.faultAlbumRef.setInspectionTaskNum(row.inspectionTaskNum);
            });
        },
~~~

3）子组件

~~~js
    methods: {
		setInspectionTaskNum(inspectionTaskNum){
			this.inspectionTaskNum = inspectionTaskNum;
			this.isView = true;
		},
}
~~~





#### 子级向父级传递数据:$emit方法 + 使用自定义事件传值

- 子级按钮绑定了一个click点击事件 -> 
- 会调用自定义的sendData()函数 -> 
- sendData()函数中调用`this.$emit()`函数，指定触发哪一个父级的事件和传递的数据 -> 
- 触发父级的事件后调用自定义的xx函数(能够接收到子级传递的数据) -> 
- 使用子级传递过来的数据

例子：

父级组件：

~~~vue
<template>
  <div>
	<h1>{{ data }}</h1>
	<!-- 1.在父组件中给子组件hello绑定自定义事件，并使用changeData方法接收子组件的数据并使用 -->
	<hello @myevent="changeData"></hello>
    
  </div>
</template>

<script>

import hello from "./components/hello.vue"

export default {
	components:{
		hello
	},
	data(){
		return {
			data:"App.vue data"
			}
	},
	methods:{
		// 2.接收子组件的数据并使用
		changeData(data){
			console.log("App.vue: " + data);
			this.data = data;
		}
	}
}
</script>

<style>

</style>

~~~

子级组件：

~~~vue
<template>
	<!-- 3.设置子级向父级发送数据的入口 -->
	<button @click="sendData">发送数据给父级</button>
</template>

<script>
	export default{
		data(){
			return {
				childData:"data from hello.vue"
			}
		},
		methods:{
			sendData(){
				console.log("hello.vue: " + this.childData)
				// 4.调用$emit方法，触发父级的myevent事件，实现子级向父级发送数据
				this.$emit("myevent",this.childData);
			}
			
		}
	}
</script>

<style>
</style>
~~~

#### 使用Query Stringurl传值

~~~js
					// 跳转到日报详细界面
					uni.navigateTo({
						url:"/pages/log/logAudit/logDetails?index="+index+"&logs="+logStr
					})
~~~





#### 非父子级(同级)传递数据

使用store.js文件(百货商店；储存)来管理，类似于一个全局变量的东西。

公共的数据叫状态：state

store.js

~~~javascript
export default{
	// 1 定义一个类似于全局变量的东西来存放公共的数据(store.js中的state选项)
	state:{
		message:"hello vue"
	},
	// 注意:这里的函数不需要包裹在methods:{}中
	setStateMessage(str){
		this.state.message = str;
	}
	
	
	
}
~~~

结构：同级的Brother.vue与Sister.vue

Brother.vue:

~~~vue
<template>
	<div>
		<h1>
			Brother.vue
		<button @click="changeData">改变共享数据</button>
		</h1>
		<!-- 4 使用数据，自定义的data.属性名 -->
		<p>{{ state.message }}</p>
	</div>
</template>

<script>
	// 2 导入store
	import store from '../store.js'
	
	export default{
		data(){
			return {
				// 3 使用之前在本文件声明一次  注意：这样子声明：message:store.store.message ，然后去使用message是不会实时变化的!要使用store.message才可以，像上面一样
				state:store.state
			}
		},
		methods:{
			changeData(){
				console.log("准备改变")
                 // 调用方法： 文件变量.方法名
				store.setStateMessage("change")
				console.log("准备完成")
				console.log("结果: " + this.state.message)
			}
		}
		
	}
</script>

<style>
</style>
~~~

Sister.vue

~~~vue
<template>
	<div>
		<h1>Sister.vue</h1>
		<p>{{ state.message }}</p>
	</div>
</template>

<script>
	// 2 导入store
	import store from '../store.js'
	
	export default{
		data(){
			return {
				// 3 使用之前在本文件声明一次
				state:store.state
			}
		}
		
	}
</script>

<style>
</style>
~~~





#### 例子：购物车

结构：App.vue 引用 Cart.vue， Cart.vue引用Counter.vue

App.vue :

~~~vue
<template>
  <span>

    <Cart></Cart>
  </span>
</template>

<script>
import Cart from "./components/Cart.vue"
// App.vue只作为一个容器
export default {
	components:{Cart}
}
</script>

<style>

</style>
~~~



Cart.vue：

~~~vue
<template>
	<div>
		<h1>购物车</h1>
		<ul>
			<li v-for="(v,i) of fruits" :key="i">
				{{ v.name }} 单价: {{ v.pirece }}
				<!-- 父级向子级传数据 -->
				<Counter 
				:count="v.count" 
				:index="i" 
				@add="add"
				@sub="sub">
				</Counter>
			</li>
		</ul>
		<h1>总价格: {{ 
			(fruits[0].pirece * fruits[0].count) + 
			(fruits[1].pirece * fruits[1].count) + 
			(fruits[2].pirece * fruits[2].count) 
			}}</h1>
	</div>
</template>

<script>
	import Counter from './Counter.vue'
	export default{
		components:{Counter},
		data(){
			return {
				fruits:[
					{name:"苹果",pirece:3.14,count:0},
					{name:"香蕉",pirece:2.14,count:0},
					{name:"梨",pirece:1.14,count:0}
				]
			}
		},
		methods:{
			add(index){
				this.fruits[index].count++;
			},
			sub(index){
				if(this.fruits[index].count > 0){
					this.fruits[index].count--;
				}
			}
		}
		
	}
</script>

<style>
</style>
~~~

Counter.vue:

~~~vue
<template>
	<span>
		<button @click="sub">-</button>
		<span>{{ count }}</span>
		<button @click="add">+</button>
	</span>
</template>

<script>
	export default{
		props:["count","index"],
		methods:{
			add(){
				this.$emit("add",this.index)
			},
			sub(){
				this.$emit("sub",this.index)
			}
			
			
		}
		
		
	}
</script>

<style>
</style>
~~~



将系统拆分成组件：

一方面降低了功能的耦合，但是一方面也提升了数据传输难度。

这里的利弊得失需要大家在项目中不断摸索。



# 一、通用的参数传递方式VueX



## **Vuex 介绍**

Vuex 是 Vue.js 官方提供的 **全局状态管理库**，用于在 Vue 组件之间共享和管理应用的状态。它遵循 **集中式状态管理** 的思想，将应用的所有组件共享的数据存储在一个全局的 `store`（仓库）中，并提供了 **响应式的、可预测的数据流**。

------

## **Vuex 的核心概念**

Vuex 主要由以下几个部分组成：

1. **State（状态）**：存储应用的全局数据，所有组件都可以访问。
2. **Mutations（同步修改状态）**：定义修改 `state` 的方法，必须是同步操作。
3. **Actions（异步操作）**：用于处理异步请求，然后提交 `mutations` 来更新 `state`。
4. **Getters（计算属性）**：类似 Vue 组件中的 `computed`，用于从 `state` 派生出数据。
5. **Modules（模块化）**：Vuex 支持将 `store` 按模块拆分，方便管理。

------

## **Vuex vs 直接使用对象和方法**

|              | Vuex                                                     | 直接使用对象和方法                             |
| ------------ | -------------------------------------------------------- | ---------------------------------------------- |
| **数据存储** | 统一存储在 `state` 中                                    | 组件的 `data` 或 `sessionStorage/localStorage` |
| **数据修改** | 通过 `mutations` 进行修改，保证可追踪性                  | 直接修改对象，难以追踪                         |
| **数据获取** | 组件通过 `this.$store.state.xxx` 获取数据                | 组件间通过 `props` 传递或 `$emit/$on` 事件     |
| **数据共享** | 所有组件都能访问 `state`                                 | 需要手动传递数据                               |
| **异步操作** | 通过 `actions` 处理异步，再调用 `mutations` 修改 `state` | 直接使用 `axios` 请求并修改数据                |
| **调试能力** | Vue DevTools 允许查看 `state` 变化                       | 需手动打印调试                                 |

------

## **Vuex 代码示例**

### **1. 创建 Vuex Store**

```javascript
import Vue from 'vue';
import Vuex from 'vuex';

Vue.use(Vuex);

const store = new Vuex.Store({
  state: {
    count: 0, // 共享的状态
  },
  mutations: {
    increment(state) {
      state.count++; // 只能在 mutations 里修改 state
    },
  },
  actions: {
    asyncIncrement({ commit }) {
      setTimeout(() => {
        commit('increment'); // 异步操作后调用 mutation
      }, 1000);
    },
  },
  getters: {
    doubleCount(state) {
      return state.count * 2; // 计算属性
    },
  },
});

export default store;
```

### **2. 在组件中使用 Vuex**

#### **例子1：keep-alive缓存组件**

index.js:

~~~js
import Vue from 'vue';
import Vuex from 'vuex';

import {swapArrayItem} from '../components/common/utils';

Vue.use(Vuex);

const store = new Vuex.Store({
    state: {
		common: {
		  resetCacheMark: false, // 是否重新加载缓存组件的标志
           reCacheMark : false, // 是否缓存组件的标志
		},

    },

    mutations: {
		setResetCacheMark(state, resetCacheMark) {
		  state.common.resetCacheMark = resetCacheMark;
		},
        setReCacheMark(state,reCacheMark){
			state.common.reCacheMark = reCacheMark;
		},
});


export default store;
~~~



app.vue中：

~~~vue
<template>
    <div id="app">
		<keep-alive include="digital-screen" :exclude="excludedComponents">
		    <router-view v-if="isRouterAlive"></router-view>
		</keep-alive>
    </div>
</template>
<script>
	
    export default {
        name: 'App',
        provide () {
            return {
                reload: this.reload
            }
        },
        data () {
            return {
                isRouterAlive: true,
				excludedComponents: [], // 存放要排除的组件
            }
        },
		watch : {
			// 监听 Vuex 中 resetCacheMark 的变化
			'$store.state.common.resetCacheMark': function(newValue) {
				  if(newValue == true){
					console.log(`清空缓存`);
					this.resetCache();
				  }
			},
			// 登录后重新设置digital-screen可缓存
			'$store.state.common.reCacheMark': function(newValue) {
				  if(newValue == true){
					console.log(`开启缓存`);
					this.startCache();
				  }
			}
		},
        methods: {
            reload () {
                this.isRouterAlive = false
                this.$nextTick(function () {
                    this.isRouterAlive = true
                })
            },
			resetCache() {
                // 排除的优先级比include="digital-screen"要大！
				this.excludedComponents = ["digital-screen"]; // 注销时清除相应组件的缓存
			},
			startCache(){
				this.excludedComponents = [];
			},
        }
    }
</script>
~~~



缓存2：父页面中的子组件切换缓存，

~~~vue
<div class="digisc-main">
    <keep-alive include="task-list"> // 展示的组件name为task-list时才进行缓存
        <component
                   :is="currentComponent"
                   ref="bodyComponent"
                   @switch-component="sonPageSwitchComponent"
                   :initData="initData"
                   ></component>
    </keep-alive>
</div>
~~~

被缓存的组件task-list中：

~~~js
	// keep-alive相关的钩子函数
	activated() {
	    // console.log("B 组件被激活了！");
	},
	deactivated() {
       
	    if (this.$route.path !== '/login') {
			// console.log("B 组件被缓存，但暂时隐藏");
         // 跳转到登录界面了，说明是注销
	    } else {
			// console.log("注销时，清空 B 组件缓存");
			this.$destroy(); // 销毁组件，防止登出清除缓存时再次缓存
	    }
	},
~~~



注销时：

~~~js
        loginOut() {
            this.$confirm('确定退出系统?', '提示', {type:'warning'}).then(()=>{
                logout().then(
                    () => {
                        sessionStorage.clear();
						// 通知 App.vue 清空 keep-alive 缓存
						this.$store.commit('setResetCacheMark', true);
						this.$store.commit('setReCacheMark', false);
                        this.$router.push('/login');
                    },
                    () => {
                        sessionStorage.clear();
						// 通知 App.vue 清空 keep-alive 缓存
						this.$store.commit('setResetCacheMark', true);
						this.$store.commit('setReCacheMark', false);
                        this.$router.push('/login');
                    }
                );
            });
        },
~~~

登录时：

~~~js
		onLoginSucc(respBody, inputPasswd=null, jumpToDashBoard=true) {
			// ...省略无关代码
			// 登录成功，设置不清除缓存的组件
			this.$store.commit('setResetCacheMark', false);
			this.$store.commit('setReCacheMark', true);

		},
~~~





#### 例子2：语法糖

```vue
<template>
  <div>
    <p>Count: {{ count }}</p>
    <p>Double Count: {{ doubleCount }}</p>
    <button @click="increment">增加</button>
    <button @click="asyncIncrement">异步增加</button>
  </div>
</template>

<script>
import { mapState, mapMutations, mapActions, mapGetters } from 'vuex';

export default {
  computed: {
    ...mapState(['count']), // 访问 state
    ...mapGetters(['doubleCount']), // 访问 getters
  },
  methods: {
    ...mapMutations(['increment']), // 访问 mutations
    ...mapActions(['asyncIncrement']), // 访问 actions
  },
};
</script>
```

------

## **Vuex 的优势**

✅ **集中管理状态**：所有组件共享的数据都存储在 `state`，避免了组件间数据混乱。
 ✅ **易于调试**：Vue DevTools 提供了时间旅行调试，能清晰查看 `state` 变化。
 ✅ **组件解耦**：不需要 `props` 逐级传递，任何组件都能直接访问 `state`。
 ✅ **可预测性**：通过 `mutations` 进行修改，保证 `state` 变化可跟踪。

------

## **何时使用 Vuex？**

### **适合使用 Vuex 的场景**

- 需要 **多个组件共享** 相同的数据，比如用户信息、权限、主题设置等。
- 组件之间通信 **过于复杂**，`props` 和 `emit` 不能很好地管理数据流。
- 需要 **全局状态管理**，例如购物车、登录状态、后台管理等。

### **不适合使用 Vuex 的场景**

- 仅在 **单个组件内部** 维护数据的情况，比如 `isModalOpen` 这样的局部状态。
- 小型项目，使用 `props` 和 `$emit` 足够传递数据，不需要 Vuex。

------

## **总结**

1. Vuex 适用于 **需要在多个组件之间共享数据** 的场景，比如登录状态、用户权限等。
2. Vuex 通过 `state` 统一管理数据，`mutations` 负责同步修改，`actions` 处理异步逻辑，`getters` 计算派生数据。
3. 与普通对象和方法相比，Vuex 提供了 **更好的数据追踪、调试能力和组件解耦**，适用于复杂的前端项目。

如果你的项目状态管理较复杂，使用 Vuex 是一个不错的选择！ 🚀



这段代码是 Vue 组件中使用 Vuex 的写法，主要通过 `mapState`、`mapMutations`、`mapActions` 和 `mapGetters` 将 Vuex `store` 中的状态、方法等映射到组件的 `computed` 和 `methods` 里，方便在模板 (`template`) 中直接使用。
 下面我们来逐步解析这段代码的作用。

------

## **代码解析**

```javascript
import { mapState, mapMutations, mapActions, mapGetters } from 'vuex';
```

🔹 `mapState`、`mapMutations`、`mapActions` 和 `mapGetters` 是 Vuex 提供的辅助函数，用于简化 Vue 组件访问 `store` 的代码。

```javascript
export default {
  computed: {
    ...mapState(['count']), // 访问 Vuex state
    ...mapGetters(['doubleCount']), // 访问 Vuex getters
  },
  methods: {
    ...mapMutations(['increment']), // 访问 Vuex mutations
    ...mapActions(['asyncIncrement']), // 访问 Vuex actions
  },
};
```

这部分代码是 Vue 组件的 `computed`（计算属性）和 `methods`（方法）部分，使用了 Vuex 的辅助函数来映射 `store` 中的数据和方法。

------

## **各个辅助函数的作用**

### **1. `mapState` - 绑定 `state` 到组件的 `computed`**

```javascript
computed: {
  ...mapState(['count'])
}
```

👆 这行代码等价于：

```javascript
computed: {
  count() {
    return this.$store.state.count;
  }
}
```

💡 **作用**：

- 让 `count` 直接变成组件的计算属性 (`computed`)，可以在 `template` 里 `{{ count }}` 直接使用。

------

### **2. `mapGetters` - 绑定 `getters` 到组件的 `computed`**

```javascript
computed: {
  ...mapGetters(['doubleCount'])
}
```

👆 这行代码等价于：

```javascript
computed: {
  doubleCount() {
    return this.$store.getters.doubleCount;
  }
}
```

💡 **作用**：

- ```
  doubleCount
  ```

   是 Vuex 

  ```
  getters
  ```

   中计算派生的数据，比如：

  ```javascript
  getters: {
    doubleCount(state) {
      return state.count * 2;
    }
  }
  ```

- 组件可以直接使用 `{{ doubleCount }}` 显示 `count` 的两倍。

------

### **3. `mapMutations` - 绑定 `mutations` 到组件的方法**

```javascript
methods: {
  ...mapMutations(['increment'])
}
```

👆 这行代码等价于：

```javascript
methods: {
  increment() {
    this.$store.commit('increment');
  }
}
```

💡 **作用**：

- 让 `increment` 方法变成 Vue 组件的方法，点击按钮时可以 `this.increment()` 调用。

- ```
  increment
  ```

   方法实际上是触发 Vuex 的 

  ```
  mutations
  ```

  ：

  ```javascript
  mutations: {
    increment(state) {
      state.count++;
    }
  }
  ```

------

### **4. `mapActions` - 绑定 `actions` 到组件的方法**

```javascript
methods: {
  ...mapActions(['asyncIncrement'])
}
```

👆 这行代码等价于：

```javascript
methods: {
  asyncIncrement() {
    this.$store.dispatch('asyncIncrement');
  }
}
```

💡 **作用**：

- 让 `asyncIncrement` 方法变成 Vue 组件的方法，可以 `this.asyncIncrement()` 调用。

- ```
  asyncIncrement
  ```

   方法是 Vuex 

  ```
  actions
  ```

   里的异步操作，比如：

  ```javascript
  actions: {
    asyncIncrement({ commit }) {
      setTimeout(() => {
        commit('increment'); // 延迟 1 秒后调用 mutations 修改 state
      }, 1000);
    }
  }
  ```



------

## **总结**

| Vuex 方式                        | 作用                            | 对应 Vue 代码                                                |
| -------------------------------- | ------------------------------- | ------------------------------------------------------------ |
| `mapState(['count'])`            | 绑定 `state` 到组件的计算属性   | `computed: { count() { return this.$store.state.count } }`   |
| `mapGetters(['doubleCount'])`    | 绑定 `getters` 到组件的计算属性 | `computed: { doubleCount() { return this.$store.getters.doubleCount } }` |
| `mapMutations(['increment'])`    | 绑定 `mutations` 到组件的方法   | `methods: { increment() { this.$store.commit('increment') } }` |
| `mapActions(['asyncIncrement'])` | 绑定 `actions` 到组件的方法     | `methods: { asyncIncrement() { this.$store.dispatch('asyncIncrement') } }` |

🚀 **优势：**

1. **代码简洁**：不需要手写 `this.$store.state.xxx`，用 `mapState` 直接映射。
2. **增强可读性**：`mapMutations`、`mapActions` 让 `store` 逻辑更清晰。
3. **方便扩展**：如果 `store` 里有多个 `state`、`mutations`，可以轻松映射多个。

这样，你就能高效地使用 Vuex 了！🎯



# 二、

### 计算属性与侦听器

#### computed:计算属性

- computed:计算属性
- watch:侦听器（监听器)

computed使用场景：

![image-20240804212625464](C:\Users\Gloom\AppData\Roaming\Typora\typora-user-images\image-20240804212625464.png)

与data类似：

![image-20240804212806582](C:\Users\Gloom\AppData\Roaming\Typora\typora-user-images\image-20240804212806582.png)

例子：拼接姓名

使用data:

![image-20240804213526891](C:\Users\Gloom\AppData\Roaming\Typora\typora-user-images\image-20240804213526891.png)

使用computed:计算属性

![image-20240804213612022](C:\Users\Gloom\AppData\Roaming\Typora\typora-user-images\image-20240804213612022.png)



作用：

在 Vue 中，计算属性（computed property）是基于数据状态动态计算并缓存的属性。与方法不同，计算属性的结果会被缓存，当依赖的数据发生变化时才会重新计算，因此更适合处理复杂的计算或频繁访问的值。

### 计算属性的特点
1. **缓存**：计算属性的结果会被缓存，只有在依赖的数据发生变化时才会重新计算。这可以减少不必要的计算，提高性能。
2. **响应式**：计算属性是响应式的，当依赖的数据改变时，计算属性的值也会更新。

### 示例解释
在这个例子中，我们通过计算属性 `computedTotalNum` 来计算 `allInvoiceArray` 数组中的总金额。

```javascript
computedTotalNum() {
    let total = this.allInvoiceArray.reduce((accumulator, currentItem) => {
        // 使用 BigNumber 进行精确的浮点运算
        return new BigNumber(accumulator).plus(new BigNumber(currentItem.totalMoney));
    }, new BigNumber(0));
    // 返回保留两位小数的字符串
    return total.toFixed(2);
}
```

#### 代码解析
1. **reduce 方法**：`reduce` 是 JavaScript 数组的方法，用于对数组中的每一项进行累加操作。在这个例子中，`accumulator` 是累加器，`currentItem` 是当前数组项，即每个发票的金额。
   
2. **BigNumber**：使用 `BigNumber` 是为了避免 JavaScript 中的浮点数运算精度问题，尤其在金额计算中更为重要。每个金额都转换为 `BigNumber` 类型，然后相加以获得精确结果。

3. **保留小数**：`toFixed(2)` 将总金额格式化为保留两位小数的字符串，以便在显示时符合货币格式的要求。

#### 优势
通过计算属性 `computedTotalNum`，我们可以直接在模板中使用 `{{ computedTotalNum }}` 来显示总金额值，而无需每次都重新计算；当 `allInvoiceArray` 中的数据发生变化时，`computedTotalNum` 会自动更新，实现响应式数据绑定。



#### watch:侦听器（监听器)

##### 侦听一个属性的变化：

![image-20240804214359760](C:\Users\Gloom\AppData\Roaming\Typora\typora-user-images\image-20240804214359760.png)



~~~js
    watch: {
		// 新值与旧值都可获取到
      value(newVal, oldVal) {
        let param = {
                    inspectionTaskNum: this.inspectionTaskNum ? this.inspectionTaskNum : '',
                    failureType: newVal == true ? this.failureType : '',
        };

      },


    },
~~~





##### 监听路由路径的变化



![image-20240805101910028](C:\Users\Gloom\AppData\Roaming\Typora\typora-user-images\image-20240805101910028.png)

例子：

~~~js
		watch: {
		    '$route.path': function(newPath) {
		      if (newPath === '/pages/log/logAudit/logAudit') {
		        this.resetLogBottom();
		      }
		    }
		  },
		methods: {
			preventTouchMove(){},
			resetLogBottom() {
			      console.log("store.logBottom = " + this.store.logBottom);
				  // qtypeof store.setStoreLogBottom = function,typeof this.store.setStoreLogBottom = undefindes
				  console.log("typeof store.setStoreLogBottom = " + typeof store.setStoreLogBottom);
			      store.setStoreLogBottom(false);
			      console.log("重置LogBottom成功");
			    }
~~~

logAudit.js

~~~js
export default{
	store:{
			logBottom:false
		},
	setStoreLogBottom(value){
		console.log("参数：" + value);
		console.log("修改之前：this.store.logBottom = " + this.store.logBottom);
		this.store.logBottom = value;
		console.log("修改之后:this.store.logBottom = " + this.store.logBottom);
	}
}
~~~





#### 比较

![image-20240804214656602](C:\Users\Gloom\AppData\Roaming\Typora\typora-user-images\image-20240804214656602.png)







### 生命周期钩子(函数)

作用：页面加载的时候，主动执行某些程序。

![image-20240804220904683](C:\Users\Gloom\AppData\Roaming\Typora\typora-user-images\image-20240804220904683.png)

执行是有顺序的：

![image-20240804215527018](C:\Users\Gloom\AppData\Roaming\Typora\typora-user-images\image-20240804215527018.png)



#### 各个生命周期函数解释



视频演示：https://www.bilibili.com/video/BV1SE411H7CY?p=11&vd_source=d6367c1fc21883823f1fb738f86ef26e  p12



~~~html
<!DOCTYPE html>
<html lang="en" xmlns:v-on="http://www.w3.org/1999/xhtml">

<head>
  <meta charset="UTF-8">
  <title>vue生命周期函数</title>
</head>

<body>
  <div id="app">
    <span id="sp"> {{ msg }} </span>
    <input type="button" value="改变data的值" @click="changeData">
  </div>
  <!-- 引入Vue  -->
  <script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
  <script>
    const app = new Vue({
      el: "#app",
      data: {
        msg: "hello Vue.js!",
      },
      methods: {
        changeData() {
          // this.msg = "Vue.js niubility!";
          this.msg = Math.random();
        }
      },
      // ====================初始化阶段====================
      // 1.生命周期中第一个函数,该函数在执行时Vue实例仅仅完成了自身事件的绑定和生命周期函数的初始化工作,Vue实例中还没有 Data el methods相关属性
      beforeCreate() {
        console.log("beforeCreate: " + this.msg);
      },
      // 2.生命周期中第二个函数,该函数在执行时Vue实例已经初始化了data属性和methods中相关方法
      created() {
        console.log("created:" + this.msg);
      },
      // 3.生命周期中第三个函数,该函数在执行时Vue将El中指定作用范围作为模板编译
      beforeMount() {
        console.log("beforeMount: " + document.getElementById("sp").innerText);
      },
      // 4.生命周期中第四个函数,该函数在执行过程中,已经将数据渲染到界面中并且已经更新页面
      mounted() {
        console.log("Mounted: " + document.getElementById("sp").innerText);
      },
      // ====================运行阶段====================
      // 5.生命周期中第五个函数,该函数是data中数据发生变化时执行 这个事件执行时仅仅是Vue实例中data数据变化页面显示的依然是原始数据
      beforeUpdate() {
        console.log("beforeUpdate-vue: " + this.msg);
        console.log("beforeUpdate-dom: " + document.getElementById("sp").innerText);
      },
      // 6.生命周期中第六个函数,该函数执行时data中数据发生变化,页面中数据也发生了变化  页面中数据已经和data中数据一致
      updated() {
        console.log("updated-vue: " + this.msg);
        console.log("updated-dom: " + document.getElementById("sp").innerText);
      },
      // ====================销毁阶段====================
      // 7.生命周期第七个函数,该函数执行时,Vue中所有数据 methods componet 都没销毁
      beforeDestory() { },
      // 8.生命周期的第八个函数,该函数执行时,Vue实例彻底销毁
      destoryed() { }
    });
  </script>
</body>

</html>

~~~



#### 使用例子：网站加载

![image-20240804220503105](C:\Users\Gloom\AppData\Roaming\Typora\typora-user-images\image-20240804220503105.png)

![image-20240804220530042](C:\Users\Gloom\AppData\Roaming\Typora\typora-user-images\image-20240804220530042.png)



- 1.插槽 slot
- 2.DOM操作
- 3.过滤器

### 插槽 slot

#### 简单使用：

![image-20240804222343019](C:\Users\Gloom\AppData\Roaming\Typora\typora-user-images\image-20240804222343019.png)

![image-20240804222517752](C:\Users\Gloom\AppData\Roaming\Typora\typora-user-images\image-20240804222517752.png)



#### 具名插槽 v-slot

![image-20240804222755859](C:\Users\Gloom\AppData\Roaming\Typora\typora-user-images\image-20240804222755859.png)

![image-20240804223021093](C:\Users\Gloom\AppData\Roaming\Typora\typora-user-images\image-20240804223021093.png)

![image-20240804223136440](C:\Users\Gloom\AppData\Roaming\Typora\typora-user-images\image-20240804223136440.png)

#### 应用

![image-20240804223315234](C:\Users\Gloom\AppData\Roaming\Typora\typora-user-images\image-20240804223315234.png)



### DOM操作

![image-20240804225429946](C:\Users\Gloom\AppData\Roaming\Typora\typora-user-images\image-20240804225429946.png)

![image-20240804224127851](C:\Users\Gloom\AppData\Roaming\Typora\typora-user-images\image-20240804224127851.png)

![image-20240804224252840](C:\Users\Gloom\AppData\Roaming\Typora\typora-user-images\image-20240804224252840.png)



### 过滤器(数据处理)

![image-20240804224317917](C:\Users\Gloom\AppData\Roaming\Typora\typora-user-images\image-20240804224317917.png)

例子：

![image-20240804224812129](C:\Users\Gloom\AppData\Roaming\Typora\typora-user-images\image-20240804224812129.png)

效果：

![image-20240804224837836](C:\Users\Gloom\AppData\Roaming\Typora\typora-user-images\image-20240804224837836.png)



### 表单



#### 数据双向绑定

![image-20240804225638726](C:\Users\Gloom\AppData\Roaming\Typora\typora-user-images\image-20240804225638726.png)

![image-20240804230221080](C:\Users\Gloom\AppData\Roaming\Typora\typora-user-images\image-20240804230221080.png)

![image-20240804230135693](C:\Users\Gloom\AppData\Roaming\Typora\typora-user-images\image-20240804230135693.png)

#### 使用vue来提交表单

数据定义：

![image-20240804232355730](C:\Users\Gloom\AppData\Roaming\Typora\typora-user-images\image-20240804232355730.png)

#### from与文本框：

![image-20240804230951168](C:\Users\Gloom\AppData\Roaming\Typora\typora-user-images\image-20240804230951168.png)



#### 密码框与下拉单选框：

![image-20240804232158924](C:\Users\Gloom\AppData\Roaming\Typora\typora-user-images\image-20240804232158924.png)



#### 圆形单选框与方块多选框

![image-20240804232721167](C:\Users\Gloom\AppData\Roaming\Typora\typora-user-images\image-20240804232721167.png)

结果：

![image-20240804232507864](C:\Users\Gloom\AppData\Roaming\Typora\typora-user-images\image-20240804232507864.png)



### 数据交互

![image-20240805092147513](C:\Users\Gloom\AppData\Roaming\Typora\typora-user-images\image-20240805092147513.png)



#### 例子：

后端程序：

![image-20240805094453460](C:\Users\Gloom\AppData\Roaming\Typora\typora-user-images\image-20240805094453460.png)

![image-20240805094723090](C:\Users\Gloom\AppData\Roaming\Typora\typora-user-images\image-20240805094723090.png)



前端程序：

![image-20240805093624838](C:\Users\Gloom\AppData\Roaming\Typora\typora-user-images\image-20240805093624838.png)



![image-20240805093716959](C:\Users\Gloom\AppData\Roaming\Typora\typora-user-images\image-20240805093716959.png)

![image-20240805093753862](C:\Users\Gloom\AppData\Roaming\Typora\typora-user-images\image-20240805093753862.png)

效果:

![image-20240805094847100](C:\Users\Gloom\AppData\Roaming\Typora\typora-user-images\image-20240805094847100.png)



### 路由

![image-20240805094930841](C:\Users\Gloom\AppData\Roaming\Typora\typora-user-images\image-20240805094930841.png)

创建路由模板文件



#### 使用



##### 快速入门

案例：

~~~vue
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>路由的基本使用</title>
</head>
<body>
<div id="app">
    <!--4、在页面中显示路由的组件-->
    <router-view></router-view>
    <!--5、根据链接切换路由组件-->
    <a href="#/login">点我登录</a>
    <a href="#/register">点我注册</a>
</div>
</body>
</html>
<script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
<script src="https://unpkg.com/vue-router@3.3.4/dist/vue-router.js"></script>
<script>

    // 1、创建组件对象
    const login = {
        template: "<h1>登录</h1>"
    };
    const register = {
        template: "<h1>注册</h1>"
    };

    // 2、创建路由对象
    const router = new VueRouter({
        routes: [ // 定义路由对象的规则
            // path:设置路由的路径, component:路径对应的组件
            {path: "/login", component: login},
            {path: "/register", component: register}
        ]
    });
    
    const app = new Vue({
        el: "#app",
        data: {},
        methods: {},
        router: router // 3、在vue实例中注册路由对象 注册后浏览器url最后会从 # 变为 
    });
</script>

~~~





##### 使用标签来切换路径

1）配置展示效果：

![image-20240805100332732](C:\Users\Gloom\AppData\Roaming\Typora\typora-user-images\image-20240805100332732.png)

router-link：

![image-20240911180423840](E:\alwaysUse\notes\myNotes\frontend note\vue\vue.assets\image-20240911180423840.png)

效果：

![image-20240805100442066](C:\Users\Gloom\AppData\Roaming\Typora\typora-user-images\image-20240805100442066.png)

2）配置路径与要展示组件的对应关系：

![image-20240805095853022](C:\Users\Gloom\AppData\Roaming\Typora\typora-user-images\image-20240805095853022.png)

或者：

![image-20240821172851124](E:\alwaysUse\notes\myNotes\frontend note\vue\vue.assets\image-20240821172851124.png)

##### $router：



###### 切换路径

![image-20240823105547035](E:\alwaysUse\notes\myNotes\frontend note\vue\vue.assets\image-20240823105547035.png)



###### 获取当前路径



![image-20240823113203537](E:\alwaysUse\notes\myNotes\frontend note\vue\vue.assets\image-20240823113203537.png)

###### 传统方式获取参数 .query

![image-20240911181619680](E:\alwaysUse\notes\myNotes\frontend note\vue\vue.assets\image-20240911181619680.png)



###### restful方式传递参数 .params

![image-20240911181848483](E:\alwaysUse\notes\myNotes\frontend note\vue\vue.assets\image-20240911181848483.png)



##### 配置默认路由

![image-20240911180605257](E:\alwaysUse\notes\myNotes\frontend note\vue\vue.assets\image-20240911180605257.png)



##### 嵌套路由



~~~html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <title>路由中传递参数</title>
</head>

<body>
<div id="app">
    <router-link to="/product">商品管理</router-link>
    <router-view></router-view>
</div>
<template id="product">
    <div>
        <h1>商品管理</h1>
        <router-link to="/product/add">商品添加</router-link>
        <router-link to="/product/edit">商品编辑</router-link>
        <router-view></router-view>
    </div>
</template>
</body>
</html>
<script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
<script src="https://unpkg.com/vue-router@3.3.4/dist/vue-router.js"></script>
<script>
    // 声明最外层和内层组件对象
    const product = {
        template: '#product'
    };
    const add = {
        template: "<h4>商品添加</h4>"
    };
    const edit = {
        template: "<h4>商品编辑</h4>"
    };
    // 创建含有路由对象的路由对象(嵌套路由), 通过children嵌套
    const router = new VueRouter({
        routes: [
            {
                path: "/product",
                component: product,
                children: [
                    // 请求"/product/add路径后才可以访问add组件"
                    {path: "add", component: add},
                    {path: "edit", component: edit},
                ]
            },
        ]
    });

    const app = new Vue({
        el: "#app",
        data: {},
        methods: {},
        router // 注册路由
    });
</script>

~~~



https://www.bilibili.com/video/BV1SE411H7CY?p=10&vd_source=d6367c1fc21883823f1fb738f86ef26e

mark1 p



#### vue3写法:





#### 本地（浏览器）存储数据

#### 博客 + 登录例子：

效果：

![image-20240805104830246](C:\Users\Gloom\AppData\Roaming\Typora\typora-user-images\image-20240805104830246.png)

##### 登录：

![image-20240805101044227](C:\Users\Gloom\AppData\Roaming\Typora\typora-user-images\image-20240805101044227.png)

~~~vue
<script>
    // 登录后跳转首页
    this.$router.push("/")
</script>
~~~



##### 注销：

![image-20240805102215971](C:\Users\Gloom\AppData\Roaming\Typora\typora-user-images\image-20240805102215971.png)



#### 导航守卫



![image-20240805102704223](C:\Users\Gloom\AppData\Roaming\Typora\typora-user-images\image-20240805102704223.png)

没有写next()进行放行就会被拦截。



###### 登录权限控制：

![image-20240805103241319](C:\Users\Gloom\AppData\Roaming\Typora\typora-user-images\image-20240805103241319.png)



###### 登录后才显示欢迎用户的文字，没有登录则不显示：

![image-20240805104754728](C:\Users\Gloom\AppData\Roaming\Typora\typora-user-images\image-20240805104754728.png)

![image-20240805104556102](C:\Users\Gloom\AppData\Roaming\Typora\typora-user-images\image-20240805104556102.png)



### ElementUl

一个基于vue的UI框架

ElementUl： https://element.eleme.cn/#/zh-CN

![image-20240805110146486](C:\Users\Gloom\AppData\Roaming\Typora\typora-user-images\image-20240805110146486.png)

![image-20240805110350416](C:\Users\Gloom\AppData\Roaming\Typora\typora-user-images\image-20240805110350416.png)



组件示例

- 1.按钮
- 2.表单
- 3.表格

效果：

![image-20240805115704613](C:\Users\Gloom\AppData\Roaming\Typora\typora-user-images\image-20240805115704613.png)

例子：

![image-20240805112432057](C:\Users\Gloom\AppData\Roaming\Typora\typora-user-images\image-20240805112432057.png)



### 配置文件



![image-20240805133954184](C:\Users\Gloom\AppData\Roaming\Typora\typora-user-images\image-20240805133954184.png)

#### 使用配置文件中定义的数据



![image-20240805134813544](C:\Users\Gloom\AppData\Roaming\Typora\typora-user-images\image-20240805134813544.png)

![image-20240805134410619](C:\Users\Gloom\AppData\Roaming\Typora\typora-user-images\image-20240805134410619.png)



### 前端请求后端

#### 使用axios的$http命令

安装：

1

![image-20240823112655394](E:\alwaysUse\notes\myNotes\frontend note\vue\vue.assets\image-20240823112655394.png)

配置：

1

![image-20240823112834931](E:\alwaysUse\notes\myNotes\frontend note\vue\vue.assets\image-20240823112834931.png)



使用：

![image-20240823112540413](E:\alwaysUse\notes\myNotes\frontend note\vue\vue.assets\image-20240823112540413.png)

#### 基本使用



![image-20240907182322149](E:\alwaysUse\notes\myNotes\frontend note\vue\vue.assets\image-20240907182322149.png)



#### axios发送并发请求

![image-20240907182231366](E:\alwaysUse\notes\myNotes\frontend note\vue\vue.assets\image-20240907182231366.png)





# 语法糖



## Vue函数的两种写法

![image-20240907170612290](E:\alwaysUse\notes\myNotes\frontend note\vue\vue.assets\image-20240907170612290.png)







# Vue的使用例子

**会查看官方文档完成需求即可。**

## 1 三更springBoot课程的案例

### 1.1请求后台接口  把接受到的数据渲染展示在页面中 vue : el、data、methods、created()

~~~html
<div id="app">
    <table class="table table-striped table-bordered table-hover">
    <thead>
        <tr>
            <th>学号</th>
            <th>姓名</th>
            <th>年龄</th>
            <th>地址</th>
        </tr>
    </thead>
    <tbody>
        <!-- 遍历users数组-->
        <tr v-for="user in users">
            <td>{{user.id}}</td>
            <td>{{user.username}}</td>
            <td>{{user.age}}</td>
            <td>{{user.address}}</td>
        </tr>

    </tbody>
</table>
</div>

	<!-- 导入vue与axios-->
    <script src="https://cdn.jsdelivr.net/npm/vue@2/dist/vue.js"></script>
    <script src="https://unpkg.com/axios/dist/axios.min.js"></script>

   <script>
      var v = new Vue({
          <!-- 对id=app的标签进行控制-->
          el:"#app",
          <!-- 创建一个存放数据的数组-->
          data:{
              users:[]
          },
          <!-- id=app的标签创建时就会调用created()方法-->
          created(){
              this.findAll();
          },
          methods:{
              <!-- 定义方法-->
              findAll(){
                  //请求后台接口  把接受到的数据命名为res，渲染展示在页面中
                  axios.get("http://localhost/user/findAll").then((res)=>{
                      console.log(res);
                      //判断是否成功
                      if(res.data.code==200){
                          //如果成功，把数据赋值给this.users
                          this.users = res.data.data;
                      }

                  })
              }
          }
      });
   </script>
~~~

### 1.2 登录页面

~~~html
<form>
    <!-- 使用v-model把输入框的数据绑定给user.username -->
    <input id="exampleInputEmail1" v-model="user.username" type="email" placeholder="请输入用户名" class="form-control">
    <input id="exampleInputPassword1" v-model="user.password"  type="password" placeholder="请输入密码" class="form-control">
    <!-- 点击登录按钮时调用handleLogin()方法 -->
    <button type="submit" class="btn btn-block btn-primary" @click="handleLogin()">登录</button>
</form>

	<script src="https://cdn.jsdelivr.net/npm/vue@2/dist/vue.js"></script>
    <script src="https://unpkg.com/axios/dist/axios.min.js"></script>
   <script>
       var v = new Vue({
           el:"#app",
           data:{
               // 定义一个空的user对象
               user:{}
           },
           methods:{
               handleLogin(){
                   //请求后台登录接口  发起一个异步的post请求，把this.user对象写入请求体中
                   
                   axios.post("http://localhost/sys_user/login",this.user).then((res)=>{
                       // console.log(res);
                       //判断是否成功
                       if(res.data.code==200){
                           //登录成功
                           // alert("登录成功");
                           //存储token
                           localStorage.setItem("token",res.data.data.token);
                           //跳转页面到index.html
                           location.href = "/index.html";
                       }else{
                           //登录失败
                           alert(res.data.msg);
                       }

                   })
               }
           }
       });
   </script>
~~~





## 动态类Class绑定



## 例子

```
vue如何实现当isWhole是false时，就让<view class="invoiceCards">中的invoiceCards类的background-color属性改为rgb(252, 195, 31);
```





在 Vue 中，可以通过绑定 `class` 或使用 `:style` 动态绑定 `background-color` 样式来实现这一效果。假设你的 `isWhole` 是一个 Boolean 值，可以使用如下代码实现条件样式变化：

### 方案1: 动态绑定类



#### 动态绑定单个class:

动态绑定id也是相同做法。

~~~vue
<view :class="isShowInvoiceCategorize ? 'haveInvoiceNumberContent' : 'haveInvoiceNumberContent-categorize'">
</view>
~~~





#### 动态绑定多个class:

```html
<!-- :class指令也可以与普通的class共存 -->
<view class="test" :class="{'invoiceCards': true, 'highlight-background': !isWhole}">
  <!-- 其他内容 -->
</view>
```

```css
.invoiceCards {
  /* 默认样式 */
  background-color: initial;
}

.highlight-background {
  background-color: rgb(252, 195, 31);
}

.test{
    color: black;
}
```



#### 总结：

![image-20240825122116978](Vue2.assets/image-20240825122116978.png)



### 方案2: 动态绑定内联样式

```html
<view class="invoiceCards" :style="{ backgroundColor: !isWhole ? 'rgb(252, 195, 31)' : '' }">
  <!-- 其他内容 -->
</view>
```



![image-20240825121126136](Vue2.assets/image-20240825121126136.png)



实际使用：

~~~vue
<view class="oneColumn" :style="{ transform: invoiceFolderObj.over == false ? 'translate(-7vw, 0vh)' : getClickedFinishButtonStyle(invoiceFolderObj.totalMoney) }">
</view>
~~~



js:

~~~js
			// 根据发票金额计算移动端发票金额与已完结按钮布局所需要的大小
			getClickedFinishButtonStyle(totalMoney){
				if (!totalMoney) return `translate(0vw, 0vh)`; // 处理空值或未定义的情况
				let strLen = totalMoney.length;
				let baseNum = strLen < 8 ? (8 - strLen) * 2 : 0;
				return `translate(-${baseNum}vw, 0vh)`;
			},
~~~









## 一、 使用scoped 



为什么要使用scoped ，举一个例子说明



使用 `scoped` 样式的主要原因是 **局部化样式**，即保证样式只作用于当前组件，避免样式泄漏或影响到其他组件。这在开发大型应用时非常有用，特别是当组件化开发时，能够确保每个组件的样式不会互相干扰。

### 举个例子，说明 `scoped` 的作用：

假设我们有两个组件：`Header.vue` 和 `Footer.vue`，它们分别代表页面的头部和底部。在这两个组件中，我们都要使用 `.title` 类来设置标题的样式。假设没有 `scoped`，如果两者都使用 `.title`，它们的样式可能会互相干扰，导致不可预期的效果。

### 1. **没有 `scoped` 的情况**：

#### `Header.vue`：

```vue
<template>
  <div class="header">
    <h1 class="title">页面头部</h1>
  </div>
</template>

<script>
export default {
  name: 'Header',
};
</script>

<style>
.title {
  color: blue;
}
</style>
```

#### `Footer.vue`：

```vue
<template>
  <div class="footer">
    <h1 class="title">页面底部</h1>
  </div>
</template>

<script>
export default {
  name: 'Footer',
};
</script>

<style>
.title {
  color: red;
}
</style>
```

### 结果：

- 由于没有 `scoped`，`Header.vue` 中的 `.title` 和 `Footer.vue` 中的 `.title` 都是全局样式，它们会互相影响。
- 最终，你可能看到头部标题是红色的（`Footer.vue` 的样式覆盖了 `Header.vue` 的样式），而不是蓝色的，这显然不是你希望的效果。

### 2. **使用 `scoped` 的情况**：

现在我们给每个组件的样式加上 `scoped`，这样每个组件的样式就会局部作用于该组件，不会影响到其他组件。

#### `Header.vue`（使用了 `scoped`）：

```vue
<template>
  <div class="header">
    <h1 class="title">页面头部</h1>
  </div>
</template>

<script>
export default {
  name: 'Header',
};
</script>

<style scoped>
.title {
  color: blue;
}
</style>
```

#### `Footer.vue`（使用了 `scoped`）：

```vue
<template>
  <div class="footer">
    <h1 class="title">页面底部</h1>
  </div>
</template>

<script>
export default {
  name: 'Footer',
};
</script>

<style scoped>
.title {
  color: red;
}
</style>
```

### 结果：

- 使用了 `scoped` 后，`Header.vue` 和 `Footer.vue` 中的 `.title` 类不会互相影响。它们分别会显示为蓝色和红色，完全按照各自组件的样式来渲染。
- `scoped` 保证了样式仅作用于当前组件，避免了样式冲突。

### 为什么使用 `scoped`：

- **局部化样式**：确保样式只作用于当前组件，不会影响到其他组件，避免样式污染。
- **可维护性**：在开发复杂项目时，每个组件都可以独立管理自己的样式，减少样式间的冲突。
- **增强可读性**：使用 `scoped` 后，样式只影响当前组件的 HTML 结构，便于其他开发人员理解和修改。

### 总结：

使用 `scoped` 主要是为了 **局部化样式**，确保样式只作用于当前组件，避免不同组件之间的样式干扰。当你需要在 Vue 组件中封装样式，使得它们与其他组件的样式互不干扰时，`scoped` 是非常有用的。





## 二、 深度选择器 >>>

举个例子来帮助你更好地理解 `>>>` 选择器的用途。



好的，举个例子来帮助你更好地理解 `>>>` 选择器的用途。

### 场景描述：

假设你有一个父组件，它包含了一个子组件。在父组件中，你想给子组件的内部元素应用一些样式，但是父组件的样式被 `scoped` 限制了，无法影响到子组件的元素。

### 例子：

#### 1. **父组件 (`Parent.vue`)**：

```vue
<template>
  <div class="parent">
    <h1>欢迎来到管理平台</h1>
    <!-- 引入子组件 -->
    <ChildComponent />
  </div>
</template>

<script>
import ChildComponent from './ChildComponent.vue';

export default {
  components: {
    ChildComponent,
  },
};
</script>

<style scoped>
/* 父组件样式 */
.parent h1 {
  color: blue;
}

/* 尝试给子组件的内部元素应用样式 */
.parent >>> .child-button {
  background-color: red; /* 希望子组件的按钮背景是红色 */
}
</style>
```

#### 2. **子组件 (`ChildComponent.vue`)**：

```vue
<template>
  <div class="child">
    <button class="child-button">点击我</button>
  </div>
</template>

<script>
export default {
  name: 'ChildComponent',
};
</script>

<style scoped>
/* 子组件样式 */
.child-button {
  background-color: green; /* 子组件的按钮背景是绿色 */
}
</style>
```

### 解释：

- 在父组件 `Parent.vue` 中，我们希望给子组件中的 `.child-button` 元素应用样式，改变它的背景颜色为红色。
- 由于父组件使用了 `scoped`，它的样式默认只会影响父组件中的元素，不能直接影响到子组件的 `.child-button` 元素。
- 使用 `>>>` 选择器后，父组件的样式就能够突破 `scoped` 限制，直接作用于子组件中的 `.child-button` 元素。

### 结果：

- 子组件 `ChildComponent.vue` 中的 `.child-button` 默认是绿色的。
- 但在父组件 `Parent.vue` 中，我们通过 `>>>` 选择器强制给子组件 `.child-button` 设置了红色背景。

如果不使用 `>>>`，父组件的样式将无法影响到子组件，`.child-button` 仍然会保持绿色背景。

### 总结：

- **父组件样式无法影响子组件**：如果子组件的样式被 `scoped` 限制了，父组件的样式默认是无法穿透的。
- **使用 `>>>` 可以突破作用域**：通过 `>>>` 选择器，父组件可以影响到子组件的元素样式，即使子组件使用了 `scoped` 限制。

这样，在实际开发中，如果你需要让父组件控制子组件的样式，就可以使用 `>>>`。





## 三、全局监听回车事件



是的，你可以全局监听回车事件来实现这个功能。通过在页面加载时绑定全局的 `keyup` 事件来监听回车键，无论焦点在哪个输入框上，都能够触发相应的登录操作。

你可以在 Vue 的生命周期钩子 `mounted` 中添加全局的 `keyup` 事件监听，在回车键被按下时执行登录操作。

### 实现步骤：

1. **在 `mounted` 生命周期中添加全局监听器。**
2. **判断回车是否被按下，并执行登录逻辑。**

以下是实现代码：

```html
<div>
    <span class="usernameText">用户名：</span>
    <img class="usernameIcon" src="../../../assets/img/userNameInput.png"/>

    <el-autocomplete
        class="usernameInput"
        v-model="username"
        :fetch-suggestions="queryHistoryUserAccounts"
        placeholder="请输入用户名"
        @select="handleSelectUser"  <!-- 选择用户名时触发 -->
    >
        <template slot-scope="{ item }">
            <span style="font-style:italic;">{{item.name}}</span>
            <span style="float:right;font-weight:bold;">{{item.dispName}}</span>
        </template>
    </el-autocomplete>
</div>

<div>
    <span class="passwordText">密码：</span>
    <img class="passwordIcon" src="../../../assets/img/passwordInput.png"/>
    <input type="password" placeholder="请输入您的密码" class="passwordInput" v-model="password"/>
</div>
```

然后在 JavaScript 中，使用 `mounted` 钩子来全局监听回车事件：

```javascript
data() {
    return {
        username: '',
        password: '',
        selectedUser: false  // 标记是否选择了用户名
    };
},
methods: {
    handleSelectUser(item) {
        // 选择用户名时，将用户名存储到 v-model
        this.username = item.name;
        this.selectedUser = true;  // 标记用户名已选择
    },
    handleLogin() {
        console.log('用户名:', this.username);
        console.log('密码:', this.password);
        // 在此处调用实际的登录 API 或逻辑
    }
},
mounted() {
    // 全局监听回车键
    window.addEventListener('keyup', this.handleKeyUp);
},
beforeDestroy() {
    // 移除全局监听
    window.removeEventListener('keyup', this.handleKeyUp);
},
methods: {
    handleKeyUp(event) {
        // 只处理回车键（Enter键的keyCode是13）
        if (event.key === 'Enter') {
            // 如果用户名已选择或密码框已经填写，调用登录方法
            if (this.username && this.password) {
                this.handleLogin();
            }
        }
    }
}
```

### 关键点：

1. **全局监听回车事件：** `window.addEventListener('keyup', this.handleKeyUp)` 在 `mounted` 钩子中注册监听，`beforeDestroy` 中移除监听，确保不会在组件销毁后仍然监听事件。
2. **`handleKeyUp` 方法：** 判断按下的键是否是回车键（`event.key === 'Enter'`），并且确保用户名和密码都不为空后执行 `handleLogin`。
3. **手动触发登录：** 当回车键被按下时，调用 `handleLogin` 进行登录。

### 优点：

- **全局监听回车事件**，不依赖输入框是否失去焦点，确保回车键的事件始终能被捕获并执行。
- **适用于复杂场景**，例如多个输入框或组件中用户进行操作时，都能通过全局事件监听触发相应的处理函数。

这样实现后，无论用户选择了用户名并点击了回车，还是直接在密码框输入并点击回车，都会触发 `handleLogin` 函数，确保登录操作的顺利进行。



## 四、`@dblclick` 鼠标双击事件

`@dblclick` 是一个鼠标双击事件（Double Click Event），用于监听用户在元素上双击鼠标的操作。通常，它用在需要响应用户双击行为的场景，比如打开某个项目、选中文本等。

在 Vue.js 中，`@dblclick` 是事件监听的简写方式，等同于 `v-on:dblclick`，它会触发一个函数或方法来处理双击事件。

例如：

```html
<template>
  <div @dblclick="handleDoubleClick">双击我</div>
</template>

<script>
export default {
  methods: {
    handleDoubleClick() {
      alert('元素被双击了！');
    }
  }
}
</script>
```

这段代码中，`@dblclick="handleDoubleClick"` 表示监听 `dblclick` 事件，并在事件触发时调用 `handleDoubleClick` 方法。





## 五、$emit,$on 事件通信

~~~
解释代码：bus.$emit('accDevTypeChanged')
bus.$on('accDevTypeChanged')，业务为用户选择切换机器人、360的选项后，$emit + $on 进行广播通知，使页面展示用户选择的数据类型
~~~



这段代码使用了 **Vue 的事件总线（Event Bus）** 机制，利用 `$emit` 和 `$on` 在组件之间进行**事件通信**。

### **代码解释**

#### 1. `bus.$emit('accDevTypeChanged')`

- `bus` 代表的是 Vue 实例，通常用作事件总线（`new Vue()` 或 `Vue.observable({})`）。
- `$emit` 方法触发（广播）名为 `'accDevTypeChanged'` 的事件。
- 当用户选择 **切换机器人或 360 选项** 后，该事件会被触发，通知页面更新。

#### 2. `bus.$on('accDevTypeChanged')`

- `$on` 监听 `'accDevTypeChanged'` 事件，一旦该事件被触发，就会执行相应的回调函数。
- 监听到事件后，页面会更新，展示用户选择的设备类型（机器人或 360）。

### **示例代码**

#### **事件总线的实现**

在 Vue 2.x 中，通常会创建一个全局的 `bus` 事件总线：

```javascript
// eventBus.js
import Vue from 'vue';
export const bus = new Vue();
```

#### **组件 A（触发事件，用户切换设备类型）**

```javascript
import { bus } from '@/eventBus';

methods: {
  changeDeviceType() {
    // 用户选择了不同的设备（机器人或 360）
    bus.$emit('accDevTypeChanged', this.selectedDeviceType);
  }
}
```

#### **组件 B（监听事件，更新页面展示）**

```javascript
import { bus } from '@/eventBus';

created() {
  bus.$on('accDevTypeChanged', (deviceType) => {
    this.currentDeviceType = deviceType; // 更新页面数据
  });
}
```

### **总结**

- `bus.$emit('accDevTypeChanged')`：触发事件（用户切换设备类型）。
- `bus.$on('accDevTypeChanged')`：监听事件（页面组件接收并更新 UI）。
- 事件总线 `bus` 让**非父子组件**之间也可以进行数据传递。

如果你的项目是 Vue 3，建议使用 **`mitt`** 代替 `$emit/$on`，因为 Vue 3 已经移除了事件总线。



~~~
使用vueX也可以实现类似的效果吧，有什么区别
~~~



是的，**Vuex 也可以实现类似的效果**，但它与 **Event Bus（$emit / $on）** 在**使用场景和作用范围**上有很大区别。

------

## **📌 区别对比**

| 方式                         | 适用场景                                 | 数据管理                                              | 作用范围                                                | 维护成本                             |
| ---------------------------- | ---------------------------------------- | ----------------------------------------------------- | ------------------------------------------------------- | ------------------------------------ |
| **Event Bus（$emit / $on）** | 组件间的临时、简单通信                   | **仅用于事件通知**，不存储数据                        | **局部（特定组件间）**，适用于 **兄弟组件或非父子组件** | **较低**（适用于小型项目或简单交互） |
| **Vuex**                     | **全局状态管理**，适用于多个组件共享数据 | **集中管理状态（state）**，支持持久化、调试、插件扩展 | **全局（整个应用）**，适用于**多个组件共享数据**        | **较高**（适用于中大型项目）         |

------

## **📌 Vuex 实现 accDevTypeChanged**

如果你想用 Vuex 来代替 Event Bus，可以这样实现：

### **🔹 1. 在 Vuex store 里定义状态和 mutation**

```javascript
// store.js
import Vue from 'vue';
import Vuex from 'vuex';

Vue.use(Vuex);

export default new Vuex.Store({
  state: {
    deviceType: '' // 设备类型（机器人/360）
  },
  mutations: {
    setDeviceType(state, type) {
      state.deviceType = type;
    }
  }
});
```

------

### **🔹 2. 在触发事件的组件中（类似 `$emit`）**

```javascript
// 组件 A：用户切换设备类型
methods: {
  changeDeviceType(type) {
    this.$store.commit('setDeviceType', type); // 提交 mutation 更新 state
  }
}
```

------

### **🔹 3. 在监听事件的组件中（类似 `$on`）**

```javascript
// 组件 B：监听并更新 UI
computed: {
  currentDeviceType() {
    return this.$store.state.deviceType; // 读取 Vuex state
  }
}
```

------

## **📌 何时用 Event Bus，何时用 Vuex？**

| **场景**                                       | **推荐方案**                     |
| ---------------------------------------------- | -------------------------------- |
| **组件之间简单的事件通知（点击切换、弹窗等）** | **Event Bus（$emit / $on）**     |
| **全局共享数据，多个组件需要访问相同的状态**   | **Vuex**                         |
| **项目较小，组件通信需求简单**                 | **Event Bus 或 Vue 3 的 `mitt`** |
| **项目较大，需要状态管理、持久化、调试工具**   | **Vuex**                         |

------

## **📌 结论**

- **Event Bus（$emit / $on）适用于轻量级的事件通信**，比如 **某个组件触发另一个组件的 UI 更新**，但数据不会长期存储。
- **Vuex 适用于管理全局共享状态**，当多个组件需要访问同一个数据时，Vuex 可以确保数据**同步和一致性**。

对于你的场景（**用户选择设备类型后通知页面更新**）：

- **如果只是兄弟组件间传递一次性数据**，`Event Bus` 更简单。
- **如果设备类型需要多个组件共享并长期管理**，`Vuex` 更合适。

如果是 Vue 3，推荐使用 `mitt` 替代 Event Bus，或 `Pinia` 替代 Vuex。





## 六、this.$nextTick的使用



![image-20250414143250226](Vue2.assets/image-20250414143250226.png)





## 七、混入（Mixin）实现代码复用



~~~js
解释代码：“import RobotMixin from '../api/mixin';

export default {

    mixins: [RobotMixin],        //切割车的型号
    methods:{
        formatTrainCodes(code) {

            return this.calcTrainFullDispTag(code);
        }
    }
        ”
~~~



![image-20250417113846323](Vue2.assets/image-20250417113846323.png)



这段代码是一个 Vue 组件（或 JavaScript 模块）的一部分，主要作用是引入一个混入（Mixin）`RobotMixin`，并定义了一个方法 `formatTrainCodes`。下面是详细解释：

------

### ✅ 代码详解

```js
import RobotMixin from '../api/mixin';
```

- 这行代码引入了一个名为 `RobotMixin` 的混入模块。
- 混入（Mixin）是 Vue 中的一种代码复用机制，通常包含一些组件中可以复用的方法、生命周期函数、计算属性等。
- 路径 `'../api/mixin'` 指向该模块的位置。

```js
export default {
    mixins: [RobotMixin],        // 切割车的型号
```

- `export default` 导出一个默认对象，也就是 Vue 组件的配置对象。
- `mixins` 是 Vue 组件的一个选项，它接收一个混入对象的数组。
- 把 `RobotMixin` 加入进来，说明当前组件会“继承” `RobotMixin` 中定义的方法、数据等。
- 注释 `//切割车的型号` 可能是作者为了提示：这个 mixin 里有关于“切割车型号”的逻辑。

```js
    formatTrainCodes(code) {
        return this.calcTrainFullDispTag(code);
    }
```

- 定义了一个名为 `formatTrainCodes` 的方法。
- 这个方法接收一个参数 `code`（可能是车次、车型编号等），并调用 `this.calcTrainFullDispTag(code)` 来处理它。
- `calcTrainFullDispTag` 很可能是在 `RobotMixin` 中定义的方法（通过 `mixin` 继承进来）。
- 这个方法的作用应该是“格式化车次代码”，比如把原始的 `code` 转换成一个完整的显示标签。

------

### 🧠 总结

这个组件通过引入 `RobotMixin`，复用了其中的逻辑（尤其是 `calcTrainFullDispTag` 方法），并封装了一个本地方法 `formatTrainCodes` 来格式化传入的车次代码。这种写法让逻辑更清晰、复用性更高
