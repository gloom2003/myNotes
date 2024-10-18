# element-ui



### ElementUl

一个基于vue的UI框架

ElementUl： https://element.eleme.cn/#/zh-CN

#### 安装

配合官方文档进行操作

![image-20240805110146486](C:\Users\Gloom\AppData\Roaming\Typora\typora-user-images\image-20240805110146486.png)

![image-20240805110350416](C:\Users\Gloom\AppData\Roaming\Typora\typora-user-images\image-20240805110350416.png)



![image-20240821171503469](E:\alwaysUse\notes\myNotes\frontend note\element-ui\elememt-ui.assets\image-20240821171503469.png)







组件示例

- 1.按钮
- 2.表单
- 3.表格

效果：

![image-20240805115704613](C:\Users\Gloom\AppData\Roaming\Typora\typora-user-images\image-20240805115704613.png)

例子：

![image-20240805112432057](C:\Users\Gloom\AppData\Roaming\Typora\typora-user-images\image-20240805112432057.png)



### 按钮组件

#### 一般按钮



![image-20240821172412103](E:\alwaysUse\notes\myNotes\frontend note\element-ui\elememt-ui.assets\image-20240821172412103.png)

#### 按钮组

![image-20240821173941442](E:\alwaysUse\notes\myNotes\frontend note\element-ui\elememt-ui.assets\image-20240821173941442.png)



mark1 p4 2:17



### Link 文字链接组件

![image-20240822175721898](E:\alwaysUse\notes\myNotes\frontend note\element-ui\elememt-ui.assets\image-20240822175721898.png)



### Layout布局组件

每一个el-row占24份(`1*2*3*4`)，方便后面操作

#### el-row属性

![image-20240822181159174](E:\alwaysUse\notes\myNotes\frontend note\element-ui\elememt-ui.assets\image-20240822181159174.png)

效果：

![image-20240822181237578](E:\alwaysUse\notes\myNotes\frontend note\element-ui\elememt-ui.assets\image-20240822181237578.png)



#### el-col属性



![image-20240822181852553](E:\alwaysUse\notes\myNotes\frontend note\element-ui\elememt-ui.assets\image-20240822181852553.png)

设置：

![image-20240822181913023](E:\alwaysUse\notes\myNotes\frontend note\element-ui\elememt-ui.assets\image-20240822181913023.png)

效果：

![image-20240822181932354](E:\alwaysUse\notes\myNotes\frontend note\element-ui\elememt-ui.assets\image-20240822181932354.png)



### 3 Container布局容器

![image-20240823093704961](E:\alwaysUse\notes\myNotes\frontend note\element-ui\elememt-ui.assets\image-20240823093704961.png)

设置：

![image-20240823093322056](E:\alwaysUse\notes\myNotes\frontend note\element-ui\elememt-ui.assets\image-20240823093322056.png)

效果：

![image-20240823093343103](E:\alwaysUse\notes\myNotes\frontend note\element-ui\elememt-ui.assets\image-20240823093343103.png)



### radio单选按钮组件的使用

![image-20240823100638617](E:\alwaysUse\notes\myNotes\frontend note\element-ui\elememt-ui.assets\image-20240823100638617.png)



### 分页组件的使用



![image-20240823142654072](E:\alwaysUse\notes\myNotes\frontend note\element-ui\elememt-ui.assets\image-20240823142654072.png)



### 日期组件

![image-20240823144914275](E:\alwaysUse\notes\myNotes\frontend note\element-ui\elememt-ui.assets\image-20240823144914275.png)



### Table表格 el-table 表格属性

参考视频：https://www.bilibili.com/video/BV1NK4y187XH?p=19&spm_id_from=pageDriver&vd_source=d6367c1fc21883823f1fb738f86ef26e



#### 合并单元格

![image-20240822130650749](E:\alwaysUse\notes\myNotes\frontend note\element-ui\elememt-ui.assets\image-20240822130650749.png)

设置：

1

![image-20240822132129400](E:\alwaysUse\notes\myNotes\frontend note\element-ui\elememt-ui.assets\image-20240822132129400.png)

​	2![image-20240822132044828](E:\alwaysUse\notes\myNotes\frontend note\element-ui\elememt-ui.assets\image-20240822132044828.png)



效果：

合并前：

