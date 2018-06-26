## Mybatis配置文件笔记

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
  PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
  "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>

  <properties>
  	//...mybatis的相关properties配置，例如引用外部properties文件
  </properties>

  <settings>
  	//...mybatis相关环境配置,例如是否开启缓存，是否开启驼峰命名方式等
  </settings>

  <typeAliases>
  	//...对象别名
  </typeAliases>
  
  <typeHandlers>
  	//...自定义类型处理器
  </typeHandlers>
  
  <!-- 环境设置，例如数据源等 -->
  <environments default="development">
    <environment id="development">
      <transactionManager type="JDBC"/>
      <!-- 配置数据源 -->
      <dataSource type="POOLED">
        <property name="driver" value="com.mysql.jdbc.Driver"/>
        <property name="url" value="jdbc:mysql://localhost:3306/sqls?characterEncoding=utf8"/>
        <property name="username" value="root"/>
        <property name="password" value="admin"/>
      </dataSource>
    </environment>
  </environments>
  
  <mappers>
    //...配置映射
  </mappers>
  
  <plugins>
  	//...mybatis相关插件
  </plugins>
  
</configuration>
```

总结规律: 凡是`s`结尾的标签节点都是`configuration`标签节点的子标签。

`typeAliases ` 子标签有`typeAlias`。通过`typeAlias`子标签可以给指定的类取别名,例如:

```xml
<typeAliases>
  	<typeAlias type="com.mybatis.bean.Vendor" alias="vendor"/>
</typeAliases>
```

此时`com.mybatis.bean.Vendor`被指定为vendor,在mapper映射文件中就可以用`vendor`替换`com.mybatis.bean.Vendor`。

在`typeAliases`下除了`typeAlias`还有`package`子标签:

```xml
<typeAliases>
	<package name="com.mybatis.bean"/>
</typeAliases>
```

此时mybatis就会通过扫描包路径给指定包路径下及其子包下的类取别名。别名为小写类名或类名首字母大写(mybatis取别名时实际上不分大小写)。

如果包路径下及子包下有相同的类名，如果不手动指定类别名mybatis会报错，此时可以用`@Alias`自定义类别名。如果手动指定了别名，则别名是指定的名称。mapper映射文件中只能使用类全名或指定的别名(别名不区分大小写)。

`建议不采用别名，采用全路径名方便查看xml的时候可以知道对应的实体类。`


`properties`标签中有两个属性:resource、url:

properties属性 | 属性解释
------| ------
resource | 指定类路径下的资源文件(properties文件)
url | 指定绝对路径的资源文件(可以是磁盘路径，也可以是网络路径)

`settings`标签中有通过`setting`子标签设置mybatis运行时行为设置。例如开启驼峰命名规则:

```xml
<settings>
  	<setting name="mapUnderscoreToCamelCase" value="true"/>
</settings>
```

`typeHandlers`标签中用多个`typeHandler`子标签指定多个类型处理器,例如java对象的String类型与数据库varchar类型的映射。

`mappers`标签中用`mapper`标签指定映射文件,或用`package`标签对指定包路径进行扫描映射文件。

例如，如下代码扫描了包路径，并将包路径下的所有mapper映射文件加载值mybatis中:

```xml
<mappers>
    <package name="com.heiketu.mapper"/>
</mappers>
```

`plugins`标签中指定了mybatis使用的插件，mybaits通过动态代理的方式拦截处理方法调用前的一些自定义行为：
`Executor、ParamterHandler、ResultSetHandler、StatementHandler`插件会拦截mybatis中的4个和核心对象，对行为进行相应的处理。

