# CSS学习笔记

## 一、CSS基础

CSS是Cascading Style Sheets（层叠样式表）的缩写，主要用于设置HTML内容的布局和样式。



### 1、CSS引入方法

CSS主要有三种引入方式：

- 行内样式：直接在HTML元素中使用`style`属性，如``
- 内部样式：在``标签中使用``标签定义CSS规则
- 外部样式： 利用``标签引入外部`.css`文件



### 2、CSS选择器

CSS选择器用于选定你想要样式化的HTML元素。主要有以下几种：

- 元素选择器：使用元素名称选取元素，如`p {}`
- 类选择器：使用`.classname`选取类，如`.intro {}`
- ID选择器：使用`#idname`选取特定ID，如`#name {}`
- 属性选择器：使用`[attribute]`选取具有特定属性的元素，如`[href] {}`
- 伪类选择器：用于选取处于特定状态的元素，如`a:hover {}`



## 二、CSS盒模型

CSS盒模型主要由以下部分组成：

- 内容（Content）
- 填充（Padding）
- 边框（Border）
- 外边距（Margin）

每个部分都可以通过CSS进行样式设置。



## 三、CSS常用属性

以下是一些常用的CSS属性：

- `color`：定义文本颜色
- `font-size`、`font-family`：定义字体大小和字体家族
- `background-color`：定义背景颜色
- `width`、`height`：定义元素的宽度和高度
- `border`：定义元素的边框
- `margin`、`padding`：定义元素的外边距和内边距
- `text-align`：定义文本的水平排列方式
- `vertical-align`：定义文本的垂直排列方式
- `display`：定义元素应该生成的框的类型



## 四、CSS布局

CSS主要有三种布局模式：

- 流动模型（Flow）
- 浮动模型（Float）
- 定位模型（Positioning）



## 五、CSS3新增特性

CSS3增加了许多强大的新特性，如：

- 圆角 (`border-radius`)
- 渐变（`gradient`）
- 阴影 (`box-shadow`, `text-shadow`)
- 2D和3D变换 (`transform`)
- 过渡（`transition`）
- 动画（`animation`）
- 媒体查询（`media query`） 