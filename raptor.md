##Raptor相关

###1、raptor产品
* frameWork是基于客户端实现的分库分表逻辑设计和实现，所有的分库分表数据连接和查询在用户端实现
* metabase，分库分表的数据源有非常多的参数，静态和动态参数等，动态参数的动态推送由metabase完成
* raptor-server，分库分表的client的权限管理、心跳、异常上报以及异常报警灯
* raptor-console，所有配置的前端管控，配置的更改和展示
* raptor-processor，线上数据库扩容预热功能，实现方式是把线上sql全部改写成select然后再目标数据库执行
* raptor-support，为了方便用户集成开发所开发的集成开发环境，用户只需要添加配置，spring会在启动的时候自动查询配置，扫描dao层，mapper等配置，并生成相应的数据源，事务管理器等等。
* raptor-sequence，全局的唯一id生成器，类似分布式锁的实现，将id的生成状态记录到DB中，并发由mysql的事务进行控制，可以保证多机器并发查访问变更只有一个变更成功。
* raptor-migration，分库分表的数据库的数据迁移，为什么不用db拷贝。第一，量大，没法增量更新；第二，线上的binlog开启的row格式，如果直接删除会把binlog打爆
* pigeon，数据库增量变更消息订阅和分发平台，拉取mysql的binlog日志，然后再本地解析成对应的event，并将消息发送到消息队列，由用户去消费执行相应的逻辑。

###2、核心特征
* 基于jdbc协议做业务隔离
* 动态数据源和连接池
	* 连接池使用阿里的druid管理
	* 动态数据源下发
	* 数据库不同角色适用不同的使用场景
	* 支持全链路压测
	* 弹性连接池，共享连接

* 监控安全体系
* 分库分表

###3、合理的模块化设计
* client，连接Db的客户端
* parser，sql的解析
* filter，sql过滤
* sharding，数据库分片
* plan，生成sql执行计划
* execution，sql的真正执行逻辑
* merger，分发的sql的结果合并
* heartbeat，心跳
* sequence，分库分表的主键生成器
* group/router，分库分表的路由策略
* atom，原子库
* config，动态配置变更
* monitor，状态监控

###2、分库分表的计算拆分、sql改写、结果合并
* count(a)实现
	* sql分发到sql执行，mysql上执行count(a) as num
	* raptor对返回的结果执行合并，sum(num)

* min(a), max(a), sum(a)
	* mysql执行部分min(a) as m1, max(a) as m2, sum(a) as s1
	* raptor结果合并min(m1), max(m2), sum(s1)

* avg(a)结果改写
	* mysql执行sum(a) as s，count(a) as c
 	* raptor结果合并执行sum(s)/sum(c)

* group by a,b
	* mysql执行group by a,b
	* raptor端对多路结果重新分组比较，相同结果做合并

* order by a,b desc
	* mysql端执行order by a,b desc
	* raptor端对多路结果做有序列表的合并

* limit M,N
	* mysql端执行limit 0，M+N
	* raptor端做多路结果合并，然后截取数据，取limit M，N

* select distinct(a)
	* mysql端做select distinct(a)
	* raptor端做多路结果合并去重，重新计算结果

* select count(distinct(a))
	* mysql端做select distinct(a)
	* raptor端对多路返回的结果做合并，然后计算总的结果

* insert into a(x,y) value(1,2)会做主键替换，尤其是分库分表的场景下
	* mysql端做主键替换，插入结果为insert into a(id,x,y) value(${id},1,2)
	* raptor端结果合并的时候结果集ResultSet中要替换掉GeneratedKeys

* explain
	* 正常sql解析、路由、生成路由结果
	* 执行、合并执行结果，每行加入分表的执行信息

* show支持
	* 单表查询
		* 如果是分表，选择第一个分片执行，获取结果并替换表明
		* 如果不是分表，直接在默认库执行返回
	* 查询所有的表
		* 存在默认库

###3、库表路由
