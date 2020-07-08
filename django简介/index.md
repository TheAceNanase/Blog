# Django简介

# Django 简介

Django 是用Python开发的一个免费开源的Web框架，可以用于快速搭建高性能，优雅的网站！采用了MVC的框架模式，即模型M，视图V和控制器C，也可以称为MVT模式，模型M，视图V，模板T。

它最初是被开发来用于管理劳伦斯出版集团旗下的一些以新闻内容为主的站点的, 并于2005年7月在BSD许可证下公布.这套框架是以比利时的吉普赛爵士吉他手Django Reinhardt来命名的.

Django的主要目标是使得开发复杂的、数据库驱动的网站变得简单。Django 注重组件的重用性和“可插拔性”，敏捷开发和 DRY 法则（Don’t 
Repeat Yourself）。在 Django 中 Python 被普遍使用，甚至包括配置文件和数据模型。

Django 于 2008 年 6 月 17 日正式成立基金会。

### Django 架构分析

![](https://atts.w3cschool.cn/attachments/image/20200611/1591867104705969.jpeg)

### Django 框架的组成部分

Django 框架的核心包括：

* 一个 面向对象 的映射器，用作数据模型（以 Python 类的形式定义）和关系型数据库间的介质；
* 一个基于正则表达式的 URL 分发器；
* 一个视图系统，用于处理请求；
* 一个模板系统。

核心框架中还包括：

* 一个轻量级的、独立的 Web 服务器，用于开发和测试。
* 一个表单序列化及验证系统，用于 HTML 表单和适于数据库存储的数据之间的转换。
* 一个缓存框架，并有几种缓存方式可供选择。
* 中间件支持，允许对请求处理的各个阶段进行干涉。
* 内置的分发系统允许应用程序中的组件采用预定义的信号进行相互间的通信。
* 一个序列化系统，能够生成或读取采用 XML 或 JSON 表示的 Django 模型实例。
* 一个用于扩展模板引擎的能力的系统。


Django 包含了很多应用在它的 contrib 包中，这些包括：

* 一个可扩展的认证系统
* 动态站点管理页面
* 一组产生 RSS 和 Atom 的工具
* 一个灵活的评论系统
* 产生 Google 站点地图（Google Sitemaps）的工具
* 防止跨站请求伪造（cross-site request forgery）的工具
* 一套支持轻量级标记语言（Textile 和 Markdown）的模板库
* 一套协助创建地理信息系统（GIS）的基础框架

### Django 的内置应用

* Django 包含了非常多应用在它的"contrib"包中, 这些包含:
* 一个可扩展的认证系统;
* 动态站点管理页面;
* 一组产生RSS和Atom的工具;
* 一个灵活的评论系统;

* 产生Google站点地图(Google Sitemaps)的工具;
* 防止跨站请求伪造(cross-site request forgery)的工具;
* 一套支持轻量级标记语言(Textile和Markdown)的模板库;
* 一套协助创建地理信息系统(GIS)的基础框架;

### Django 的优缺点总结

#### Django 的优点

* 完美的文档，Django近乎完美的官方文档。
* 强大的URL路由配置，Django让你可以设计出非常优雅的URL。
* 自助管理后台，让你几乎不用写一行代码就拥有一个完整的后台管理界面。
* 全套的解决方案（full-stackframework+ batteries included），基本要什么有什么（比如：cache、session、feed、orm、geo、auth），而且全部Django自己造，开发网站应手的工具Django基本都给你做好了，因此开发效率是不用说的。

#### Django 的缺点

* Template功能比较弱，不能插入Python代码，要写复杂一点的逻辑需要另外用Python实现Tag或Filter。
* URL配置虽然强大，但全部要手写，高手和初识Django的人配出来的URL会有很大差异。
* 自带的ORM远不如SQLAlchemy强大，SQLAlchemy是Python世界里事实上的ORM标准，其它框架都支持SQLAlchemy了，唯独Django仍然坚持自己的那一套。
* Django的auth跟其它模块结合紧密，功能也挺强，但做的有点过了，用户的数据库schema都给你定好了，比如很多网站要求email地址唯一，可schema里这个字段的值不是唯一的。
* 系统紧耦合，如果你觉得Django内置的某项功能不是很好，想用喜欢的第三方库来代替是很难的，比如说的ORM、Template。要在Django里用SQLAlchemy或Mako几乎是不可能，即使打了一些补丁用上了也会让你觉得非常非常别扭。

