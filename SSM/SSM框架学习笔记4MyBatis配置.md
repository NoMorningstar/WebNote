# MyBatis配置

### 概述

* MyBatis配置项顺序不能颠倒

### properties属性

* 给系统配置运行参数，放在XML文件或properties文件中
* MyBatis提供3种方式使用properties
  * property子元素
  * properties文件（普遍，简单）
  * 程序代码传递

### settings设置

* MyBatis最复杂的配置，深刻影响MyBatis底层运行
* 大部分情况下使用默认值即可运行

### typeAliases别名

* 用别名来代替全限定名
* 分为系统定义别名和自定义别名
* 别名由类TypeAliasRegistry定义
* 使用TypeAliasRegistry 的registerAlias方法即可注册别名
* MyBatis支持扫描包中的类，并将首字母小写作为别名，若出现重名，通过MyBatis提供注解区分

### typeHandler类型转换器

* 分为两类，其作用是承担jdbcType和javaType之间的相互转换
  * jdbcType-定义数据库类型
  * javaType-定义Java类型

* 自定义typeHandler需要实现接口typeHandler，或者BaseTypeHandler

### ObjectFactory （对象工厂）

* 创建结果集时，MyBatis会使用一个对象工厂完成创建这个结果集实例

### environments （运行环境）

* 主要作用是配置数据库信息
* 可配置元素：事务管理器（transactionManager）、数据源（dataSource）

* 事务管理器主要工作是提交、回滚、关闭数据库事务
* 事务管理器可配置成JDBC和MANAGED方式
* 数据源包含3种：UNPOOLED、POOLED、JNDI
  * UNPOOLED采用非数据库池管理方式，每次请求都会打开新的数据库连接
  * POOLED利用“池”的概念，无须再建立和验证数据库连接
  * JNDI主要是为了能在EJB或应用服务器这类容器中使用

### databaseldProvider 数据库厂商标识

* 多数据库SQL需要配置databaseldProvidetype的属性

### 引入映射器的方法

* 引入映射器的方法
  * 用文件路径引入映射器
  * 用包名引入映射器
  * 用类注册引入映射器
  * 用userMapper.xml引入映射器

