## Mybatis Generator使用测试

项目结构:

<img src="/Users/zhengjie/Documents/文档笔记/java/mybatis/img/1.png" alt="项目目录结构" width="30%"/>


`Mybatis Generator配置文件内容:`

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE generatorConfiguration
  PUBLIC "-//mybatis.org//DTD MyBatis Generator Configuration 1.0//EN"
  "http://mybatis.org/dtd/mybatis-generator-config_1_0.dtd">

<generatorConfiguration>
  <!--targetRuntime指定生成文件的策略-->
  <context id="DB2Tables" targetRuntime="MyBatis3Simple">
  
    <!--JDBC驱动相关配置-->
    <jdbcConnection driverClass="com.mysql.jdbc.Driver"
        connectionURL="jdbc:mysql://127.0.0.1:3306/sqls?useUnicode=true&amp;characterEncoding=utf-8"
        userId="root"
        password="admin">
    </jdbcConnection>

	<!--java类型解析器设置,默认设置forceBigDecimals属性-->
    <javaTypeResolver >
      <property name="forceBigDecimals" value="false" />
    </javaTypeResolver>

	<!--生成java实体类策略
	    targetPackage属性指定生成实体类存放包路径
	    targetProject属性指定相对于哪个存放的文件
	-->
    <javaModelGenerator targetPackage="com.heiketu.domain" targetProject="./src">
      <property name="enableSubPackages" value="true" />
      <property name="trimStrings" value="true" />
    </javaModelGenerator>

	<!--生成java映射接口策略
	    targetPackage属性指定生成映射接口存放包路径
	    targetProject属性指定相对于哪个存放的文件
	-->
    <sqlMapGenerator targetPackage="com.heiketu.mapper"  targetProject="./src">
      <property name="enableSubPackages" value="true" />
    </sqlMapGenerator>
    
    
	<!--生成java映射XML文件策略
	    targetPackage属性指定生成映射XML存放包路径
	    targetProject属性指定相对于哪个存放的文件
	-->
    <javaClientGenerator type="XMLMAPPER" targetPackage="com.heiketu.mapper"  targetProject="./config">
      <property name="enableSubPackages" value="true" />
    </javaClientGenerator>

	<!--设置对应的数据库表
		tableName属性对应表明
		domainObjectName属性指定对应实体类
	-->
    <table tableName="usrs" domainObjectName="Usrs"></table>

  </context>
</generatorConfiguration>
```

`相关java代码:`

```java
List<String> warnings = new ArrayList<String>();
boolean overwrite = true;

//指定genrator的XML配置文件
File configFile = new File("mbg.xml");
ConfigurationParser cp = new ConfigurationParser(warnings);
Configuration config = cp.parseConfiguration(configFile);
DefaultShellCallback callback = new DefaultShellCallback(overwrite);
MyBatisGenerator myBatisGenerator = new MyBatisGenerator(config, callback, warnings);
myBatisGenerator.generate(null);
```