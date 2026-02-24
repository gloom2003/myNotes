







## 加载效果

~~~js
import { ElMessage, ElMessageBox, ElLoading } from 'element-plus' 

// 打开人员选择弹窗并等待用户选择
  return new Promise((resolve, reject) => {
    // 设置确认回调函数
    personListDialog.onConfirm = async (selectedPerson) => {
      // 显示全屏加载效果
      const loading = ElLoading.service({
        lock: true,
        text: '正在执行发卡操作，请稍候...',
        background: 'rgba(0, 0, 0, 0.7)'
      })
      
      try {
        let executeCardAuthorizationRes = await executeCardAuthorization(executeCardAuthorizationData)
        
        // 关闭加载效果
        loading.close()
        
        if(executeCardAuthorizationRes && !executeCardAuthorizationRes.success){
          return reject(new Error(executeCardAuthorizationRes.message))
        }
        return resolve(executeCardAuthorizationRes.data)
        
      } catch (error) {
        // 确保在出错时也关闭加载效果
        loading.close()
        return reject(error)
      }
    }
  })
~~~





## ElMessageBox.confirm 确认框的使用

我明白了，您遇到的这个错误 `"Uncaught runtime errors: ERROR cancel"` 是因为 `ElMessageBox.confirm` 在点击“取消”或关闭按钮时会**抛出一个被拒绝的 Promise**，而这个 Promise 在当前上下文中没有被 `.catch()` 捕获。

让我们来详细解释一下这个问题，以及如何解决它。

### 问题根源：`ElMessageBox.confirm` 的 Promise 行为

`ElMessageBox.confirm` 返回的是一个 `Promise`。这个 `Promise` 的行为是这样的：

1.  **点击“确认”**: `Promise` 会 `resolve()`。
2.  **点击“取消” 或 点击右上角关闭按钮**: `Promise` 会 `reject()`。

在您当前的代码中：

```javascript
ElMessageBox.confirm(executeCardAuthorizationRes.message, '提示', {
  confirmButtonText: '确定',
  showClose: true // 允许点击右上角关闭
})
// 这里缺少了 .catch() 来处理 ElMessageBox.confirm 抛出的 reject
```

当您点击“取消”或关闭按钮时，`ElMessageBox.confirm` 内部的 `Promise` 立即 `reject`，但由于您没有使用 `.catch()` 来捕获这个 `reject`，它就变成了一个**“未捕获的 Promise Rejection”**，因此浏览器会在控制台打印出 `Uncaught runtime errors: ERROR cancel`。

而点击“确认”时，`Promise` 是 `resolve` 的，所以不会有这个错误。

### 解决方案：捕获 `ElMessageBox.confirm` 的拒绝

为了解决这个问题，您需要使用 `.catch()` 来捕获 `ElMessageBox.confirm` 返回的 Promise 的拒绝。

**修改方案：**

~~~js
  if (faceAuthorizeCardRes && !faceAuthorizeCardRes.success) {
    // 错误信息提示使用确认框，点击确认后关闭
    ElMessageBox.confirm(faceAuthorizeCardRes.message, '提示', {
      confirmButtonText: '确定',
      showClose: true
    }).catch(() => {
    })
    console.error('人脸授卡出错:', faceAuthorizeCardRes.message);
    return
  }
~~~





## 切换页面、调整F12窗口大小时，element-plus报错： ResizeObserver loop completed with undelivered notifications.



参考：https://blog.csdn.net/zhangyunwei_Blog/article/details/136562707

~~~js
// 给所有 ResizeObserver 的回调添加全局防抖补丁，避免在同一轮布局/渲染中产生无限接近死循环的观察通知导致报错：ResizeObserver loop completed with undelivered notifications.
const debounce = (fn, delay) => {
  let timer = null;
  return function () {
    let context = this;
    let args = arguments;
    clearTimeout(timer);
    timer = setTimeout(function () {
      fn.apply(context, args);
    }, delay);
  }
}
const _ResizeObserver = window.ResizeObserver;
window.ResizeObserver = class ResizeObserver extends _ResizeObserver{
  constructor(callback) {
    callback = debounce(callback, 16);
    super(callback);
  }
}
~~~



