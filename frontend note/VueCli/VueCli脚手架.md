# VueCli脚手架



## `mock` 文件夹主要用于 **前端开发环境下的接口模拟

[6 tools called]

这个 `mock` 文件夹主要用于 **前端开发环境下的接口模拟（API Mocking）**。

### 1. 这个文件夹是做什么的？
它的作用是搭建一个**本地的虚拟后端服务器**。在真实的后端接口还没有开发完成，或者你需要离线开发时，可以通过它来拦截前端发出的 HTTP 请求，并返回伪造的数据（Mock Data）。

从代码结构来看：
*   **`index.js`**: 是 Mock 服务器的入口文件，它导出了一个中间件函数，用于配置 Webpack Dev Server。它会监听请求并匹配 `controller` 中的路由规则。
*   **`controller/`**: 存放具体的接口定义。例如 `router.js` 里定义了 `/router/getList` 接口的返回数据。
*   **`utils/`**: 存放辅助工具，例如自动读取 `controller` 目录下所有文件的逻辑。

### 2. Mock 是前端的单元测试吗？
**不是。**
*   **单元测试 (Unit Test)**：通常指使用 Jest、Mocha 等框架对函数、组件进行逻辑测试（例如：输入 A 是否输出 B）。
*   **这里的 Mock**：指的是 **服务虚拟化**。它是在网络层面拦截 AJAX/Fetch 请求。你不需要写真正的测试用例代码，只需要定义“当请求 URL 为 X 时，返回 JSON 数据 Y”。

### 3. 如何使用？

#### 第一步：启用 Mock 服务
在你的 `vue.config.js` 文件中，我注意到 Mock 功能目前是被**注释掉（禁用）**的：

```javascript:SafetyLinkNovo/SafetyLinkNovo/DoorSysPlatform/web-client/vue.config.js
// ...
    devServer: {
// ...
    //setupMiddlewares: require('./mock'), // 禁用Mock，使用真实后端
// ...
```

如果要使用，你需要**取消注释**这一行：
```javascript
setupMiddlewares: require('./mock'),
```
*(注意：代码注释中提到，Webpack 5 下前端代理 `proxy` 可能无法与 mock 同时使用，如果开启了 mock，代理可能会失效)*

#### 第二步：添加一个新的 Mock 接口
在 `mock/controller/` 目录下新建一个 JS 文件（例如 `test.js`），或者修改现有文件。

**格式示例：**
```javascript
// mock/controller/test.js
const { mock } = require('mockjs')

module.exports = [
  {
    url: '/api/getUserInfo', // 接口地址
    type: 'get',             // 请求方法: get, post, put, delete
    response(req, res) {     // 响应回调
      // req.query 获取 URL 参数
      // req.body 获取 POST 参数
      
      // 返回模拟数据
      return {
        code: 200,
        msg: 'success',
        data: mock({
          'id|+1': 1,           // mockjs 语法：自增 ID
          'name': '@cname',     // mockjs 语法：随机中文名
          'age|18-60': 1,       // mockjs 语法：18-60 随机数
          'roles': ['admin']
        })
      }
    },
  },
]
```

#### 第三步：重启项目
修改配置或添加 Mock 文件后，通常需要重启 `npm run serve`，Mock 服务就会生效。前端请求 `/api/getUserInfo` 时就会收到你定义的随机数据。

## 单元测试环境

[4 tools called]

根据你的 `package.json` 文件分析，你的项目已经配置好了单元测试环境（看到了 `"test:unit": "vue-cli-service test:unit"` 和 `@vue/test-utils` 依赖）。通常 Vue CLI 项目默认使用的是 **Jest** 作为测试框架。

你不需要修改现有业务代码，只需要在项目中添加 `*.spec.js` 或 `*.test.js` 文件即可。

以下是两个具体的例子，分别展示如何测试**纯 JS 函数**和**Vue 组件**。

### 1. 测试纯 JS 函数（逻辑测试）

假设你有一个工具函数文件 `src/utils/math.js`（这是假设的文件，你不必真的创建，仅作说明）：

```javascript
// src/utils/math.js
export function add(a, b) {
  return a + b;
}

export function isAdult(age) {
  return age >= 18;
}
```

你应该在 `tests/unit/` 目录下创建一个对应的测试文件，例如 `tests/unit/math.spec.js`：

```javascript
// tests/unit/math.spec.js
import { add, isAdult } from '@/utils/math' // 引入你要测试的函数

// describe 定义一个测试套件（一组相关的测试）
describe('Utils: math.js', () => {
  
  // it 定义一个具体的测试用例
  it('add 函数应该能正确计算两个数字的和', () => {
    // 准备数据 & 执行函数
    const result = add(1, 2);
    
    // expect 断言结果是否符合预期
    expect(result).toBe(3); 
  });

  it('isAdult 函数应该正确判断是否成年', () => {
    expect(isAdult(20)).toBe(true);  // 20岁应该是成年
    expect(isAdult(10)).toBe(false); // 10岁应该未成年
  });
  
});
```

### 2. 测试 Vue 组件（UI 与交互测试）

假设你有一个简单的按钮组件 `src/components/MyButton.vue`：

```vue
<!-- src/components/MyButton.vue -->
<template>
  <button @click="handleClick">{{ label }}</button>
  <div v-if="clicked">已点击</div>
</template>

<script>
export default {
  props: ['label'],
  data() {
    return { clicked: false }
  },
  methods: {
    handleClick() {
      this.clicked = true;
      this.$emit('submit');
    }
  }
}
</script>
```

对应的测试文件 `tests/unit/MyButton.spec.js` 如下：

```javascript
// tests/unit/MyButton.spec.js
import { shallowMount } from '@vue/test-utils' // Vue 官方测试工具
import MyButton from '@/components/MyButton.vue'

describe('Component: MyButton.vue', () => {
  
  it('应该正确渲染 props 传入的文字', () => {
    // 1. 挂载组件
    const wrapper = shallowMount(MyButton, {
      props: { label: '提交' }
    });
    
    // 2. 检查文字内容
    expect(wrapper.text()).toContain('提交');
  });

  it('点击按钮后应该改变状态并触发事件', async () => {
    const wrapper = shallowMount(MyButton, {
      props: { label: '测试' }
    });

    // 初始状态下，不应该显示 "已点击"
    expect(wrapper.text()).not.toContain('已点击');

    // 1. 模拟点击按钮
    // find('button') 查找 DOM 元素，trigger('click') 触发事件
    await wrapper.find('button').trigger('click');

    // 2. 检查数据变化：clicked 应该变为 true
    // 界面上应该出现 "已点击"
    expect(wrapper.text()).toContain('已点击');

    // 3. 检查是否向父组件发送了 'submit' 事件
    expect(wrapper.emitted()).toHaveProperty('submit');
  });
  
});
```

### 3. 如何运行测试

在你的终端中运行以下命令，它会自动寻找并执行所有 `*.spec.js` 文件：

```bash
npm run test:unit
```

### 总结
*   **不修改代码**：你完全不需要改动现有的业务逻辑，测试代码是独立的。
*   **核心思想**：给定一个输入（参数或操作），断言（Expect）输出是否符合预期。
*   **好处**：当你修改了老代码（重构）时，运行一下测试，如果全是绿色的（Passed），你就知道没有改坏以前的逻辑。
