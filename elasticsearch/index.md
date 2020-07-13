# Elasticsearch



## Elasticsearch 是什么？

Elasticsearch 是一个分布式的、开源的搜索分析引擎，支持各种数据类型，包括文本、数字、地理、结构化、非结构化。
Elasticsearch 是基于 Apache Lucene 的。
Elasticsearch 因其简单的 REST API、分布式特性、告诉、可扩展而闻名。
Elasticsearch 是 Elastic 产品栈的核心，Elastic 产品栈是个开源工具集合，用于数据接收、存储、分析、可视化。

## 一个搜索和分析引擎

Elasticsearch 可以让你存储所有类型的数据。
你可能认为搜索是关于文本的，的确，Elasticsearch 精通索引和查询文本。
但是，那不是全部，你还可以存储数字类型的数据、Geo地理类型的数据。
Elasticsearch 不仅可以查询数据，还可以做汇总、聚合等等操作。

### 开源

Elasticsearch 是免费、开源的。

Elasticsearch 所属的 Elastic 公司，是一家商业盈利性质的公司，但你并不需要因为使用 Elasticsearch 而付费。

Elastic 公司使用的是增值服务模式，你付费的话可以得到更多的支持和产品特性。

### 一个完整的生态

Elasticsearch 是 Elastic 产品栈的核心。

其中的工具可以帮助你实现可视化（Kibana）、接入（Beats、Logstash）和管理存储在 Elasticsearch 中的数据。

除了官方工具，还有大量免费和商用的工具库可以使用。

### 弹性

一是 Elasticsearch 可以轻松进行节点扩展。

二是你可以非常轻松的使用 Elasticsearch，非常容易起步，而且，还通过多种方式帮助你成功的使用在产品环境中。

### 分布式

可扩展性是 Elasticsearch 的一个巨大优势。

在你起步的时候，可以使用一个节点，在壮大之后，Elasticsearch 可以轻松的扩展。

添加物理节点，然后在配置文件中列出即可。

在新节点加入之后，你的 indexes 会自动分布到新的节点。

## 可以用来做什么？

使用场景例如：

- 文档存错查询

    可以很好地存储和查询文档，用于应用程序搜索、企业搜索和网站搜索。

- 日志存储和索引

    使用 ELK，轻松存储和分析日志。

    ELK 还通常用于监控基础信息、应用程序性能和使用情况。

- 地理数据存储和分析
- 商业智能平台

    在各类场景中，可以抽象出2种数据类型：

    1. 静态数据

    Elasticsearch 用作搜索引擎。

    2. 时间序列数据

    时序数据发送到 Elasticsearch，用于产品分析、报告、异常检测 ……
