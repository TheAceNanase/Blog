# Hexo优化之使用GooglePrettify高亮代码


虽然Hexo自带了代码高亮的功能，但是渲染生成后的代码是使用table，然后一大堆的tr、td标签。而且试了好几种方法都没能让代码自动换行。想起了之前在Wordpress上使用的Google Prettify插件，于是尝试使用prettify对代码进行高亮显示。

首先关闭Hexo的代码高亮功能，修改_config.yml：
```
    highlight:
      enable: false
      line_number: false
      auto_detect: false
      tab_replace:
```
然后下载prettify，并引入css和js文件，
```
    <link rel="stylesheet" href="/vendors/prettify/prettify.css" type="text/css">
    ...
    <script src="/vendors/prettify/prettify.min.js" type="text/javascript"></script>
```
最后在网页加载完成之后调用即可。
```
    $(window).load(function(){
     $('pre').addClass('prettyprint linenums').attr('style', 'overflow:auto;');
       prettyPrint();
     })
```
关于代码的自动换行，在你的css文件中添加如下的样式即可：
```
    pre{
        word-break: break-all;
        word-wrap: break-word;
      }
```
关于代码高亮的主题，可以在这个网站http://jmblog.github.io/color-themes-for-google-code-prettify/选择一套你喜欢的主题替换上面的prettify.css即可。
