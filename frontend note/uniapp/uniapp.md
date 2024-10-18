# uniapp

官方文档：https://uniapp.dcloud.net.cn/collocation/pages.html#condition

视频参考：https://www.bilibili.com/video/BV1mT411K7nW?p=12&vd_source=d6367c1fc21883823f1fb738f86ef26e



## nevigator 跳转页面

![image-20240825114301892](E:\alwaysUse\notes\myNotes\frontend note\uniapp\uniapp.assets\image-20240825114301892.png)

解决：

![image-20240825115101289](E:\alwaysUse\notes\myNotes\frontend note\uniapp\uniapp.assets\image-20240825115101289.png)



## 轮播图 swiper



## 小程序生命周期函数 

https://uniapp.dcloud.net.cn/tutorial/page.html#lifecycle

### onReachBottom（）页面滚动到底部的事件



### onload() 监听页面加载(第一次显示时)



![image-20240825180130824](E:\alwaysUse\notes\myNotes\frontend note\uniapp\uniapp.assets\image-20240825180130824.png)



### onShow() 页面每次出现在屏幕上都触发

https://uniapp.dcloud.net.cn/tutorial/page.html#lifecycle

~~~js
		onShow(){
			// 处理用户修改发票后没有点击确认，直接点击左上角按钮返回的情况，在这里把发票数据进行恢复
			if(this.recoverClickedInvoiceData){
				this.$set(this.allInvoiceMsg, this.clickedIncoiceIndex, this.clickedInvoiceCopy);
				this.recoverClickedInvoiceData = false;
			}
		},
~~~



### 获取页面跳转时传递的参数

https://uniapp.dcloud.net.cn/api/router.html#navigateto

![image-20240830142741391](E:\alwaysUse\notes\myNotes\frontend note\uniapp\uniapp.assets\image-20240830142741391.png)

例子：

设置跳转时传参：

![image-20240828102518217](E:\alwaysUse\notes\myNotes\frontend note\uniapp\uniapp.assets\image-20240828102518217.png)

获取：

![image-20240828102407667](E:\alwaysUse\notes\myNotes\frontend note\uniapp\uniapp.assets\image-20240828102407667.png)



### onUnload（）用户点击左上角返回按钮

在 `uni-app` 中，可以通过 `onUnload` 钩子函数监听用户点击左上角返回按钮的事件。`onUnload` 会在页面卸载时触发，即当用户点击左上角的返回按钮或者调用 `uni.navigateBack()` 返回上一个页面时，都会触发这个钩子。

可以按如下方式使用 `onUnload`：

```javascript
export default {
  onUnload() {
    // 这里是返回上一个页面时触发的代码
    console.log('用户点击了返回按钮');
    // 在此处理返回时需要执行的逻辑
  }
}
```

这样，当用户点击左上角返回按钮离开当前页面时，你的逻辑就会被执行。

如果需要根据用户操作进行一些状态处理，可以在 `onUnload` 中添加相应的业务逻辑。





## 提示框



### 加载框与只展示文字框



![image-20240827172127022](E:\alwaysUse\notes\myNotes\frontend note\uniapp\uniapp.assets\image-20240827172127022.png)



### 确认框

https://uniapp.dcloud.net.cn/api/ui/prompt.html#iprompterror-values-3

![image-20240819103628547](C:\Users\Gloom\AppData\Roaming\Typora\typora-user-images\image-20240819103628547.png)

#### 注意this的指向问题

使用方式1：

~~~js
deleteInvoice(){
				uni.showModal({
					title: '删除操作',
					content: '确认要删除吗?',
                    // 1 使用函数表达式
					success: (res) => {
						if (res.confirm) {
							for(let i = 0; i < this.mergArryIndex.length; i++){
								let deletedIndex = this.mergArryIndex[i];
								this.allInvoiceMsg.splice(deletedIndex,1);
							}
						} else if (res.cancel) {
							console.log('用户点击取消');
						}
					}
				});
			}
~~~

使用方式2：

~~~js
deleteInvoice(){
    			// t
    			let _this = this;
				uni.showModal({
					title: '删除操作',
					content: '确认要删除吗?',
					success: function(res){
						if (res.confirm) {
							for(let i = 0; i < _this.mergArryIndex.length; i++){
								let deletedIndex = _this.mergArryIndex[i];
								this.allInvoiceMsg.splice(deletedIndex,1);
							}
						} else if (res.cancel) {
							console.log('用户点击取消');
						}
					}
				});
			}
~~~





### 多选框

![image-20240827173542737](E:\alwaysUse\notes\myNotes\frontend note\uniapp\uniapp.assets\image-20240827173542737.png)

效果：