### 每行代码解释

```ts
const debounce = (fn, delay) => {
```
- **第 28 行**：定义一个名为 `debounce` 的函数，接收两个参数：  
  - `fn`: 需要被“防抖”的原始函数  
  - `delay`: 延迟时间（毫秒）

```ts
  let timer = null;
```
- **第 29 行**：在 `debounce` 的闭包中定义一个 `timer` 变量，用来保存 `setTimeout` 返回的定时器 ID。

```ts
  return function () {
```
- **第 30 行**：`debounce` 返回一个新的函数，以后真正被调用的是这个“包装后”的函数，而不是 `fn` 本身。

```ts
    let context = this;
```
- **第 31 行**：保存当前函数调用的 `this` 上下文，后面执行 `fn` 时要用同样的 `this`。

```ts
    let args = arguments;
```
- **第 32 行**：保存当前函数调用的所有参数（`arguments`），后面执行 `fn` 时原样带进去。

```ts
    clearTimeout(timer);
```
- **第 33 行**：每次调用这个包装后的函数，先把上一次尚未执行的定时器清掉，避免短时间内多次触发。

```ts
    timer = setTimeout(function () {
```
- **第 34 行**：重新开启一个新的定时器，在 `delay` 毫秒后执行内部函数。

```ts
      fn.apply(context, args);
```
- **第 35 行**：到达延迟时间后，以原来的 `this` 和原来的参数调用真正的函数 `fn`。

```ts
    }, delay);
```
- **第 36 行**：设置定时器的延迟时间为 `delay` 毫秒。

```ts
  }
```
- **第 37 行**：结束返回的包装函数。

```ts
}
```
- **第 38 行**：结束 `debounce` 函数定义。

---

```ts
const _ResizeObserver = window.ResizeObserver;
```
- **第 40 行**：把浏览器原生的 `ResizeObserver` 构造函数保存到本地变量 `_ResizeObserver` 中，防止后面重写 `window.ResizeObserver` 之后丢失原始实现。

```ts
window.ResizeObserver = class ResizeObserver extends _ResizeObserver{
```
- **第 41 行**：重写全局的 `window.ResizeObserver`，定义一个新的类 `ResizeObserver`，继承自原来的 `_ResizeObserver`。  
  - 之后所有新建的 `new ResizeObserver(...)` 都会用到这个“包装后的”类。

```ts
  constructor(callback) {
```
- **第 42 行**：重写这个新类的构造函数，接收一个回调 `callback`，这是用户传给 `ResizeObserver` 的回调。

```ts
    callback = debounce(callback, 16);
```
- **第 43 行**：对用户传入的 `callback` 做“防抖”包装：
  - 把原来的 `callback` 替换成一个防抖后的版本  
  - 延迟时间是 `16` 毫秒，大约等于一帧（60fps ≈ 16.67ms）

```ts
    super(callback);
```
- **第 44 行**：调用父类（原生 `_ResizeObserver`）的构造函数，把**防抖后的回调**传进去，而不是直接传原始回调。

```ts
  }
```
- **第 45 行**：结束构造函数。

```ts
}
```
- **第 46 行**：结束新定义的 `ResizeObserver` 类，并完成对 `window.ResizeObserver` 的重写。

---

### 为什么这段代码能缓解 / 解决 “ResizeObserver loop completed with undelivered notifications” 报错？

浏览器报错信息：

> `ResizeObserver loop completed with undelivered notifications.`

这类错误/警告通常出现在以下场景：

