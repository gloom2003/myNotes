# Vue3

## 参考：

https://www.bilibili.com/video/BV1Yg4y127Fp?spm_id_from=333.788.videopod.episodes&vd_source=d6367c1fc21883823f1fb738f86ef26e&p=21



### 1 入门 hello world

data是一个函数，使用到的是他的返回值（一个data对象）：

![image-20241214120014384](Vue3.assets/image-20241214120014384.png)





### 2 选项式与组合式api

![image-20241214122521967](Vue3.assets/image-20241214122521967.png)



![image-20241214140406882](Vue3.assets/image-20241214140406882.png)



### 3 使用ref()实现响应式

使用目的：实现响应式

其他的不会展示在页面上的数据可以定义普通变量来使用。

#### 3.1 使用流程：

1:

![image-20241214143313950](Vue3.assets/image-20241214143313950.png)

2:

![image-20241214142921167](Vue3.assets/image-20241214142921167.png)



#### 3.2 注意

![image-20241214144138746](Vue3.assets/image-20241214144138746.png)

![image-20241214143705325](Vue3.assets/image-20241214143705325.png)



### 4 设置Vue3文件的模板

![image-20241214175239474](Vue3.assets/image-20241214175239474.png)

解释：

![image-20241214152331116](Vue3.assets/image-20241214152331116.png)

1

~~~vue
<template>
  <view class=""> 

  </view>
</template>

<script setup>
	
</script>

<style lang="scss" scoped>

</style>

~~~





### 5 template标签的使用

![image-20241214153211180](Vue3.assets/image-20241214153211180.png)

效果：

![image-20241214153258016](Vue3.assets/image-20241214153258016.png)



![image-20241214154114129](Vue3.assets/image-20241214154114129.png)



### 6 v-if的易错点

![image-20241214153540216](Vue3.assets/image-20241214153540216.png)



### 7 v-for中 :key的易错点

![image-20241214155107471](Vue3.assets/image-20241214155107471.png)



vue3的基础main.js文件：

~~~js
// main.js
import { createApp } from "vue";
import App from "./App.vue"; // 根组件

// 创建 Vue 应用实例
const app = createApp(App);
// 挂载应用到 DOM
app.mount("#app");
~~~





## 学习进度：

https://www.bilibili.com/video/BV1Yg4y127Fp?spm_id_from=333.788.videopod.episodes&vd_source=d6367c1fc21883823f1fb738f86ef26e&p=21



![image-20241214174811622](Vue3.assets/image-20241214174811622.png)