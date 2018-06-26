##Mybatis使用笔记
如果是maven管理项目，那么可以直接到maven的中央仓库中获取[**mybatis**](http://mvnrepository.com/artifact/org.mybatis/mybatis)的jar包的坐标

在maven的[pom.xml](#)文件中的`dependencies`标签中填入如下坐标:

```
<dependency>
	<groupId>org.mybatis</groupId>
	<artifactId>mybatis</artifactId>
	<version>3.4.6</version>
</dependency>
```

每一个[**mybatis**](#)应用都以`sqlSessionFactory`实例为核心。

**mybatis**通过`sqlSessionFactoryBuilder`创建`sqlSessionFactory` 。

再通过sqlSessionFacotry创建连接会话`sqlSession`。

#####创建代码如下

```
String resource = "mybatis-config.xml";
InputStream resourceAsStream = Resources.getResourceAsStream(resource);
SqlSessionFactory build = new SqlSessionFactoryBuilder().build(resourceAsStream);
```
**mybatis** 的sqlSessionFactory配置文件如下

```
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
  PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
  "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
  <environments default="development">
    <environment id="development">
      <transactionManager type="JDBC"/>
      <dataSource type="POOLED">
        <property name="driver" value="com.mysql.jdbc.Driver"/>
        <property name="url" value="jdbc:mysql://localhost:3306/sqls?characterEncoding=utf8"/>
        <property name="username" value="root"/>
        <property name="password" value="admin"/>
      </dataSource>
    </environment>
  </environments>
  <mappers>
    	//映射文件位置
  </mappers>
</configuration>
```
配置文件中内容解释如下

    标签名     |    标签解释
------------- | -------------
configuration | 配置文件的根标签
environments  | 对应多个数据库环境
environment   | 每一个对应一个数据库环境
transactionManager | 数据库事物类型
dataSource	| 对应一个数据源
property	| 数据源的属性

也可以采用java代码的方式来创建sqlSessionFactory [**不建议**](#)

```
DataSource dataSource = BlogDataSourceFactory.getBlogDataSource();
TransactionFactory transactionFactory = new JdbcTransactionFactory();
Environment environment = new Environment("development", transactionFactory, dataSource);
Configuration configuration = new Configuration(environment);
configuration.addMapper(BlogMapper.class);
SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(configuration);
```

mybatis中的mapper映射文件内容如下

```
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
  PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
  "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.mybatis.mapper.BlogMapper">
  <select id="selectBlog" resultType="com.mybatis.beans.Orders">
    select * from Orders where order_num = #{order_num}
  </select>
</mapper>
```

#####其结构解读如下

标签名| 标签解释
-----| ------
mapper| 映射文件的根标签
select | 对应sql查询中的select语句
> mapper 中的namespace对应mapper的接口文件全路径名，mybatis通过动态代理获取namespace属性的值然后动态获取select中的id属性值从而拼接并调用接口方法

> select中的id属性对应接口文件中的方法签名

> resultType属性对应返回值类型，此处因为没有规定别名，所以需要指定类全限定名

--
从**SQLSessionFactory**中获取**SQLSession**代码如下

```
SqlSession session = sqlSessionFactory.openSession();
try {
  BlogMapper mapper = session.getMapper(BlogMapper.class);
  Blog blog = mapper.selectBlog(101);
} finally {
  session.close();
}
```

--
###Mybatis配置文件各属性讲解
* [properties 属性](#properties)
* [settings 设置](#settings)
* [typeAliases 设置](#typeAliases)
* [typeHandlers 设置](#typeHandlers)

####<a name="properties"></a>properties
property属性都可以从外部配置且动态替换的。
也可以通过`properties`文件中的`key-value`来传递

`properties`标签放在`configuration`标签节点下

```
<properties resource="org/mybatis/example/config.properties">
<!--这里面也可以添加对应的property标签-->
<property name="属性名" value="值"/>
...
</properties>
```

> **resource** 属性指定properties文件所存放的路径,可以是相对路径也可以是绝对路径。

然后通过`${..}`语法从properties文件中获取对应的`key-value`值:

```
<property name="driver" value="${mybatis.driver}"/>
<property name="url" value="${mybatis.url}"/>
<property name="username" value="${mybatis.username}"/>
<property name="password" value="${mybatis.password}"/>
```

如果属性在不只一个地方进行了配置，那么 MyBatis 将按照下面的顺序来加载：

* 在 properties 元素体内指定的属性首先被读取。
* 然后根据 properties 元素中的 resource 属性读取类路径下属性文件或根据 url 属性指定的路径读取属性文件，并覆盖已读取的同名属性。
* 最后读取作为方法参数传递的属性，并覆盖已读取的同名属性。

**因此，通过方法参数传递的属性具有最高优先级，resource/url 属性中指定的配置文件次之，最低优先级的是 properties 属性中指定的属性。**

从`MyBatis 3.4.2`开始，你可以为占位符指定一个默认值，但是需要开启这个属性(默认是关闭的):
`org.apache.ibatis.parsing.PropertyParser.enable-default-value`

使用默认站位符如下

```
<configuration>
	<properties resource="dataSource.properties">
		<property name="org.apache.ibatis.parsing.PropertyParser.enable-default-value" value="true" />
	</properties>
	....
	<environments default="development">
		<environment id="development">
			...
			<dataSource type="POOLED">
				...				
				<!--value属性中":admin"就是当properties文件中未定义mybatis.password这个key时的默认填充值，其中":"符号可以采用OGNL三元运算符 '?:'-->
				<property name="password" value="${mybatis.password:admin}" />
			</dataSource>
		</environment>
	</environments>
</configuration>
```

####<a name="settings"></a>settings

settings相关属性如下表

设置参数 | 描述 | 有效值 | 默认值
-------|------|-------|-------:
cacheEnabled|全局地开启或关闭配置文件中的所有映射器已经配置的任何缓存。|true  false |true
lazyLoadingEnabled|	延迟加载的全局开关。当开启时，所有关联对象都会延迟加载。 特定关联关系中可通过设置<mark>fetchType</mark>属性来覆盖该项的开关状态。|true  false|	false
aggressiveLazyLoading|当开启时，任何方法的调用都会加载该对象的所有属性。否则，每个属性会按需加载（参考<mark>lazyLoadTriggerMethods</mark>).|true  false|false (在版本**3.4.1**及以下时为true)
multipleResultSetsEnabled|是否允许单一语句返回多结果集（需要兼容驱动）。|true  false| true
useColumnLabel|	使用列标签代替列名。不同的驱动在这方面会有不同的表现， 具体可参考相关驱动文档或通过测试这两种不同的模式来观察所用驱动的结果。|true false | true
useGeneratedKeys|允许 JDBC 支持自动生成主键，需要驱动兼容。 如果设置为 true 则这个设置强制使用自动生成主键，尽管一些驱动不能兼容但仍可正常工作（比如 Derby）。|true false | false
autoMappingBehavior|指定 MyBatis 应如何自动映射列到字段或属性。 NONE 表示取消自动映射；PARTIAL 只会自动映射没有定义嵌套结果集映射的结果集。 FULL 会自动映射任意复杂的结果集（无论是否嵌套）。|NONE, PARTIAL, FULL| PARTIAL
autoMappingUnknownColumnBehavior|指定发现自动映射目标未知列（或者未知属性类型）的行为。 * NONE: 不做任何反应  * WARNING: 输出提醒日志 ('org.apache.ibatis.session.AutoMappingUnknownColumnBehavior' 的日志等级必须设置为 WARN) * FAILING: 映射失败 (抛出 SqlSessionException)| NONE, WARNING, FAILING | NONE
defaultExecutorType | 配置默认的执行器。SIMPLE 就是普通的执行器；REUSE 执行器会重用预处理语句（prepared statements）； BATCH 执行器将重用语句并执行批量更新。 | SIMPLE REUSE BATCH | SIMPLE 
mapUnderscoreToCamelCase | 是否开启自动驼峰命名规则（camel case）映射，即从经典数据库列名 A_COLUMN 到经典 Java 属性名 aColumn 的类似映射。 | true \| false|false
更多配置需要参考[Mybatis](#http://www.mybatis.org/mybatis-3/zh/configuration.html#properties)官网

######一个配置完整的 settings 元素的示例如下

```
<settings>
  <setting name="cacheEnabled" value="true"/>
  <setting name="lazyLoadingEnabled" value="true"/>
  <setting name="multipleResultSetsEnabled" value="true"/>
  <setting name="useColumnLabel" value="true"/>
  <setting name="useGeneratedKeys" value="false"/>
  <setting name="autoMappingBehavior" value="PARTIAL"/>
  <setting name="autoMappingUnknownColumnBehavior" value="WARNING"/>
  <setting name="defaultExecutorType" value="SIMPLE"/>
  <setting name="defaultStatementTimeout" value="25"/>
  <setting name="defaultFetchSize" value="100"/>
  <setting name="safeRowBoundsEnabled" value="false"/>
  <setting name="mapUnderscoreToCamelCase" value="false"/>
  <setting name="localCacheScope" value="SESSION"/>
  <setting name="jdbcTypeForNull" value="OTHER"/>
  <setting name="lazyLoadTriggerMethods" value="equals,clone,hashCode,toString"/>
</settings>
```

####<a name="typeAliases"></a>typeAliases

类型别名是为 Java 类型设置一个短的名字，存在的意义仅在于用来减少类完全限定名的冗余。例如

```
<typeAliases>
  <typeAlias alias="Author" type="domain.blog.Author"/>
  <typeAlias alias="Blog" type="domain.blog.Blog"/>
  <typeAlias alias="Comment" type="domain.blog.Comment"/>
  <typeAlias alias="Post" type="domain.blog.Post"/>
  <typeAlias alias="Section" type="domain.blog.Section"/>
  <typeAlias alias="Tag" type="domain.blog.Tag"/>
</typeAliases>
```

也可以指定一个包名,这样mybatis会搜索包路劲下所有的Java Bean。并将类名作为alias属性值，全限定吗作为type属性值。此时类名就可以直接用在mapper映射文件中

```
<typeAliases>
  <package name="包路径"/>
</typeAliases>
```
每一个在包路径中的 Java Bean，在没有注解的情况下，会使用 Bean 的<mark>首字母小写</mark>的非限定类名来作为它的别名。
比如 `domain.blog.Author` 的别名为 `author`
若有注解，则别名为其注解值，例如

```
@Alias("author")
public class Author {
    ...
}
```

####<a name="typeHandlers"></a>typeHandlers

无论是 MyBatis 在预处理语句（PreparedStatement）中设置一个参数时，还是从结果集中取出一个值时， 都会用类型处理器将获取的值以合适的方式转换成 Java 类型。

**从 3.4.5 开始，MyBatis 默认支持 JSR-310(日期和时间 API)**

类型处理太多，规整查看至[**mybatis**](#http://www.mybatis.org/mybatis-3/zh/configuration.html#properties)官网


可以重写类型处理器或创建你自己的类型处理器来处理不支持的或非标准的类型。 具体做法为：实现 `org.apache.ibatis.type.TypeHandler` 接口， 或继承一个很便利的类 `org.apache.ibatis.type.BaseTypeHandler`


##映射器
mybatis通过映射文件来获取SQL语句，所以需要告诉mybatis映射文件在哪里取。共4种方式可供选择:

* 使用相对于类路径的资源引用

	```
	<mappers>
	  <mapper resource="org/mybatis/builder/AuthorMapper.xml"/>
	  <mapper resource="org/mybatis/builder/BlogMapper.xml"/>
	  <mapper resource="org/mybatis/builder/PostMapper.xml"/>
	</mappers>
	```
* 使用完全限定资源定位符（URL）

	```
	<mappers>
	  <mapper url="file:///var/mappers/AuthorMapper.xml"/>
	  <mapper url="file:///var/mappers/BlogMapper.xml"/>
	  <mapper url="file:///var/mappers/PostMapper.xml"/>
	</mappers>
	```

* 使用映射器接口实现类的完全限定类名



	```
	<!--此处类为Mapper的接口类，所以需要将接口和XML文件放置在同一包路径下-->
	<mappers>
	  <mapper class="org.mybatis.builder.AuthorMapper"/>
	  <mapper class="org.mybatis.builder.BlogMapper"/>
	  <mapper class="org.mybatis.builder.PostMapper"/>
	</mappers>
	```


* 将包内的映射器接口实现全部注册为映射器[包扫描的方式]

	```
	<!--此处包为Mapper的包路径，所以需要将接口和XML文件放置在同一包路径下-->
	<mappers>
	  <package name="org.mybatis.builder"/>
	</mappers>
	```


`:gitHub测试部分`