![image-20240827173608295](E:\alwaysUse\notes\myNotes\frontend note\uniapp\uniapp.assets\image-20240827173608295.png)

类似组件：

![image-20240827173804124](E:\alwaysUse\notes\myNotes\frontend note\uniapp\uniapp.assets\image-20240827173804124.png)



## 设置页面的标题与颜色等



### 在pages.json中进行配置

![image-20240827175304873](E:\alwaysUse\notes\myNotes\frontend note\uniapp\uniapp.assets\image-20240827175304873.png)



### 使用js动态设置

https://uniapp.dcloud.net.cn/api/ui/navigationbar.html

会覆盖配置文件中配置的颜色等：

![image-20240827175209690](E:\alwaysUse\notes\myNotes\frontend note\uniapp\uniapp.assets\image-20240827175209690.png)

效果：

![image-20240827175501532](E:\alwaysUse\notes\myNotes\frontend note\uniapp\uniapp.assets\image-20240827175501532.png)



## 配置URL路径与页面的路由



### pages.json中配置

![image-20241011085445125](E:\alwaysUse\notes\myNotes\frontend note\uniapp\uniapp.assets\image-20241011085445125.png)

使用：

![image-20241011085709239](E:\alwaysUse\notes\myNotes\frontend note\uniapp\uniapp.assets\image-20241011085709239.png)



## 配置底部栏tarBar



### pages.json中底部栏的一般设置



https://uniapp.dcloud.net.cn/collocation/pages.html#tabbar

设置：

![image-20240825113055342](E:\alwaysUse\notes\myNotes\frontend note\uniapp\uniapp.assets\image-20240825113055342.png)

效果：

![image-20240825113137029](E:\alwaysUse\notes\myNotes\frontend note\uniapp\uniapp.assets\image-20240825113137029.png)



### pages.json中使用iconfont图标

![image-20240827180647625](E:\alwaysUse\notes\myNotes\frontend note\uniapp\uniapp.assets\image-20240827180647625.png)



### 动态设置 tabBar 某一项的内容

https://uniapp.dcloud.net.cn/api/ui/tabbar.html



## 数据缓存Storage的用法

https://uniapp.dcloud.net.cn/api/storage/storage.html

### 浏览器存储



存储位置：

![image-20240828105147522](E:\alwaysUse\notes\myNotes\frontend note\uniapp\uniapp.assets\image-20240828105147522.png)

![image-20240828105531324](E:\alwaysUse\notes\myNotes\frontend note\uniapp\uniapp.assets\image-20240828105531324.png)



### 一般全使用sync

![image-20240828110327102](E:\alwaysUse\notes\myNotes\frontend note\uniapp\uniapp.assets\image-20240828110327102.png)



## 修改内置组件

例子：

### 取消scroll-view标签的滑动框

![image-20240828143116971](E:\alwaysUse\notes\myNotes\frontend note\uniapp\uniapp.assets\image-20240828143116971.png)



![image-20240828142930497](E:\alwaysUse\notes\myNotes\frontend note\uniapp\uniapp.assets\image-20240828142930497.png)



### 修改rich-text组件中的图片大小显示问题

![image-20240830144112130](E:\alwaysUse\notes\myNotes\frontend note\uniapp\uniapp.assets\image-20240830144112130.png)



微信小程序的解决方法：

![image-20240830144848771](E:\alwaysUse\notes\myNotes\frontend note\uniapp\uniapp.assets\image-20240830144848771.png)



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



## 打包方式（小程序，app,h5）



![image-20240828112357630](E:\alwaysUse\notes\myNotes\frontend note\uniapp\uniapp.assets\image-20240828112357630.png)

详细教程：直接google



## 媒体相关组件

### image标签

image标签常用：防止图片变形

~~~vue
		<view class="pic">
			<image :src="item.picurl" mode="aspectFill"></image>
		</view>
~~~

![image-20240828144750787](E:\alwaysUse\notes\myNotes\frontend note\uniapp\uniapp.assets\image-20240828144750787.png)



mark1 p57



## 富文本 rich-text组件

https://uniapp.dcloud.net.cn/component/rich-text.html

![image-20240830143510562](E:\alwaysUse\notes\myNotes\frontend note\uniapp\uniapp.assets\image-20240830143510562.png)

例子：





## 其他基础



### 小程序与H5的差异



#### 顶部不同

**H5的顶部从左上角开始，小程序的顶部从导航栏bar左下角开始**

![image-20240828161749294](E:\alwaysUse\notes\myNotes\frontend note\uniapp\uniapp.assets\image-20240828161749294.png)

![image-20240828161920129](E:\alwaysUse\notes\myNotes\frontend note\uniapp\uniapp.assets\image-20240828161920129.png)

