# asp.net的使用

## 1ASP.NET Web Pages 开发模式

## Razor,c#

主要的 Razor C# 语法规则:

- Razor 代码块包含在 @{ ... } 中
- 内联表达式（变量和函数）以 @ 开头
- 代码语句用分号结束
- 变量使用 var 关键字声明
- 字符串用引号括起来
- C# 代码区分大小写
- C# 文件的扩展名是 .cshtml



~~~c#
@{ var myMessage = "Hello World"; }
<p>The value of myMessage is: @myMessage</p>
@{
var greeting = "Welcome to our site!";
var weekDay = DateTime.Now.DayOfWeek;
var greetingMessage = greeting + " Today is: " + weekDay;
}
// 在网页设计中，p标签常用于包裹文本段落，使文本具有段落结构。
<p>The greeting is: @greetingMessage</p>
~~~