- **`ResizeObserver` 的回调里又触发布局变化（修改 DOM 尺寸、样式等）**，导致新的 `resize` 事件；
- 浏览器在同一轮布局/渲染循环中不断产生新的 `ResizeObserver` 通知，形成类似“回调 -> 改尺寸 -> 再触发回调”的循环；
- 最终浏览器检测到“循环 + 通知来不及全部派发”，于是抛出这个错误/警告来中断循环。

你的这段代码通过“防抖”截断了这种高频、递归式的触发链路：

- **核心点 1：把原始回调变成了防抖回调**  
  - 原来：DOM 一有尺寸变化，就立即同步（或非常频繁地）执行 `callback`。  
  - 现在：在 16ms 的时间窗口内，如果多次触发 `ResizeObserver`，真正的 `callback` 只会在“最后一次触发后的 16ms”才执行一次。

- **核心点 2：延迟到下一帧执行，打断同一轮布局的“循环”**  
  - 16ms 大致对应浏览器的一帧时间，等价于“把这个回调推迟到下一帧再执行”。  
  - 这样，`ResizeObserver` 的通知不会在同一轮布局/渲染过程中不断叠加，而是被合并、节流。  
  - 回调中的 DOM 修改（如果有）发生在下一帧，不再反复触发同一轮的 `ResizeObserver` 计算，浏览器自然就不再报 “loop completed with undelivered notifications”。

- **核心点 3：合并多次尺寸变更，减少回调执行次数**  
  - 如果 16ms 内一个元素多次尺寸变化，原来会多次触发回调，现在只触发一次。  
  - 越少的回调执行次数，越难形成“回调触发 -> 改尺寸 -> 再触发”的剧烈循环，错误就更不容易出现。

总结一下：

- **这段代码的本质是给所有 `ResizeObserver` 的回调加了全局防抖（每帧最多执行一次），从而：**
  - 避免在同一轮布局/渲染中产生无限接近死循环的观察通知；
  - 把可能的“同步递归触发”拆散到多帧执行；
  - 因此大幅降低了浏览器抛出  
    `ResizeObserver loop completed with undelivered notifications`  
    这个错误/警告的概率，通常能达到“表面上看起来错误消失”的效果。

如果你愿意，我也可以帮你看看项目里具体是哪些组件或样式操作容易触发这个错误，从源头上优化，而不仅仅依赖这个全局防抖“补丁”。



### 每行再解释一遍（配合执行流程）

```ts
const debounce = (fn, delay) => {
  let timer = null;
  return function () {
    let context = this;
    let args = arguments;
    clearTimeout(timer);
    timer = setTimeout(function () {
      fn.apply(context, args);
    }, delay);
  }
}
```

- **`const debounce = (fn, delay) => { ... }`**  
  定义一个“高阶函数”`debounce`，接收：  
  - `fn`: 你真正想执行的函数  
  - `delay`: 延迟时间（多少毫秒后再执行）  
  它返回的是“包装后的新函数”。

- **`let timer = null;`**  
  在 `debounce` 的内部定义一个变量 `timer`，**这个变量会被后面返回的函数一直“记住”（闭包）**，用来记录上一次设置的定时器 ID。

- **`return function () { ... }`**  
  `debounce` 返回一个新的匿名函数。以后你调用的其实是这个函数，而不是直接调用 `fn`。  
  每次调用这个新函数，都会重新走里面的逻辑。

- **`let context = this;`**  
  记录当前调用时的 `this`（比如某个组件实例或 DOM 元素），后面真正执行 `fn` 时要保持同样的 `this`。

- **`let args = arguments;`**  
  记录当前调用时传进来的所有参数，后面执行 `fn` 的时候原样带进去。

- **`clearTimeout(timer);`**  
  每次调用这个包装函数时，先把**上一次设置但还没执行的定时器清掉**。  
  这就是“防抖”的核心：如果在 `delay` 时间内又触发，就把之前的执行机会作废，重新计时。