![image-20240822132235334](E:\alwaysUse\notes\myNotes\frontend note\element-ui\elememt-ui.assets\image-20240822132235334.png)

合并后：

![image-20240822131436778](E:\alwaysUse\notes\myNotes\frontend note\element-ui\elememt-ui.assets\image-20240822131436778.png)

##### 例子：

使用前：

![image-20240822151051941](E:\alwaysUse\notes\myNotes\frontend note\element-ui\elememt-ui.assets\image-20240822151051941.png)

设置：

~~~js
// 1
mergeCell({ row, column, rowIndex, columnIndex }){
				let totalRowCount = this.dayStates.length;
				// 合并1-3列的偶数行（从0开始数）
				let mergeRowIndex = 0;
				while(mergeRowIndex < totalRowCount){
					// 合并序号到年月的单元格
					for(let i = 0;i < 4;i++){
						if(rowIndex == mergeRowIndex && columnIndex == i){
							return {
									rowspan: 2,
									colspan: 1
								}
                            // 注意：这里不能使用++mergeRowIndex
						}else if(rowIndex == mergeRowIndex + 1 && columnIndex == i){
							// 隐藏被挤去右边的单元格，基于合并前最开始的坐标
							return {
									rowspan: 0,
									colspan: 0
								}
						}
					}
					mergeRowIndex += 2;
				}
}
// 2 
mergeCell({ row, column, rowIndex, columnIndex }){
				for(let i = 0;i < totalRowCount;i+=2){
					if (columnIndex >= 0 && columnIndex<= 3) {
						if (rowIndex === i) {
						  return [2, 1];
						} else if (rowIndex === i+1) {
						  return [0, 0];
						}
					}
				}
}
~~~

**注意：**

为什么不能使用++mergeRowIndex替换mergeRowIndex + 1？

~~~js
				let i = 0;
				console.log(`i = ${i}`)
				console.log(`++i = ${++i}`);
				console.log(`i++ = ${i++}`);
				console.log(`i + 1 = ${i + 1}`);
// 测试结果没问题，和Java的一样
~~~

因为++mergeRowIndex会改变mergeRowIndex的值，而mergeRowIndex + 1只会使用+1后的值。不会对mergeRowIndex本身进行改变！



使用后：

![image-20240822150854263](E:\alwaysUse\notes\myNotes\frontend note\element-ui\elememt-ui.assets\image-20240822150854263.png)



#### cell-style 设置每个单元格的样式

![image-20240822105032270](E:\alwaysUse\notes\myNotes\frontend note\element-ui\elememt-ui.assets\image-20240822105032270.png)

设置：

![image-20240822105200282](E:\alwaysUse\notes\myNotes\frontend note\element-ui\elememt-ui.assets\image-20240822105200282.png)



#### 4 固定表头 height

固定表头：

![image-20240819173730564](C:\Users\Gloom\AppData\Roaming\Typora\typora-user-images\image-20240819173730564.png)

##### 设置高度

![image-20240820165144632](C:\Users\Gloom\AppData\Roaming\Typora\typora-user-images\image-20240820165144632.png)

设置：

![image-20240820164959518](C:\Users\Gloom\AppData\Roaming\Typora\typora-user-images\image-20240820164959518.png)

效果：

![image-20240820165102768](C:\Users\Gloom\AppData\Roaming\Typora\typora-user-images\image-20240820165102768.png)



#### stripe 是否显示斑马线

![image-20240820165336422](C:\Users\Gloom\AppData\Roaming\Typora\typora-user-images\image-20240820165336422.png)

设置：

![image-20240820165415607](C:\Users\Gloom\AppData\Roaming\Typora\typora-user-images\image-20240820165415607.png)

效果：

![image-20240820165309468](C:\Users\Gloom\AppData\Roaming\Typora\typora-user-images\image-20240820165309468.png)



#### size 表的大小

![image-20240820165637030](C:\Users\Gloom\AppData\Roaming\Typora\typora-user-images\image-20240820165637030.png)

设置：

![image-20240820165514263](C:\Users\Gloom\AppData\Roaming\Typora\typora-user-images\image-20240820165514263.png)

效果：

![image-20240820165545052](C:\Users\Gloom\AppData\Roaming\Typora\typora-user-images\image-20240820165545052.png)



#### fit 设置列的宽度是否自撑开

