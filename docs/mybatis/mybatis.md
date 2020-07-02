##Mybatis相关

###1、基本构成
（1）SQLSessionFactoryBuilder，根据配置信息生成SqlSessionFactory

（2）SqlSessionFactory，session工厂用来生成session

（3）SqlSession，可以发送sql去执行然后获得返回结果，也可以获取mepper的接口

（4）Sql Mapper， 由java的interface和xml（或者在dao层上直接加入注解）文件构成的概念，它负责发送sql去执行并返回结果。


###2、声明周期
（1）SqlSessionFactoryBuilder

* 利用xml或java编码获取资源构建SqlSessionFactory，通过他可以构建多个Factory
* 一旦构建factory完成就会废弃，其生命周期只存在于方法的局部

（2）SqlSessionFactory

* 创建session回话，session会话相当于jdbc中的connection对象。每次应用访问数据库的时候，都要通过SqlSessionFactory去创建sqlSession
* 生命周期在整个mybatis应用的生命周期中，由于其责任唯一，其责任就是创建sqlSession，因此采用单例模式。
* 不用单例模式，就会创建很多的factory，factory又会创建很多的session，那么链接很快就会耗尽

（3）SqlSession

* SqlSession。就是一个会话，相当于jdbc的Connection对象，其生命周期是在请求数据库处理事务的过程中。
* 线程不安全的，多线程处理操作需要注意
* 每次创建session都应该及时关闭，否则会造成连接池资源耗尽，连接池资源泄漏

（4）Mapper

* 是一个接口，没有实现类，作用是发送sql，返回需要的结果
* 他在一个sqlSession的事务方法中，是一个方法级别的东西。

###3、相关组件的实现
（1）SqlSessionFactory的实现

（2）SqlSession的运行过程

* 映射器的动态代理
	* Mapper的映射通过动态代理实现
* sqlSession的四个对象
	* Executor，执行器，由他调度StatementHandler、ParameterHandler、ResultHandler来执行相应的sql
	* StatementHandler，作用是使用数据库的statement（preparedStatement）来执行操作
	* ParameterHandler，用于sql对参数的处理
	* ResultHandler，对对吼的数据集进行封装返回处理

###4、mybatis和spring的集成
（1）spring事务

* spring事务是通过aop去实现的

（2）mybatis集成spring

* 配置数据源
	* 配置自己需要的数据源
* 配置SqlSessionFactory
	* 可以使用spring的org.mybatis.spring.SqlSessionFactoryBean
	* setter注入，配置进数据源，别名包，config的位置以及mapper的位置
* 配置SqlSessionTemplate
	* 一个模板类，通过调用sqlSession来完成工作
	* 不建议使用
* 配置mapper
	* mapper只是一个接口，没有具体的实现，具体实现时通过动态代理的形式去生成代理对象去运行。
	* 复杂系统中的dao有很多，可以采用MapperScannerConfigurer来配置属性
		* 配置basePackage，指定扫描包
		* 配置annotationClass，表示类被注解标记以后才能被扫描到
		* 配置sqlSessionFactoryBeanName，指定SqlSessionFactory
* 事务处理
	* 结合以后使用spring的aop事务去处理，包含声明式事务和编程式事务

