# jQuery学习笔记

### **1.简介**

jQuery是一款开放源代码的JavaScript库，它极大地简化了JavaScript编程。它提供了一个简单有效的方式来操纵网页文档，创建动画效果，处理事件，以及实现各种交互效果。



### **2.添加jQuery库到你的项目中**

```html
    <script src="https://ajax.googleapis.com/ajax/libs/jquery/3.5.1/jquery.min.js"></script>
```



### **3.基本语法**

jQuery基本语法是： `$(selector).action()`

- `$` 访问jQuery
- `selector` 是用于查找HTML元素
- `action()` 需要执行的操作

例如：当点击按钮时，隐藏当前按钮：

```javascript
    $(document).ready(function(){
      $("button").click(function(){
        $(this).hide();
      });
    });
```



### 4.**选择器**

a. **基础选择器**

plaintext

```plaintext
 `'*'` 选择所有元素
 `'p'` 选择所有段落元素
 `'.class'` 选择class class的元素
 `'#id'` 选择id id的元素
```



b. **层次选择器**

plaintext

```plaintext
 `'ul li'` 选择ul下的所有子孙li元素
 `'ul > li'` 选择ul下的子li元素
```



### **5.事件**

- `click()`
- `dblclick()`
- `mouseenter()`



### **6.jQuery AJAX**

AJAX允许异步获取数据并更新网页。jQuery提供了多种AJAX方法来加载远程数据。例如，你可以用`$.ajax()`函数，或者更简单的`$.get()`和`$.post()`方法：



```javascript
    $.ajax({url: "test.html", success: function(result){
        $("#div1").html(result);
    }});
```



### **7.动画和效果**

jQuery提供了一些简单但非常强大的动画函数。例如你可以用`slideDown()`, `slideUp()`, `fadeIn()`, `fadeOut()`, 以及`animate()`等函数来添加动画效果：



```javascript
   $("#div1").slideUp();
   $("#div2").fadeIn();
```



### 8.**DOM操作**

jQuery提供了一套强大的DOM操作功能，包括获取和设置内容，获取和设置属性，添加和删除节点等。例如：

```javascript
   $("p").text("Hello world!"); // 设置所有段落的文本
   $("#div1").remove(); // 移除id为div1的元素
```



### 9.**事件处理**

jQuery提供了一套完善的事件处理机制。你可以很方便的添加和移除事件处理函数，阻止事件默认行为，或者创建自定义事件等：

```javascript
   $("p").click(function(){
       alert("The paragraph was clicked.");
   });
```



### **10.jQuery 插件**

有许多优秀的jQuery插件，你可以使用它们提供的更高级或特定的功能，例如轮播图插件，日期选择器插件，数据表格插件等。



### **11.jQuery 延迟对象和Promise**

jQuery扩展了JavaScript的Promise模式，允许你在异步操作（如AJAX请求）完成后执行特定的代码。例如, 你可以用`.done()`, `.fail()`, `.always()`, 和 `.then()`:

```javascript
    $.ajax({url: "test.html"})
        .done(function() { alert('AJAX 请求成功！'); })
        .fail(function() { alert('AJAX 请求失败！'); });
```



### **12.Chaining**

jQuery允许用链式语法来编写代码，这让你的代码更紧凑和易读。你可以把多个操作链接在一起，一次执行。例如:

```javascript
    $("button").click(function(){
        $("#div1").fadeIn().css("color", "red").slideUp();
    });
```



### **13.自定义jQuery方法**

jQuery允许你创建自定义的方法或插件。例如，你可以创建一个让元素闪烁的方法：

```javascript
   jQuery.fn.flash = function(duration) {  
        let color = this.css('color'); 
        this.animate({color: 'white'}, duration)
            .animate({color: color}, duration);
        return this;  
    };
    $("p").flash(1000);
```



### **14.jQuery 与其他库的冲突**

如果你的页面还包含其它库，可能会发生`$`符号的冲突。你可以用jQuery的`$.noConflict()`方法来解决这个问题。例如：

```javascript
   var jq = $.noConflict();
   jq(document).ready(function() {
     jq("button").click(function() {
       jq("p").text("jQuery is still working!");
     });
   });
```



### **15.jQuery UI**

jQuery UI是基于jQuery的一组用户界面交互、特效、小部件以及主题。你可以使用它来创建更复杂的动画效果，或者创建如对话框、滑块、选项卡、拖放、日期选择器等交互式组件。