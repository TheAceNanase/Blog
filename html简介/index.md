# HTML简介

### 什么是HTML

HTML 是用来描述网页的一种语言。HTML 是一种在 Web 上使用的通用标记语言。HTML 允许你格式化文本，添加图片，创建链接、输入表单、框架和表格等等，并可将之存为文本文件，浏览器即可读取和显示。

    HTML 指的是超文本标记语言: HyperText Markup Language
    HTML 不是一种编程语言，而是一种标记语言
    标记语言是一套标记标签 (markup tag)
    HTML 使用标记标签来描述网页
    HTML 文档包含了HTML 标签及文本内容
    HTML文档也叫做 web 页面

### 入门实例

新建一个test.html文件，内容如下
```
<!DOCTYPE html>
<html>
<head>
<meta charset="utf-8">
<title>ZONGXP</title>
</head>
<body>
    
<h1>我的第一个标题</h1>
<p>我的第一个段落。</p>
    
</body>
</html>
```
其中：
```
<!DOCTYPE html> 声明为 HTML5 文档
<html> 元素是 HTML 页面的根元素
<head> 元素包含了文档的元（meta）数据，如 <meta charset="utf-8"> 定义网页编码格式为 utf-8（由于在大部分浏览器中直接输出中文会出现乱码，所以要在头部将字符声明为UTF-8）
<title> 元素描述了文档的标题
<body> 元素包含了可见的页面内容
<h1> 元素定义一个大标题
<p> 元素定义一个段落
```
保存后运行，即可在浏览器中打开如下界面

### 各部分详解
3.1 标题

HTML 标题（Heading）是通过<h1> - <h6> 标签来定义的.
```
<!DOCTYPE html>
<html>
<head>
<meta charset="utf-8">
<title>ZONGXP</title>
</head>
<body>
    
<h1>这是标题 1</h1>
<h2>这是标题 2</h2>
<h3>这是标题 3</h3>
<h4>这是标题 4</h4>
<h5>这是标题 5</h5>
<h6>这是标题 6</h6>
    
</body>
</html>
```
3.2 段落

HTML 段落是通过标签 <p> 来定义的
```
<!DOCTYPE html>
<html>
<head>
<meta charset="utf-8">
<title>ZONGXP</title>
</head>
<body>
    
<p>这是一个段落。</p>
<p>这是一个段落。</p>
<p>这是一个段落。</p>
    
</body>
</html>
```
3.3 链接

HTML 链接是通过标签 <!--<a>!-->  来定义的
```
<!DOCTYPE html>
<html>
<head>
<meta charset="utf-8">
<title>ZONGXP</title>
</head>
<body>
    
<a href="https://blog.csdn.net/zong596568821xp">这是一个链接使用了 href 属性</a>
    
</body>
</html>
```
3.4 图像

HTML 图像是通过标签 <!--<img>!-->  来定义的。注意： 图像的名称和尺寸是以属性的形式提供的。
```
<!DOCTYPE html>
<html>
<head>
<meta charset="utf-8">
<title>ZONGXP</title>
</head>
<body>
    
<img src="zongxp.jpg" width="640" height="640" />
    
</body>
</html>
```
3.5 表格


* 表格由 <!--<table>!--> 标签来定义。每个表格均有若干行（由 <!--<tr>!-->  标签定义），每行被分割为若干单元格（由 <!--<td>!-->  标签定义）。字母 td 指表格数据（table data），即数据单元格的内容。数据单元格可以包含文本、图片、列表、段落、表单、水平线、表格等等。表格的表头使用 <!--<th>!-->  标签进行定义。如果不定义边框属性，表格将不显示边框。有时这很有用，但是大多数时候，我们希望显示边框。使用边框属性来显示一个带有边框的表格：

```
<table border="1">
    <tr>
        <th>Header 1</th>
        <th>Header 2</th>
    </tr>
    <tr>
        <td>row 1, cell 1</td>
        <td>row 1, cell 2</td>
    </tr>
    <tr>
        <td>row 2, cell 1</td>
        <td>row 2, cell 2</td>
    </tr>
</table>
```

### 速查列表
4.1 基本文档
```
<!DOCTYPE html>
<html>
<head>
<title>文档标题</title>
</head>
<body>
可见文本...
</body>
</html>
```
4.2 基本标签
```
<h1>最大的标题</h1>
<h2> . . . </h2>
<h3> . . . </h3>
<h4> . . . </h4>
<h5> . . . </h5>
<h6>最小的标题</h6>
    
<p>这是一个段落。</p>
<br> （换行）
<hr> （水平线）
<!-- 这是注释 -->
```
4.3 文本格式化
```
<b>粗体文本</b>
<code>计算机代码</code>
<em>强调文本</em>
<i>斜体文本</i>
<kbd>键盘输入</kbd> 
<pre>预格式化文本</pre>
<small>更小的文本</small>
<strong>重要的文本</strong>
    
<abbr> （缩写）
<address> （联系信息）
<bdo> （文字方向）
<blockquote> （从另一个源引用的部分）
<cite> （工作的名称）
<del> （删除的文本）
<ins> （插入的文本）
<sub> （下标文本）
<sup> （上标文本）
```
4.4 链接
```
普通的链接：<a href="http://www.example.com/">链接文本</a>
图像链接： <a href="http://www.example.com/"><img src="URL" alt="替换文本"></a>
邮件链接： <a href="mailto:webmaster@example.com">发送e-mail</a>
书签：
<a id="tips">提示部分</a>
<a href="#tips">跳到提示部分</a>
```
4.5 图片
```
<img src="URL" alt="替换文本" height="42" width="42">
```
4.6 样式/区块
```
<style type="text/css">
h1 {color:red;}
p {color:blue;}
</style>
<div>文档中的块级元素</div>
<span>文档中的内联元素</span>
```
4.7 无序列表
```
<ul>
    <li>项目</li>
    <li>项目</li>
</ul>
```
4.8 有序列表
```
<ol>
    <li>第一项</li>
    <li>第二项</li>
</ol>
```
4.9 定义列表
```
<dl>
    <dt>项目 1</dt>
    <dd>描述项目 1</dd>
    <dt>项目 2</dt>
    <dd>描述项目 2</dd>
</dl>
```
4.10 表格
```
<table border="1">
    <tr>
    <th>表格标题</th>
    <th>表格标题</th>
    </tr>
    <tr>
    <td>表格数据</td>
    <td>表格数据</td>
    </tr>
</table>
```
4.11 框架
```
<iframe src="demo_iframe.htm"></iframe>
```
4.12 表单
```
<form action="demo_form.php" method="post/get">
<input type="text" name="email" size="40" maxlength="50">
<input type="password">
<input type="checkbox" checked="checked">
<input type="radio" checked="checked">
<input type="submit" value="Send">
<input type="reset">
<input type="hidden">
<select>
<option>苹果</option>
<option selected="selected">香蕉</option>
<option>樱桃</option>
</select>
<textarea name="comment" rows="60" cols="20"></textarea>
</form>
```
4.13 实体
```
&lt; 等同于 <
&gt; 等同于 >
&#169; 等同于 ©

```