- **`timer = setTimeout(function () { fn.apply(context, args); }, delay);`**  
  - 再开启一个新的定时器，`delay` 毫秒后准备执行内部函数。  
  - 到时间后，通过 `fn.apply(context, args)`：  
    - 用刚才保存的 `this`（`context`）  
    - 用刚才保存的参数（`args`）  
    - 去真正执行原始函数 `fn`。  
  - 并且把这个定时器 ID 存回 `timer`，方便下次再被清掉。

---

### 举一个具体例子：搜索输入框防抖

假设你有一个搜索框，每次输入都要调接口搜索，如果用户一直狂敲键盘就会发很多请求，所以用 `debounce` 包一层。

#### 1. 定义原始函数和防抖函数

```js
// 原始函数：真正发请求的函数
function search(keyword) {
  console.log('发请求搜索：', keyword);
}

// 用 debounce 包装，300ms 内只执行最后一次
const debouncedSearch = debounce(search, 300);
```

此时：

- `debounce(search, 300)` 被调用一次
- 在 `debounce` 里面创建了一个私有变量 `timer = null`
- 返回一个新函数，赋值给 `debouncedSearch`
- 以后你只会调用 `debouncedSearch(...)`

#### 2. 模拟用户输入：快速输入 “abc”

用户在 1 秒内快速敲了 3 次：

```js
debouncedSearch('a');   // 第一次输入
debouncedSearch('ab');  // 第二次输入
debouncedSearch('abc'); // 第三次输入
```

我们按时间轴看执行过程（假设三次间隔都 < 300ms）：my：用户 300ms内输入的就是a ab abc

---

**第一次调用：`debouncedSearch('a')`**

- 进入返回的函数：
  - `context = this;`（例如是某个 Vue 组件实例）
  - `args = ['a']`
  - `clearTimeout(timer);`  
    - 此时 `timer` 还是 `null`，什么都不会清
  - `timer = setTimeout(..., 300);`  
    - 浏览器启动一个 300ms 的定时器  
    - 300ms 后准备执行 `search('a')`（带着当前的 `this`）

---

**第二次调用：`debouncedSearch('ab')`（在 300ms 之内）**

- 再次进入返回的函数：
  - `context = this;`
  - `args = ['ab']`
  - `clearTimeout(timer);`  
    - 把**上一次准备执行 `search('a')` 的那个定时器清掉**  
    - 所以 `search('a')` 将不会被执行
  - `timer = setTimeout(..., 300);`  
    - 重新设置一个 300ms 的定时器  
    - 300ms 后准备执行 `search('ab')`

---

**第三次调用：`debouncedSearch('abc')`（同样还在 300ms 之内）**

- 再次进入返回的函数：
  - `context = this;`
  - `args = ['abc']`
  - `clearTimeout(timer);`  
    - 清掉刚才准备执行 `search('ab')` 的定时器  
    - 所以 `search('ab')` 也不会被执行
  - `timer = setTimeout(..., 300);`  
    - 再次启动一个新的 300ms 定时器  
    - 300ms 后准备执行 `search('abc')`

---

**最后：300ms 内不再输入时**

- 如果用户停手了，300ms 内没有再调用 `debouncedSearch(...)`：
  - 定时器不会再被清掉
  - 时间一到，就会真正执行内部的：

    ```js
    fn.apply(context, args); // 等价于 search.call(this, 'abc')
    ```

- 控制台输出：

  ```text
  发请求搜索： abc
  ```

**结果：**

- 虽然用户输入了 3 次（`a`、`ab`、`abc`），实际只发了 **1 次请求**，而且用的是**最后一次输入的值** `abc`。
- 这就是“防抖”的具体执行过程：  
  **在一段时间内不断重置定时器，只保留最后一次触发的执行机会。**

如果你愿意，我也可以结合你项目里实际使用 `ResizeObserver` 或某个具体组件的代码，画一个完整的“回调被防抖后”的执行时序图，让你更直观地理解为什么这能减少错误。