![image-20240820165742204](C:\Users\Gloom\AppData\Roaming\Typora\typora-user-images\image-20240820165742204.png)

设置：

![image-20240820165935286](C:\Users\Gloom\AppData\Roaming\Typora\typora-user-images\image-20240820165935286.png)

效果：

![image-20240820170154476](C:\Users\Gloom\AppData\Roaming\Typora\typora-user-images\image-20240820170154476.png)



#### show-header 是否显示表头

![image-20240820170518695](C:\Users\Gloom\AppData\Roaming\Typora\typora-user-images\image-20240820170518695.png)

设置：

![image-20240820170340906](C:\Users\Gloom\AppData\Roaming\Typora\typora-user-images\image-20240820170340906.png)

效果：

![image-20240820170443856](C:\Users\Gloom\AppData\Roaming\Typora\typora-user-images\image-20240820170443856.png)



#### highlight-current-row 是否要高亮显示鼠标选择行

![image-20240820170539575](C:\Users\Gloom\AppData\Roaming\Typora\typora-user-images\image-20240820170539575.png)

设置：

![image-20240820172039863](C:\Users\Gloom\AppData\Roaming\Typora\typora-user-images\image-20240820172039863.png)

效果：

![image-20240820170752083](C:\Users\Gloom\AppData\Roaming\Typora\typora-user-images\image-20240820170752083.png)



#### current-row-key 

![image-20240820170845767](C:\Users\Gloom\AppData\Roaming\Typora\typora-user-images\image-20240820170845767.png)

设置：



效果：

#### row-class-name 设置每一行的样式

![image-20240820172207082](C:\Users\Gloom\AppData\Roaming\Typora\typora-user-images\image-20240820172207082.png)

设置：

1

![image-20240820171932289](C:\Users\Gloom\AppData\Roaming\Typora\typora-user-images\image-20240820171932289.png)

2

![image-20240820172512010](C:\Users\Gloom\AppData\Roaming\Typora\typora-user-images\image-20240820172512010.png)

3 ![image-20240820173514251](C:\Users\Gloom\AppData\Roaming\Typora\typora-user-images\image-20240820173514251.png)

注意：stripe斑马线的样式会高于这个设置的样式

效果：

![image-20240820173434631](C:\Users\Gloom\AppData\Roaming\Typora\typora-user-images\image-20240820173434631.png)



`<style scoped>`科普：

在 Vue.js 的早期版本中（2.x 版本及更早），`<style scoped>` 是一个用于指定样式作用范围的属性。当你在 `<style>` 标签中使用 `scoped` 属性时，表示这些样式只会应用于当前组件中的元素，而不会影响到其他组件中的元素。这样可以避免样式冲突，确保组件的样式是模块化和局部的。

具体工作原理：
当使用 `scoped` 属性时，Vue 会为当前组件的每个 DOM 元素生成一个唯一的属性（类似 `data-v-xxxxxx`），并将这个属性添加到组件的所有 HTML 元素上。同时，Vue 还会将这个属性添加到 CSS 选择器中，以确保这些样式仅适用于带有该属性的元素，从而实现样式的作用范围限定在当前组件内。

示例：
```vue
<template>
  <div class="my-component">
    <p>This is a paragraph in MyComponent.</p>
  </div>
</template>

<style scoped>
.my-component {
  color: blue;
}

p {
  font-size: 14px;
}
</style>
```

在上面的例子中，`scoped` 属性确保 `.my-component` 的样式和 `<p>` 元素的样式只会作用于 `MyComponent` 组件内的元素，不会影响其他组件中的元素，即使其他组件中也有相同的类名或标签选择器。

注意：
- `scoped` 属性只在 Vue 2 中有明确的支持。
- 在 Vue 3 中，Vue 官方推荐使用基于 CSS Modules 或 `<style module>` 的方式来管理局部样式。
- 如果你使用 `scoped` 样式，但需要全局样式（例如，全局样式表或第三方库），你需要将这些样式放在不带 `scoped` 属性的 `<style>` 标签中，或者将这些样式放在全局 CSS 文件中。

总结：
`scoped` 属性的主要作用是将样式的作用范围限定在当前组件内，避免样式冲突，是一种实现样式隔离的技术。



#### empty-text 设置没有数据时的提示信息

