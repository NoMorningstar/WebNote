# My Batis 的解析和运行原理

MyBatis的运行过程分为两大步：

* 读取配置文件缓存到Configuration对象，用以创建SqlSessionFactory
* SqlSession的执行过程
  * 包含反射技术和动态代理技术（此为揭示MyBatis底层构架的基础）

### 构建SqlSessionFactory过程

MyBatis通过SqlSessionFactoryBuilder构建SqlSessionFactory分为两步

* 通过org.apache.ibatis.builder.xml.XMLConfiguration解析配置的XML文件，读出所配置的参数，将读取内容存入org.apache.ibatis.session.Configuration类对象中，Configuration采用的是单例模式
* 使用Configuration对象去创建SqlSessionFactory,此为一个接口而不是实现类，所以MyBatis提供一个默认的实现类org.apache.ibatis.session.defaults.DefaultSqlSessionFactory

#### 构建Configuration

Configuration的作用是：

* 读入配置文件，包括基础配置的XML和映射器XML
* 初始化一些基础配置
* 提供单例，为后续创建SessionFactory服务，提供配置的参数
* 执行一些重要对象的初始化方法

#### 构建映射器的内部组成

映射器的内部组成：

* MappedStatement 保存映射器节点的内容、是一个类
* SqlSource是提供BoundSql对象的地方，是MappedStatement的一个属性，是接口而不是实现类，作用是根据上下文和参数解析生成需要的SQL
* BoundSql是一个结果对象，是建立SQL和参数的地方，包含3个常用属性：sql、parameterObject、parameterMappings

**需要关注的是BoundSql对象，通过它便可拿到要执行的SQL和参数，通过SQL和参数即可增强MyBatis的功能**

BuondSql提供3个主要属性

* parameterObject 参数本身，传递简单对象、POJO或者Map、@Param注解的参数
  * 传递简单对象
  * 传递POJO或者Map
  * 传递多个参数
  * 使用@Param注解，MyBatis就会把parameterObject变成Map<String,Object>对象

* parameterMappings是一个List,每一个元素都是ParameterMapping对象，对象会描述参数，一般不去改变它，通过它即可实现参数和SQL的结合
* sql属性是书写在映射器里的一条被SqlSource解析后的SQL，大部分时候无须修改，只有在使用插件时可根据需要进行改写

### SqlSession运行过程

MyBatis只用Mapper接口即可运行

原因：Mapper的XML文件的命名空间对应该接口的全限定名

方法：MyBatis根据全路径和方法名，将其和代理对象绑定起来，通过动态代理技术，使该接口运行起来，而后采用命令模式，最后使用SqlSession接口的方法使得它能够执行对应SQL

#### SqlSession下的四大对象

映射器是动态代理对进入MapperMethod的execute方法，经过简单判断进入SqlSession的delete、update、insert、select等方法

SqlSession 的执行过程是通过Executor 、StatementHandler 、Parame terHandler 和ResultSetHandler 来完成数据库操作和结果返回的，

* Executor（执行器）
  * 是真正执行Java和数据库交互的对象
  * 包含3种执行器
    * SIMPLE 简易执行器，默认使用
    * REUSE 能够执行重用预处理语句的执行器
    * BATCH 执行器重用语句和批量更新，批量专用的执行器
  * 提供查询（query）方法、更新（updete）方法和相关的事务方法

MyBatis将根据配置类型去确定需要创建哪一种Executor

* StatementHandler（数据库会话器）

专门处理数据库会话

* ParameterHandler（参数处理器）

My Batis 是通过ParameterHandler 对预编译语句进行参数设置的，它的作用是完成对预编译参数的设置

* ResultSetHandler（结果处理器）

ResultSetHandler作用是组装结果集返回