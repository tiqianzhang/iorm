# iorm
my orm mybatis initial version
IORM
1 jdbc问题分析及解决思路
JDBC问题总结： 
原始jdbc开发存在的问题如下： 
1、 数据库连接创建、释放频繁造成系统资源浪费，从而影响系统性能。 
2、 Sql语句在代码中硬编码，造成代码不易维护，实际应用中sql变化的可能较⼤，sql变动需要改变java代码。 
3、 使用preparedStatement向占有位符号传参数存在硬编码，因为sql语句的where条件不一定，可能多也可能少，修改sql还要修改代码，系统不易维护。 
4、 对结果集解析存在硬编码(查询列名)，sql变化导致解析代码变化，系统不易维护，如果能将数据库记录封装成pojo

2 问题解决思路 
使用数据库连接池初始化连接资源 
将sql语句抽取到xml配置文件中 
使用反射、内省等底层技术，自动将实体与表进行属性与字段的自动映射

3 自定义框架设计 
使用端： 
提供核心配置文件： 
sqlMapConfig.xml : 存放数据源信息，引入mapper.xml 
Mapper.xml: sql语句的配置文件信息

框架端： 
1.读取配置文件 
读取完成以后以流的形式存在，我们不能将读取到的配置信息以流的形式存放在内存中，不好操作，可 
以创建javaBean来存储 
（1）Configuration : 存放数据库基本信息、Map<唯一标识，Mapper> 唯一标识：namespace + "." + id 
（2）MappedStatement：sql语句、statement类型、输⼊参数java类型、输出参数java类型
2.解析配置文件 
创建SqlSessionFactoryBuilder类： 
方法：sqlSessionFactory build()： 
第一：使用dom4j解析配置⽂件，将解析出来的内容封装到Configuration和MappedStatement中 
第二：创建SqlSessionFactory的实现类DefaultSqlSession 
3.创建SqlSessionFactory： 
方法：openSession() : 获取sqlSession接⼝的实现类实例对象 
4.创建sqlSession接⼝及实现类：主要封装crud法：selectList(String statementId,Object param)：查询所有 
selectOne(String statementId,Object param)：查询单个
Update()
Delete()
getMappper(Class<T> mapperClass) 动态代理获取mapper的实现，加载配置文件sql文件，实现相对应的方法
5.创建Executor接口及实现类：SimpleExecutor
Query(Configuration,MappedStatement,Object ...params) 执行的就是jdbc代码
具体实现：封装JDBC
4 自定义框架设计

