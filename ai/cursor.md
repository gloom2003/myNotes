# 使用方法



### 记得认真review ai写的代码，ai回答结束后先看总结部分

1）

从0开始弄一个项目，全部让ai来写时，记得认真看ai写的代码（Claude3.7写，gemini2.5Pro 解释来review,改的地方大同小异的可以抽查一下，如果可以的话基本上都是可以的，不可以的话后面测试也知道）

先看总结部分，更加容易发现ai的错误。

（最好是和ai一起讨论如何实现【让ai先不写代码，描述一下需求，看有没有问题，有问题请直接提出来】后再去实现），不要全自然语言编程。（参考PLC门禁系统的开发）



### .cursor文件

2）可以根据一个旧项目的编程风格生成.cursor文件，后面让ai基于这个文件进行开发，新项目的编程风格就很像旧项目了



### 让ai阅读整体项目后再进行实现代码

3）可以先让ai阅读整体项目后再进行实现代码，直接把项目给他， 让他进行阅读的话后面就不用指明要修改哪一个文件了（至少少了一些），并且**ai虽然生成的有一些错误，但是大体的框架是对的，有了这个框架后就很容易把复杂的需求简单化，这个时候去review代码，会发现做事情也有了方向**。



## 迁移项目时，让ai先解释需要迁移的代码

同时这步也是把需要迁移的内容告诉ai，让他解释自己说出来就证明他清楚了，最好再让他结合现有的项目进行迁移即可。

## 另一个提示词思路：一句话说出目标，再让ai自己去拆分任务，自己再去审核和引导。



### 审核的方式；需要ai再次改才点击审核

ai生成代码后，要ai再次修改的话才去点审核按钮允许或者拒绝，一时半会不需要再修改的话，代码其实已经保存了，不进行审核则会有一个ai修改的记录，方便我们了解修改的地方在哪里。否则再改不点审核的话，新修改的内容就不明显了。

**注意**：

1. cursor重启后审核记录会消失，提问框的记录会保存。
2. 如果提问框中没有文字等，关闭提问框后再Ctrl + L打开审核记录也会消失，因为这样子会直接新开一个会话。



#### 例子

**以C#项目迁移到Java中为例：**

提示词：

~~~
通读项目：
你先理解一下人脸授卡这部分的代码，包括里面使用的函数，你要用搜索工具去看里面的实现，里面的公共静态的变量，要请求的url的地址，你不知道它的值时要去搜索这个变量初始化的地方，这样子去理解这段代码，因为后面我们要迁移到Java中，你不这样子理解这段代码，而是只看代码的表面而不看代码中函数的实现、变量的值的话是迁移不好代码的。@/MasterRfidCardClient 搜索的范围就在MasterRfidCardClient目录之中了，理解完后写一份报告我看看你理解的怎么样。或者你先通读这个项目然后再来看脸授卡这部分的代码进行理解
~~~

**迁移思路：**

迁移3个按钮的步骤：

1. 对比要迁移功能与之前迁移的不同之处(先确认需求，大体的了解项目)
2. 一个功能的全部进行迁移（会很多细节不到位，先大概审核一下，不影响之前的代码都可以通过）
3. 让ai对每一个环节、函数进行检查（根据C#源代码进行审核，让ai先专门的集中注意力的审一遍，然后再让自己去审核）
4. 优化、复用代码





### 并且可以借鉴Clade Code的思想，让ai维护一份文档来记录你的要求和ai对项目的理解，这样子即使以后需要新开对话，ai也可以快速的根据这个文档来理解需求。



## 添加：如果有任何需求不合理的地方，请指出来告诉我，我们一起讨论。





## 自己写一个示例（语法错误都行），后面再让ai读代码然后改，这样子效率是可以的





## 生活知识问题处理方法

理论配实践 ：ai知识讲解 + b站、小红书、YouTube看前人**实践认证、评论痛点**（再截图给ai评价，互相作证，引出更多的细节问题），不要完全相信ai和他人，靠得住的只有自己，95%的事自己都能做，不得不让别人做也要理解原理





## 全局规则

### 通用规则

~~~
### 核心交互原则
1. **语言**：始终使用**中文**回答。
2. **批判性思维**：若指令存在逻辑漏洞或风险，必须直接指出并提供修正方案，而非盲目执行。

### 通用代码规范
1. **注释与格式**：严禁删除有效注释；逻辑块之间保留空行以提升可读性。
2. **路径规范**：Windows 环境下统一强制使用**正斜杠** (`/`)（如 `src/components`）。
3. **命令记忆**：自动复用历史记录中执行成功的命令格式。

### 前端规范 (Vue + TS)
1. **类型安全**：严禁使用 `any`。必须显式定义 Interface 或 Type。
2. **组件开发**：
   - 单文件过大（>600行）时必须拆分组件或提取 Hooks。
   - 组件名使用 **PascalCase**，文件名与组件名保持一致。
   - 优先使用 `<script setup lang="ts">` 语法。
3. **调试日志**：
   - 级别：统一使用 `console.warn()`。
   - 格式：对象必须使用 `JSON.stringify(obj, null, 4)` 序列化。

### 后端规范 (Java)
1. **阿里规约**：严格遵守《阿里巴巴 Java 开发手册》。
2. **数据模型**：
   - **严禁**使用 `Map/HashMap` 传递业务数据。
   - 必须定义明确的 **POJO/VO/DTO** 实体类，确保类型安全与可维护性。

### Core Interaction Principles
1. **Language**: Always respond in **Chinese**.
2. **Critical Thinking**: Point out logical flaws or risks in instructions immediately and suggest corrections instead of blind execution.

### General Coding Standards
1. **Comments & Format**: DO NOT delete valid comments. Keep blank lines between logic blocks for readability.
2. **Path Convention**: Force use of **forward slashes** (`/`) for file paths (e.g., `src/components`), even on Windows.
3. **Command Memory**: Reuse successful command formats from execution history.

### Frontend Standards (Vue + TS)
1. **Type Safety**: NO `any`. Explicitly define Interfaces or Types.
2. **Component Development**:
   - Split components or extract Hooks if a file exceeds 600 lines.
   - Use **PascalCase** for component names and filenames.
   - Prefer `<script setup lang="ts">` syntax.
3. **Debugging**:
   - Level: Use `console.warn()`.
   - Format: Serialize objects using `JSON.stringify(obj, null, 4)`.

### Backend Standards (Java)
1. **Compliance**: Strictly follow the *Alibaba Java Coding Guidelines*.
2. **Data Modeling**:
   - **FORBIDDEN**: Using `Map/HashMap` for business data transmission.
   - **REQUIRED**: Define explicit **POJO/VO/DTO** classes for type safety and maintainability.
~~~



### 特定项目的规则

~~~
1）
提问者是一个Java程序员，vue2与element也比较熟悉，对C#和C++完全是没有了解过的状态，请基于这个情况多解释一下非vue2与element方面的知识，让提问者可以多理解和学习，最好总结成简洁的笔记的形式，方便提问者后面查阅

2）
写前端时不要使用类似gap这种太新的css来进行实现，因为我的要兼容chrome 69浏览器内核的版本，请改用更传统、兼容性更好的方式
When writing the front-end, do not use new CSS like GAP to implement it, because I need to be compatible with the Chrome 69 browser kernel version. Please use a more traditional and more compatible method.
~~~