##### 解决

设置固定定位 fixed时：

要这样子设置，H5才能够正常显示：

![image-20240828161454772](E:\alwaysUse\notes\myNotes\frontend note\uniapp\uniapp.assets\image-20240828161454772.png)



![image-20240828161209226](E:\alwaysUse\notes\myNotes\frontend note\uniapp\uniapp.assets\image-20240828161209226.png)





### api使用注意：

#### this指向问题解决

1）

![image-20240827174221170](E:\alwaysUse\notes\myNotes\frontend note\uniapp\uniapp.assets\image-20240827174221170.png)

2）也可以提前使用that变量指向this对象

### view等标签



![image-20240823162449117](E:\alwaysUse\notes\myNotes\frontend note\uniapp\uniapp.assets\image-20240823162449117.png)

### 响应式布局rpx



![image-20240823161312279](E:\alwaysUse\notes\myNotes\frontend note\uniapp\uniapp.assets\image-20240823161312279.png)

一般来说：1px = 2rpx



### 语法糖

#### super css



![image-20240823153109029](E:\alwaysUse\notes\myNotes\frontend note\uniapp\uniapp.assets\image-20240823153109029.png)



### 开发者工具的使用



#### 使网络请求变慢

![image-20240830140145735](E:\alwaysUse\notes\myNotes\frontend note\uniapp\uniapp.assets\image-20240830140145735.png)





## 开发小程序的实际使用

参考：https://blog.csdn.net/m0_51126511/article/details/124601431?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522B9CDB063-4F03-4C6F-B23E-177FB0E11886%2522%252C%2522scm%2522%253A%252220140713.130102334..%2522%257D&request_id=B9CDB063-4F03-4C6F-B23E-177FB0E11886&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~top_click~default-1-124601431-null-null.142^v100^pc_search_result_base8&utm_term=%40tap&spm=1018.2226.3001.4187

### @tap与@click的区别

@tap与@click的区别
tap和click都是点击事件。不过移动端有太多复杂的功能是click监听不到的，例如，触摸、按住和轻滑。这时候就要用tap方法了。

另外，click事件是点击放开之后才触发的，所以时间上会有延迟，大概200-300ms，可是我们在移动端的话就比较追求速度，所以就不能出现说有延迟的情况。所以用tap来代替click事件的话，对于针对移动设备的产品都适合。

而且，tap还有一个特点就是事件穿透，就是你执行完绑定的tap事件之后呢，如果下面如果绑定了其他事件或者是本身就存在点击事件的话，也会默认触发。



### 使用uni-ui组件

#### 使用别人的组件，修改组件样式的方法(宽度与高度等)

1）使用class或者style

2）包裹一层view标签来改变宽度与高度，让子元素自动填满父容器：

~~~vue
			    <!-- 按照报销分类进行筛选的下拉框 -->
<view class="reimbursement_category_dropdown_menu">
    <uni-data-select v-model="searchForm.category" :localdata="reimbursementCategoryList" placeholder="请选择报销分类" @change="change">		</uni-data-select>
</view>

	.reimbursement_category_dropdown_menu{
		width: 300rpx;
		margin-right: 20rpx;          /* 各个输入框之间的间隔 */
	}
~~~

3）使用`!important`来提高优先级

4）实在不行就看官方文档的例子



### 其他



#### uniapp显示空格

参考：https://blog.csdn.net/qq_27577113/article/details/121405287

```vue
<text>
<!-- 显示空格，使用vue -->
{{'   '}}查询
</text>
```



#### 单独处理H5或小程序：#ifdef H5

不同的标签：

~~~vue
			<!-- #ifdef MP -->
			<scroll-view  scroll-y="true" style="width: 100%;height: 100%;">
			<!-- #endif -->
			
			<!-- #ifdef H5 -->
			<view class="center-wrap">
			<!-- #endif -->
~~~



不同的css:

~~~css
	
	.submission_time_range_selection_box{
		width: 608rpx;
		margin-right: 20rpx;          /* 各个输入框之间的间隔 */
		margin-bottom: 10rpx; /* 设置下方间距 */
		/* #ifdef MP */
		margin-top: 20rpx;
		/* #endif */
	}
~~~



不是H5的才执行的代码：

~~~js
				// #ifndef H5
				dd.device.notification.actionSheet({
					otherButtons: ["图片/相机"],
					// otherButtons: ["图片/相机", "手动输入"],
					cancelButton: "取消", 
					onSuccess : function(res) {
						if (res.buttonIndex == 0) {
							_this.takePhoto();
						} else if(res.buttonIndex == 1) {
							console.log("点击了第二个");
						}
					},
					onFail : function(err) {},
				})
				// #endif
~~~

