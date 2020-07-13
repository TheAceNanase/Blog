# redis的codis集群


Codis 是一个分布式 [Redis](https://www.oschina.net/p/redis) 解决方案, 对于上层的应用来说, 连接到 Codis Proxy 和连接原生的 Redis Server 没有明显的区别  (不支持的命令列表), 上层应用可以像使用单机的 Redis 一样使用, Codis 底层会处理请求的转发, 不停机的数据迁移等工作,  所有后边的一切事情, 对于前面的客户端来说是透明的, 可以简单的认为后边连接的是一个内存无限大的 Redis 服务.

Codis 由四部分组成:

- Codis Proxy  (codis-proxy)
- Codis Manager (codis-config)
- Codis Redis  (codis-server)
- ZooKeeper

codis-proxy 是客户端连接的 Redis 代理服务, codis-proxy 本身实现了 Redis 协议, 表现得和一个原生的 Redis 没什么区别 (就像 [Twemproxy](http://www.oschina.net/p/twemproxy)), 对于一个业务来说, 可以部署多个 codis-proxy, codis-proxy 本身是无状态的.

codis-config 是 Codis 的管理工具, 支持包括, 添加/删除 Redis 节点, 添加/删除 Proxy 节点, 发起数据迁移等操作.  codis-config 本身还自带了一个 http server, 会启动一个 dashboard, 用户可以直接在浏览器上观察 Codis  集群的运行状态.

codis-server 是 Codis 项目维护的一个 Redis 分支, 基于 2.8.13 开发, 加入了  slot 的支持和原子的数据迁移指令. Codis 上层的 codis-proxy 和 codis-config 只能和这个版本的 Redis  交互才能正常运行.

Codis 依赖 ZooKeeper 来存放数据路由表和 codis-proxy 节点的元信息, codis-config 发起的命令都会通过 ZooKeeper 同步到各个存活的 codis-proxy.

Codis 支持按照 Namespace 区分不同的产品, 拥有不同的 product name 的产品, 各项配置都不会冲突.

目前 Codis 已经是稳定阶段，目前[豌豆荚](http://www.wandoujia.com/)已经在使用该系统。

## Codis

codis是一个代理中间件，当客户端向codis发送指令时，codis负责将指令转发到后面的redis来执行，并将结果返回给客户端。

一个codis实例可以连接多个redis实例，也可以启动多个codis实例来支撑，每个codis节点都是对等的，这样可以增加整体的QPS需求，还能起到容灾功能。

### 槽位关系

codis根据key直接hash到1024个槽位，每个槽位对应后面的一个redis。计算出key的槽位就能将key转发到正确的redis实例。

槽位关系一旦变化，就会涉及所有redis实例，codis用zookeeper来存储槽位关系（集群配置中心），还提供了一个dashboard模块来观察和修改槽位关系。

槽位关系发生变化时，codis会用一个新的命令来查找指定槽位中的所有key，然后迁移每个key到正确的槽位。当请求打在正在迁移的槽位上时，codis会强制对该key进行迁移。

codis有自动均衡功能，在系统空闲时观察每个redis实例对应的槽位数量，不均衡时就会自动迁移。

### codis命令

对于批量获取多个key的命令，codis会向每一个redis发送命令，然后汇总。

codis损失了redis的一些特性，因为原来存在一个redis里的key现在分散了，所以事务不支持了，rename这种涉及两个key的命令也不支持了。

### codis优缺点

codis增加了一层代理，网络开销变大。

codis优点：分布式交给了第三方处理，dashboard功能强大，但是调优参数多。

### 特性：

- 自动平衡
- 使用非常简单
- 图形化的面板和管理工具
- 支持绝大多数 Redis 命令，完全兼容 [twemproxy](http://www.oschina.net/p/twemproxy)
- 支持 Redis 原生客户端
- 安全而且透明的数据移植，可根据需要轻松添加和删除节点
- 提供命令行接口
- RESTful APIs

安装：

- Install go
- go get github.com/wandoulabs/codis
- cd codis
- ./bootstrap.sh
- make gotest
- cd sample
- follow instructions in usage.md