![image-20240820175124905](C:\Users\Gloom\AppData\Roaming\Typora\typora-user-images\image-20240820175124905.png)

设置：

![image-20240820175358536](C:\Users\Gloom\AppData\Roaming\Typora\typora-user-images\image-20240820175358536.png)

效果：

![image-20240820175055827](C:\Users\Gloom\AppData\Roaming\Typora\typora-user-images\image-20240820175055827.png)



#### 设置多选框

![image-20240820175907311](C:\Users\Gloom\AppData\Roaming\Typora\typora-user-images\image-20240820175907311.png)



设置：

![image-20240820180457915](C:\Users\Gloom\AppData\Roaming\Typora\typora-user-images\image-20240820180457915.png)

效果：

![image-20240820175949871](C:\Users\Gloom\AppData\Roaming\Typora\typora-user-images\image-20240820175949871.png)



### 组件事件使用

![image-20240820180948544](C:\Users\Gloom\AppData\Roaming\Typora\typora-user-images\image-20240820180948544.png)

设置

![image-20240820180910793](C:\Users\Gloom\AppData\Roaming\Typora\typora-user-images\image-20240820180910793.png)



### 组件方法使用

如，使用：

![image-20240820181225343](C:\Users\Gloom\AppData\Roaming\Typora\typora-user-images\image-20240820181225343.png)

设置：

1

给组件起别名ref

![image-20240820181303470](C:\Users\Gloom\AppData\Roaming\Typora\typora-user-images\image-20240820181303470.png)

2

![image-20240820181428899](C:\Users\Gloom\AppData\Roaming\Typora\typora-user-images\image-20240820181428899.png)





### 表格定义操作列

设置：

![image-20240821101642870](C:\Users\Gloom\AppData\Roaming\Typora\typora-user-images\image-20240821101642870.png)

效果：

![image-20240821102809188](E:\alwaysUse\notes\myNotes\frontend note\element-ui\elememt-ui.assets\image-20240821102809188.png)



### 自定义表头

设置

![image-20240821103818231](E:\alwaysUse\notes\myNotes\frontend note\element-ui\elememt-ui.assets\image-20240821103818231.png)

效果：

![image-20240821103736418](E:\alwaysUse\notes\myNotes\frontend note\element-ui\elememt-ui.assets\image-20240821103736418.png)



#### 竖直方向的边框 border

![image-20240819173957860](C:\Users\Gloom\AppData\Roaming\Typora\typora-user-images\image-20240819173957860.png)



#### 设置表头的颜色 header-cell-style

![image-20240819175609174](C:\Users\Gloom\AppData\Roaming\Typora\typora-user-images\image-20240819175609174.png)



#### el-table-column属性

##### 原始代码：

![image-20240820155423831](C:\Users\Gloom\AppData\Roaming\Typora\typora-user-images\image-20240820155423831.png)

路由：

![image-20240820154129560](C:\Users\Gloom\AppData\Roaming\Typora\typora-user-images\image-20240820154129560.png)



app.vue首页：

![image-20240820154218205](C:\Users\Gloom\AppData\Roaming\Typora\typora-user-images\image-20240820154218205.png)



效果：

![image-20240820155328174](C:\Users\Gloom\AppData\Roaming\Typora\typora-user-images\image-20240820155328174.png)

##### 5 width属性: 设置列的宽度

![image-20240820155746334](C:\Users\Gloom\AppData\Roaming\Typora\typora-user-images\image-20240820155746334.png)

默认的width是平均分配的，设置width = 200px;

![image-20240820155847974](C:\Users\Gloom\AppData\Roaming\Typora\typora-user-images\image-20240820155847974.png)

效果：

![image-20240820155513424](C:\Users\Gloom\AppData\Roaming\Typora\typora-user-images\image-20240820155513424.png)



##### fixed属性 自适应

![image-20240820155816694](C:\Users\Gloom\AppData\Roaming\Typora\typora-user-images\image-20240820155816694.png)

设置为right：

![image-20240820160048438](C:\Users\Gloom\AppData\Roaming\Typora\typora-user-images\image-20240820160048438.png)

效果：

![image-20240820160023716](C:\Users\Gloom\AppData\Roaming\Typora\typora-user-images\image-20240820160023716.png)

设置为left:

