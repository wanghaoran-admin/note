# JavaScript学习笔记

### 1.概念

JavaScript是一种轻量级的编程语言。它是可插入HTML页面的编程代码，JavaScript的基本结构通过变量，数据类型，函数和控制语句等组成。



### 2.变量

```javascript
// 定义变量
var x = 5;
var y = 6;
var z = x + y;  // z 的值为11
```



### 3.数据类型

```javascript
// 数据类型
//JavaScript有不同的数据类型：
//	String(字符串),Number(数值),
//   Boolean(布尔值),Object(对象),
//   Null(空), Undefined(未定义), 
//   Symbol(符号)
var length = 16;                               // Number - 整数
var lastName = "Johnson";                      // String - 字符串
var x = {firstName:"John", lastName:"Doe"};    // Object - 对象
```



### 4.操作符

JavaScript支持各种数学、比较、赋值和逻辑操作符。

```javascript
var x = 10;  // 赋值操作符
x *= 5;  // x = x * 5. 现在，x 的值为50
```



### 5.函数

JavaScript函数是被设计用来执行特定任务的代码块。

```javascript
function sayHello(name) {
  console.log("Hello " + name);
}
```



### 6.控制流

JavaScript支持常见的控制流语句，包括`if`语句, `for`和`while`循环以及`switch`语句。

```javascript
for (var i = 0; i < 10; i++) {
  console.log(i);
}
```



### 7.对象与数组

对象由花括号包围，并定义了一组“键值对”。

```javascript
var car = {
  brand: "Toyota",
  model: "Corolla",
  year: 2005
};
```



数组是一种特殊类型的变量，可以存储多个值。

```javascript
var fruits = ["Apple", "Banana", "Mango"];
```



### 8.错误处理

使用try...catch语句处理JavaScript错误。

```javascript
try {
  adddlert("Welcome guest!");
}
catch(err) {
  console.log(err.message);
}
```



### 9.导入导出

在 JavaScript （JS）中，我们使用 import 和 export 语句来实现模块的功能。这样可以在需要的地方导入使用。



例如，考虑以下 `math.js`文件：

```javascript
// math.js
export const pi = 3.14159;
export function sum(x, y) {
  return x + y;
}

export default function(x, y) {
  return x - y;
}
```



我们使用 `import` 来导入模块。看下面的 `app.js` 文件：

```javascript
// app.js
import { pi, sum } from './math.js';
import subtract from './math.js';

console.log(pi);
console.log(sum(1, 2));
console.log(subtract(5, 3));
```





### 10.json格式和js对象的区别

json格式

```JSON
{
  "firstName": "John",
  "lastName": "Doe"
}
```



js对象

```JavaScript
var person = {
  firstName: "John",
  lastName: "Doe"
};
```

【区别：JSON 对象的键必须使用双引号包围，但是 JavaScript 对象的键可以没有引号。】

