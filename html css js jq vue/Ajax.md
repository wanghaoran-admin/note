# 第一章 Ajax基本概念

### 1.概念

ajax不是一项新的技术，是在web请求响应基础上的优化

他是由前端的javascript+html+css+xml构成

核心操作，可以进行异步功能实现【asynchronous】

所谓异步：通俗来说，以做饭为例，在蒸煮米饭的同时，可以进行炒菜的操作，最终同时完成两个内容；程序中，就是在一次请求响应操作过程中，页面可以进行其他功能的操作，无需等待其完成后再进行。

### 2.Web的发展历程

Web大体发展分为三个阶段

Web1.0时代：上世纪90年代出现，只读时代，服务器厂商【新浪、网易、搜狐等】提供静态网页供用户查看，网络人群相对较少，无法进行交互，用户体验度较差

Web2.0时代：2000以后到现今，主打“交互”，用户在提供的服务器【Facebook，QQ，微博】上发表自己的内容，用户群体量大，体验更强

Web3.0时代：10年后至今，主打数据的"去中心化"，主要以各种APP应用为主

2.0时代中的一个核心技术就是Ajax



# 第二章 原生Ajax

### 1.传统Web和Ajax区别

| 差异               | 方式 | 说明                                 |
| ------------------ | ---- | ------------------------------------ |
| 请求方式不同       | 传统 | 发送的是同步请求                     |
|                    | Ajax | 可发送同步、异步请求                 |
| 响应方式不同       | 传统 | 响应完整页面                         |
|                    | Ajax | 响应所需数据                         |
| 客户端处理方式不同 | 传统 | 响应加载所有功能后才能进行操作       |
|                    | Ajax | 动态更新页面局部内容，不影响其他操作 |

### 2.Ajax核心对象

XMLHttpRequest对象，即使Ajax核心引擎对象，通过它的一些列属性，方法，事件来完成Ajax的所有功能

属性

**1 readyState：**Ajax引擎对象的状态值，由0-4共5个状态构成

| 状态值 | 状态说明         |
| ------ | ---------------- |
| 0      | 正在初始化       |
| 1      | 正在发送请求     |
| 2      | 请求发送完毕     |
| 3      | 正在接收响应结果 |
| 4      | 响应结果接受完毕 |

**2 status：**HTTP的状态码，由200，300，400，500等系列构成，常规值如下

| 状态值 | 状态说明                                 |
| ------ | ---------------------------------------- |
| 200    | 正常                                     |
| 404    | 资源未找到                               |
| 500    | 程序内部错误，如越界，空指针，类型转换等 |

**3 responseText：**响应的文本内容，由后端程序的out.print方法进行传递

方法

**1 open：**用来创建HTTP请求对象，携带参数

type：提交数据的类型方式【post、get】

url：请求的资源路径

async：是否同异步【true为异步，false为同步】

**2 send：**发送请求到服务器，可以携带请求文本数据，如果不携带，直接在url进行拼接，需要赋值null

**3 setRequestHeader：**如果是post提交，并且请求的数据在send方法中传递提交的话，需要进行该方法的设定，否则服务器接收不到相关数据

```javascript
xhr.setRequestHeader("content-type", "application/x-www-form-urlencoded");
```

事件

**1 onreadystatechange：**引擎对象状态从0-4进行变化的时候都会触发该事件，通常给这个事件赋值一个回调函数，在函数中进行数据的响应处理

### 3.Ajax查重的核心代码

```javascript
//判断非空
function checks() {
    var name = document.getElementById("name").value;
    if(name == ""){
        document.getElementById("msg").innerHTML = "用户名不能为空！";
        return false;
    }
    //调用方法
    doAjax(name);
}
//定义ajax引擎对象
var xhr;
//定义ajax执行函数
function doAjax(name){
    //实例化xhr
    if(window.XMLHttpRequest){
        //IE7及以上和其他浏览器
        xhr = new XMLHttpRequest();
    }else{
        xhr = new ActiveXObject("Microsoft.XMLHTTP");
    }
    //判断
    if(xhr == null){
        alert("ajax创建失败！");
    }else{
        //创建请求[post提交]
        xhr.open("post", "AjaxServlet", true);
        //设置请求头
        xhr.setRequestHeader("content-type", "application/x-www-form-urlencoded");
        //发送请求
        xhr.send("name="+name); 
        //事件触发，回调函数
        xhr.onreadystatechange = doBack; 
    }
}
//doBack回调函数
function doBack() {
    //判断正确的响应节点
    if(xhr.readyState == 4 && xhr.status == 200){
        //获取响应结果
        var result = xhr.responseText;
        //显示结果
        document.getElementById("msg").innerHTML = result;
    }
}
```

