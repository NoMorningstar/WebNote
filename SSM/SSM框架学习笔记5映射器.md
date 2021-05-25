# 映射器

* MyBatis的映射器主要是通过XML方式实现
* 自动映射和驼峰映射，能有效减少大量的映射配置，减少工作量
* 配置自动映射的autoMappingBehavior 选项的取值范围是：（通常使用默认即可）
  * NONE 不进行自动映射
  * PARTIAL 默认值，只对没有嵌套结果集进行自动映射
  * FULL 对所有结果集进行自动映射，包括嵌套结果集

* 驼峰命名（mapUnderscoreToCamelCase）设置为true即可
* 自动映射和驼峰映射建立在SQL列名和POJO属性名的映射关系上
* 对于传递多个参数，使用注解传递，提高代码可读性

>public List<Role > findRolesByAnnotation (@Param (” roleName ” ) String rolename ,
>@Param (” note ” ) String note) ;

* 4种传递多个参数的方法
  * 使用m叩传递参数导致了业务可读性的丧失，导致后续扩展和维护的困难，在实际
    的应用中要果断废弃这种方式。
  * 使用＠ Param 注解传递多个参数，受到参数个数C n ）的影响。当n 三三5 时， 这是最
    佳的传参方式，它比用Java Bean 更好，因为它更加直观； 当n> 5 时， 多个参数将
    给调用带来困难，此时不推荐使用它。
  * 当参数个数多于5 个时，建议使用Java Bean 方式
  * 对于使用混合参数的，要明确参数的合理性

* 使用resultMap映射结果集
* MyBatis内置专门处理分页的类--RowBounds,其原理是执行SQL的查询后，按照偏移量和限制条数返回查询结果，对于大量的数据查询，性能并不佳

* sql元素可定义SQL的一部分，方便后面的SQL引用

* MyBatis在存储过程中存在：输入参数（IN），输出参数（OUT），输入输出参数（INOUT）3种类型

* 特殊字符串的替换和处理（#和$）

>select $ {columns｝from t_tablename

### 级联

* MyBatis的级联分为3种，不支持多对多级联 
  * 鉴别器 根据某些条件决定采用具体实现类级联的方案（discriminator元素）
  * 一对一 学生证和学生（association元素）
  * 一对多 班主任和学生（collection元素）

* 为避免N+1问题，浪费数据库资源，MyBatis提供了延迟加载功能
* 在MyBatis 的settings 配置中存在两个元素可以配置级联
  * lazyLoadingEnabled 延迟加载的全局开关
  * aggressiveLazyLoading 启用时，对任意延迟属性的调用会使带有延迟加载属性的对象完整加载，该配置项是层级开关

* 保证lazyLoadingEnabled=true 和aggressiveLazyLoading=false 的前提下，fetchType对相应的级联元素进行具体配置
  * eager 获得当前POJO后立即加载对应的数据
  * lazy 获得当前POJO后延迟加载对应的数据

* 现实中，多对多级联通常拆分成两个一对多级联来处理

### 缓存

* MyBatis分为一级缓存和二级缓存
  * 一级缓存是在SqlSession上的缓存（默认开启）
  * 二级缓存是在SqlSessionFactory上的缓存

* 缓存要明确cache元素的配置项