![image-20240820160216904](C:\Users\Gloom\AppData\Roaming\Typora\typora-user-images\image-20240820160216904.png)

效果：

整体偏左了一些

![image-20240820160257749](C:\Users\Gloom\AppData\Roaming\Typora\typora-user-images\image-20240820160257749.png)



##### align属性 设置内容的对齐方式

设置内容的对齐方式，标题也会变为居中对齐

![image-20240820160734886](C:\Users\Gloom\AppData\Roaming\Typora\typora-user-images\image-20240820160734886.png)

设置居中对齐

![image-20240820161005448](C:\Users\Gloom\AppData\Roaming\Typora\typora-user-images\image-20240820161005448.png)

效果：

![image-20240820161133183](C:\Users\Gloom\AppData\Roaming\Typora\typora-user-images\image-20240820161133183.png)



##### header-align属性 设置表头的对齐方式

![image-20240820160750151](C:\Users\Gloom\AppData\Roaming\Typora\typora-user-images\image-20240820160750151.png)

设置：

![image-20240820161607222](C:\Users\Gloom\AppData\Roaming\Typora\typora-user-images\image-20240820161607222.png)

效果：

![image-20240820161353307](C:\Users\Gloom\AppData\Roaming\Typora\typora-user-images\image-20240820161353307.png)

##### sortable属性 排序

![image-20240820161448744](C:\Users\Gloom\AppData\Roaming\Typora\typora-user-images\image-20240820161448744.png)

设置：

![image-20240820161908968](C:\Users\Gloom\AppData\Roaming\Typora\typora-user-images\image-20240820161908968.png)

效果：

![image-20240820161746993](C:\Users\Gloom\AppData\Roaming\Typora\typora-user-images\image-20240820161746993.png)

###### sort-method属性 自定义排序

![image-20240820162103832](C:\Users\Gloom\AppData\Roaming\Typora\typora-user-images\image-20240820162103832.png)

设置：

1

![image-20240820162612376](C:\Users\Gloom\AppData\Roaming\Typora\typora-user-images\image-20240820162612376.png)

2

![image-20240820162353082](C:\Users\Gloom\AppData\Roaming\Typora\typora-user-images\image-20240820162353082.png)

效果：

![image-20240820162805822](C:\Users\Gloom\AppData\Roaming\Typora\typora-user-images\image-20240820162805822.png)



##### resizable属性 单元格是否可以拖动

![image-20240820162508070](C:\Users\Gloom\AppData\Roaming\Typora\typora-user-images\image-20240820162508070.png)

设置：

![image-20240820163000970](C:\Users\Gloom\AppData\Roaming\Typora\typora-user-images\image-20240820163000970.png)

效果：

![image-20240820163102750](C:\Users\Gloom\AppData\Roaming\Typora\typora-user-images\image-20240820163102750.png)



##### formatter属性 在数据渲染之前进行拦截并进行处理

![image-20240820163228087](C:\Users\Gloom\AppData\Roaming\Typora\typora-user-images\image-20240820163228087.png)

设置：

1

![image-20240820164122647](C:\Users\Gloom\AppData\Roaming\Typora\typora-user-images\image-20240820164122647.png)

2

![image-20240820163908874](C:\Users\Gloom\AppData\Roaming\Typora\typora-user-images\image-20240820163908874.png)

3

![image-20240820164050102](C:\Users\Gloom\AppData\Roaming\Typora\typora-user-images\image-20240820164050102.png)

效果：

![image-20240820163800172](C:\Users\Gloom\AppData\Roaming\Typora\typora-user-images\image-20240820163800172.png)



##### 与template标签配合

设置：

![image-20240821151238045](E:\alwaysUse\notes\myNotes\frontend note\element-ui\elememt-ui.assets\image-20240821151238045.png)

效果：

![image-20240821151302831](E:\alwaysUse\notes\myNotes\frontend note\element-ui\elememt-ui.assets\image-20240821151302831.png)



##### type="index" 自定义序号列

设置：

![image-20240911093311762](E:\alwaysUse\notes\myNotes\frontend note\element-ui\elememt-ui.assets\image-20240911093311762.png)

函数：

![image-20240911093113780](E:\alwaysUse\notes\myNotes\frontend note\element-ui\elememt-ui.assets\image-20240911093113780.png)



### 其他