# 第三章 JQuery-Ajax

### 1.使用缘由

1 原生ajax操作步骤繁琐

2 需要记忆的方法，属性，事件众多

3 复杂数据处理相对困难

4 浏览器的兼容性也有相应要求

而JQuery是一种Javascript的封装函数库，可以更便捷的完成页面动效【包括异步刷新等】

### 2.常用方法

$.ajax

```javascript
$.ajax( {
     "url":"url",         // 要提交的URL路径
     "type":"get",        // 发送请求的方式，默认get
     "data":data,         // 要发送到服务器的数据，可以是字符串，也可以是json
     "dataType":"text",   // 指定传输的数据格式，默认文本
     "success":function(result) { 
         // 请求成功后要执行的代码
	 }
});
```

$.get

```javascript
$.get(url,data,function(data){},"格式，默认text");
第一个参数url：提交的URL路径
第二个参数data：发送到服务器的数据（“op=login”）({“op”:"login"})这两种格式最常用
第三个参数：回调函数
第四个参数：响应格式
```

$.post

```javascript
$.post(url,data,function(data){},"格式，默认text");
第一个参数url：提交的URL路径
第二个参数data：发送到服务器的数据（“op=login”）({“op”:"login"})这两种格式最常用
第三个参数：回调函数
第四个参数：响应格式
```

$.getJSON

```javascript
$.getJSON(url,data,function(data){});
第一个参数url：提交的URL路径
第二个参数data：发送到服务器的数据（“op=login”）({“op”:"login"})这两种格式最常用
第三个参数：回调函数
```

### 3.同步处理

在操作一些功能的时候，我们可能需要把默认异步设置为同步，主要有以下两种方式

1 在$.ajax函数中，使用属性

```javascript
async:false
```

2 直接在JQ中使用全局属性设置

```javascript
$.ajaxSettings.async=false
```

# 第四章 JSON

由于在很多场景下，页面需要Ajax响应数据的类型，数据量等都有很高的要求，纯粹的文本或者xml响应不大能满足页面所需，所以我们可以使用JSON进行响应优化

### 1.JSON基本概念

**JavaScript Object Notation**是JSON的全称

是一种轻量级的数据交换格式

采用独立于语言的文本格式

通常用于在客户端和服务器之间传递数据

### 2.常用JSON数据类型

**对象类型**

```javascript
var stu = {
			"id" : 1,
			"name" : "张三",
			"sex" : "男"
		};
//使用{}进行定义，key:value形式进行属性赋值
//通过对象.属性进行取值
```

**数组类型**

```javascript
var arr = [ "红", "黄", "蓝" ];
//使用[]进行定义
//通过for,或者jq的each函数进行遍历
for (var i = 0; i < arr.length; i++) {
   var r = arr[i]
}
$.each(arr, function(i, n) {
   	//i：遍历的下标
    //n：遍历的数据
});
```

**对象数组类型（集合）**

```javascript
var stus = [ {
    "id" : 1,
    "name" : "张三",
    "sex" : "男"
}, {
    "id" : 2,
    "name" : "张敏",
    "sex" : "女"
}, {
    "id" : 3,
    "name" : "张无极",
    "sex" : "男"
} ];
//通过for,或者jq的each函数进行遍历
```

### 3.JSON数据的转换

json转字符串

```javascript
//data字符串
JSON.stringify(data)
```



字符串转json

```javascript
//datajson数据
JSON.stringify(data)
```



[注意：我们可以使用localStroage进行静态页面间的传递数据操作]





















