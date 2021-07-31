架构图

![架构图](https://img-blog.csdnimg.cn/20190316203343447.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzE2MTgzNzMx,size_16,color_FFFFFF,t_70)

- ### 接口层

  - session

    - SqlSessionFactoryBuilder ：多种方式构建DefaultSqlSessionFactory
    - SqlSessionFactory
      - DefaultSqlSessionFactory：只需要一个Configuration就能完成sqlsession工厂的搭建
    - SqlSession：sqlSession的构建就需要核心部件，executor，事物，和配置
      - DefaultSqlSession：通过executor实现各种执行方法
    - SqlSessionManager：实现了SqlSession与SqlSessionFactory，提供多线程管理

    ![img](E:\document\mybatis\assets\202018597153868.png)

  - builder：解析配置文件并构建Configuration

    - XmlConfigBuilder：解析xml配置文件，返回Configuration全局配置类
    - XMLMapperBuilder：解析xml映射文件，并与mapper接口绑定，解析mapper接口
      - <resultMap>:ResultMap对象，ResultMapping

    

- ### 核心层（数据处理）

  - executor：执行器包
    - StatementHandler：获取statement，配置入参，执行，结果集处理
      - RoutingStatementHandler：策略模式，判断到底用哪个BaseStatementHandler
      - BaseStatementHandler：
        - SimpleStatementHandler：使用Statement实现
        - PreparedStatementHandler：使用preparedStatement实现
        - CallableStatement 
    - ParameterHandler ：
      - DefaultParameterHandler
    - ResultSetHandler
      - DefaultResultSetHandler 
    - Executor：
      - BaseExecutor：抽象类实现了 一级缓存的使用
        - SimpleExecutor：简单实现执行流程
        - ReuseExecutor：可复用执行器，缓存statement
        - BatchExecutor：
      - CacheExecutor：实现二级缓存，先会查找MappedStatement中的二级缓存
  - scripting：脚本
  - plugin：插件

- ### 基础层

  - reflection：反射工具包

    - Reflector：反射器类：通过JDK反射解析出属性与方法实现，封装了javabean的所有属性集与方法集

    - ObjectFactory：通过动态的类与构建方法与参数动态实例化对象

  - type：类处理器，jdbc与java之间类转换

    - JdbcType：包装了jdbc类型

    - TypeHandler：setPamater：java类型到jdbc类型的转换、getResult：jdbc类型到java类型的转换

    - TypeHandlerRegistry：管理已知的typehandler，mybatis会注册并维护一个 所有java类转jdbc类与对应typehandler的容器用到时拿取，我们可以自定义typehandler，并从配置文件中加入

  - datasource：数据源 unpooled、pooled、jndi    一般使用第三方数据源

  - trasaction：事务  一般使用spring事务

  - binding：代理对象绑定

    - 提供了一个mapper映射层，解决了以前指定sqlid执行时，无法做的关联检验
    - Mapper：映射器
    - MapperRegistry ：代理对象工厂与mapper接口的注册中心
    - MapperProxyFactory：代理对象工厂 jdk动态代理mapper接口，根据实现invokeHandler接口的MapperProxy的执行策略
    - MapperProxy：
      - MapperMethod：
        - SqlCommand：
        - MethodSignature：

  - cache：mybatis一二级缓存的实现类

    - PerpetualCache：基本实现类，使用hashMap实现缓存
    - BlockingCache：保证一个线程
    - CacheKey：缓存项的key，确定一个缓存是多维的

  - parsing：解析xml的基础类，XNode等

  - mapper：Configuration关联的映射类、ParameterMap、ResultMap就在该包下

  - IO：通过classloader加载类包

  - log：日志

  - exception：异常

- jdbc单元测试

- annotations注解