# 一、HTML

## HTML(HyperText Markup Language)前言

软件架构

B/S

​	Browser/Server   网站

C/S

​	Client/Server	       QQ

HTML的简介、发展史：

万维网联盟（W3C）维护。包含HTML内容的文件最常用的扩展名是.html，但是像DOS这样的旧操作系统限制扩展名为最多3个字符，所以.htm扩展名也被使用。虽然现在使用的比较少一些了，但是.htm扩展名仍旧普遍被支持。

网站：

把所有的网站资源文件（HTML,CSS,JS,图片,视频等）整合到一起(的一个文件夹)

编程语言：解释型和编译型

解释型：HTML、PHP、Javascript，Python

编译型语言：C、C++、Java

WEB前端：HTML+CSS+JavaScript

HTML：结构标准，超文本标记语言，负责通过标签来表达网页的页面结构。

css：外观标准，层叠样式表标记语言，负责通过属性标记来表达网页的外观效果。

## 一、什么是HTML？

超文本标记语言

​	(1) 标签 也叫做 标记

​	(2) html是由标签/标记 和内容组成的

​	(3) 标签 是由 标签名称 和属性组成的

实例：

> <人 肤色=“黄色” 眼镜="很大"></人>

扩展：

使用协议为  http超文本传输协议

普通文本：文字内容

超文本：视频、音频、图片、文字...

## 二、HTML的主体标签

实例

```html
<!DOCTYPE html>  #H5的头   声明文档类型 为html
<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Strict//EN" "http://www.w3.org/TR/xhtml1/TDT/xhtml1-strit.dtd">  #之前的头文件 现在不用
<html>
<head>
	<title>标题内容</title>
  	<meta charset="UTF-8"> #设置字符集
</head>
<body>
  	放html的主体标签
</body>
</html>
```

- html:文件是网页，浏览器加载网页，就可以浏览 
- head:头部分，网页总体信息 
  + title:网页标题
  + meta：网页主体信息，会根据name(author/description/keywords)
  + link:引入外部文件
  + style：写入CSS
  + script：写入JS
- body:身体部分，网页内容

## 三、HTML的标签

标签分为：单标签/单标记 

如：\<br /> /\<br >

双标签/双标记  

如: \<p>\</p>

### 1、 文本标签

1. `<div></div>`  块标签  作用是设定字、画、表格等的摆放位置

2. `<p></p>   `		段落标签  自成一段  会将上下的文字 和它保持一定的距离

3. `<h1>-</h6> `标题标签 字体加粗 自占一行

   注意： 建议一个页面h1只能出现一次

### 2、 图片标签

`<img />` 在网页中插入一张图片

属性：

+ src： 图片名及url地址 (必须属性)
+ alt: 图片加载失败时的提示信息
+ title：文字提示属性
+ width：图片宽度
+ height：图片高度

实例:

```html
<img src="图片地址" title="文字提示" alt="图片加载失败显示得信息" width="宽" height="高" border="边框" />
```

注意：

如果宽和高 只给一个 那么为等比缩放  如果俩个都给 那么会按照 你所给的宽和高来显示

### 3、路径

1. 相对路径
   + ./	当前
   + ../     上一级
2. 绝对路径
   + 一个固定得链接地址(如域名)
   + 从根磁盘 一直到你的文件得路径

     file协议  打开本地磁盘文件得协议（试一下火狐）

### 4、 超链接

\<a href="链接地址" title="提示信息" target="打开方式">点击显示得内容\</a>

属性：

href必须，指的是链接跳转地址

target: 

​	   _blank 新建窗口得形式来打开

​	   _self      本窗口来打开(默认)

title：文字提示属性（详情）

### 5、 列表

1. ##### 无序列表

   ```html
   <ul>
   	<li></li>  
   </ul>
   <!--
   属性：
   	type 显示得类型
   	square 方形显示
   	disc 默认圆点
   	circle 空心圆
   -->
   ```

2. ##### 有序列表

   ```html
   <ol>
    	<li></li>
   </ol>
   <!--
   属性 
   	type i a A  
   	start 起始值
   -->
   ```
   
3. ##### 自定义列表

   ```html
   <dl>
     	<dt>列表头</dt>
     	<dd>列表内容</dd>
   </dl>
   ```


### 6、 HTML注释

多行注释：<!--注释的内容-->

注释的作用：

1. 代码的调试
2. 解释说明

## 四、iframe

### 1、定义和用法

iframe 元素会创建包含另外一个文档的内联框架（即行内框架）。

### 2、使用

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
</head>
<body>

<iframe src="http://mediaplay.kksmg.com/2022/07/25/h264_720p_600k_39038-DFTVHD-20220725175000-4800-310117-600k_mp4.mp4"></iframe>

<p>一些老的浏览器不支持 iframe。</p>
<p>如果得不到支持，iframe 是不可见的。</p>

</body>
</html>
```



   ## 五 TABLE表格

table表格

### 1、属性：

+ width 宽
+ height 高
+ border 边框
+ rowspan合并行
+ colspan合并列

### 2、标签：

caption 表格标题

tr	行标签

th  列头标签
td  列标签

实例：

   ```html
<table>
  <caption>我是表格的标题</caption>
  <tr>
    <th>我是表头</th>
    <th>我是表头</th>
    <th>我是表头</th>
  </tr>
  <tr>
    <td>我是单元格</td>
    <td>我是单元格</td>
    <td>我是单元格</td>
  </tr>
</table>
   ```



   ## 六 FORM表单

标签： `<form></form>`

### 1、 form属性

```html
action	提交的地址
method	提交的方式
	get
		(1) 默认不写 为get传参  url地址栏可见
		(2) 长度受限制 （IE浏览器2k火狐8k）
		(3) 相对不安全
	post
		(1) url地址栏不可见 长度默认不受限制
		(2) 相对安全
```

### 2、input 标签

`<input>` 表单项标签input定义输入字段，用户可在其中输入数据。在 HTML 5 中，type 属性有很多新的值。

具体在下面有详解：

如：

`<input type="text" name="username">`

### 3、  select 标签创建下拉列表。

属性：

+ name属性:定义名称,用于存储下拉值的


##### 内嵌标签：

`<option>`  下拉选择项标签,用于嵌入到`<select>`标签中使用的;

##### 属性：

+ value属性:下拉项的值

+ selected属性:默认下拉指定项.

### 4、 textarea 多行的文本输入区域

属性：

+ \ name :定义名称,用于存储文本区域中的值。

+ cols ：规定文本区内可见的列数。

+ rows ：规定文本区内可见的行数。


### 5、input 标签

type属性:表示表单项的类型:

值如下:

+ text:单行文本框
+ password:密码输入框
+ checkbox:多选框 注意要提供value值
+ radio:单选框   注意要提供value值
+ file:文件上传选择框
+ button:普通按钮
+ submit:提交按钮
+ reset:重置按钮, 还原到开始(第一次打开时)的效果

## 七、HTML中HEAD头部设置

设置网页编码：

> `<meta charset="utf-8"/>`

网页标题：

> `<title>本网页标题</title>`

导入CSS文件：

> `<link type="text/css" rel="stylesheet" href=".css"/>`

CSS代码：

> `<style type="text/css">嵌入css样式代码</style>`

JS文件或代码： 

> `<script >。。。</script>`
