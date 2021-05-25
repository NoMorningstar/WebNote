# 认识MyBatis核心组件

### 持久层概念和MyBatis特点

* 持久层可以将业务数据存储到磁盘，具备长期存储能力，缺点是比较慢
* Java互联网应用可以通过MyBatis框架访问数据库
* MyBatis成功的3点
  * 不屏蔽SQL，可对其优化改造，提高系统性能
  * 提供强大、灵活的映射机制，提供动态SQL功能
  * 提供使用Mapper的接口编程，只要一个接口和一个XML就能创建映射器，简化工作

### MyBatis的核心组件

* SqlSessionFactoryBuilder(构造器)

  根据配置或代码生成SqlSessionFactory，采用的是分步构建的Builder模式

* SqlSessionFactory（工厂接口）

  依靠它来生成SqlSession，使用的是工厂模式

* SqlSession（会话）

  既可发送SQL执行返回结果，也可获取Mapper的接口

* SQL Mapper

  由一个Java接口和XML文件构成，需要给出对应的SQL和映射规则，负责发送SQL去执行，并返回结果

### SqlSessionFactory（工厂接口）

* 使用MyBatis首先是使用配置或代码生产SqlSessionFactory

  * 通过读取配置的XML文件的形式生成SqlSessionFactory**（推荐）**

  * 通过Java代码形式生成SqlSessionFactory

* 通过Configuration类对象构建整个MyBatis的上下文
* 在MyBatis中存在两个实现类：
  * SqlSessionManager（多线程环境）
  * DefaultSqlSessionFactory（具体实现）

* SqlSessionFactory的唯一作用是生产MyBatis的核心接口对象SqlSession

#### 使用XML构建SqlSessionFactory

* XML分为两类
  * 基础配置文件，通常只有1个，主要配置最基本的上下文参数和运行环境
  * 映射文件，配置映射关系、SQL、参数等信息

* MyBatis基础配置文件
  * <typeAlias>元素定义一个别名，在MyBatis上下文中使用别名代替全限定名
  * <environment>元素，其中<transactionManager>元素是配置事务管理器，<dataSource>元素配置数据库
  * <mapper>元素代表引入的映射器

### SqlSession

* 在MyBatis中，SqlSession是其核心接口，其作用类似于JDBC的Connection对象

  代表一个连接资源的启用

* 其具体作用有3个
  * 获取Mapper接口
  * 发送SQL给数据库
  * 控制数据库事务

### 映射器

* 映射器是MyBatis中最重要、最复杂的组件，由1个接口和对应的XML文件（或注解）组成
* 可配置如下内容
  * 描述映射guiz
  * 提供SQL语句，配置SQL参数类型、返回类型、缓存刷新等信息
  * 配置缓存
  * 提供动态SQL

* 实现映射器的两种方式
  * XML文件形式
  * 注解形式

* 主要作用是：将SQL查询到的结果映射为1个POJO，或将POJO数据插入到数据库中，并定义关于缓存等重要内容
* MyBatis在默认情况下提供自动映射
* 注解方式定义映射器，只需要一个接口即可通过MyBatis的注解注入SQL
* 注解方式和XML方式同时定义，XML方式会覆盖掉注解方式，推荐XML方式
* 使用SqlSession或Mapper（**推荐使用，当前主流**）发送SQL

### 生命周期

* SqlSessionFactoryBuilder只存在于创建SqlSessionFactory中，创建成功后，失去作用
* SqlSessionFactory存在于整个MyBatis应用中，尽量使用单例
* SqlSession存在于一个业务申请中，处理完整个请求后，关闭该连接，归还给SqlSessionFactory
* Mapper由SqlSession创建，生命周期小于等于SqlSession的生命周期，随着SqlSession的关闭而消失