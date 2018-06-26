##Mybatis映射文件之简单查询select记录


创建数据库和数据库表,SQL如下:

```sql
CREATE TABLE `Vendors` (
  `vend_id` char(10) COLLATE utf8_unicode_ci NOT NULL,
  `vend_name` char(50) COLLATE utf8_unicode_ci NOT NULL,
  `vend_address` char(50) COLLATE utf8_unicode_ci DEFAULT NULL,
  `vend_city` char(50) COLLATE utf8_unicode_ci DEFAULT NULL,
  `vend_state` char(5) COLLATE utf8_unicode_ci DEFAULT NULL,
  `vend_zip` char(10) COLLATE utf8_unicode_ci DEFAULT NULL,
  `vend_country` char(50) COLLATE utf8_unicode_ci DEFAULT NULL,
  PRIMARY KEY (`vend_id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8 COLLATE=utf8_unicode_ci

```

项目采用`maven`来管理依赖jar包。
在pom.xml中加入如下依赖:

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
	<modelVersion>4.0.0</modelVersion>
	<groupId>com.mybatis</groupId>
	<artifactId>mybatis</artifactId>
	<version>0.0.1-SNAPSHOT</version>

	<dependencies>
		<!--Mybatis核心包,此处版本为3.4.6-->
		<dependency>
			<groupId>org.mybatis</groupId>
			<artifactId>mybatis</artifactId>
			<version>3.4.6</version>
		</dependency>
		<!--连接数据库时需要的数据库驱动，此处为mysql驱动-->
		<dependency>
			<groupId>mysql</groupId>
			<artifactId>mysql-connector-java</artifactId>
			<version>5.1.45</version>
		</dependency>
	</dependencies>

	<build>
		<plugins>
			<!--默认maven编译项目时使用的是JDK1.5，这里修改编译环境的JDK版本为1.8,并且maven下载JDK源码版本也指定为1.8-->
			<plugin>
				<groupId>org.apache.maven.plugins</groupId>
				<artifactId>maven-compiler-plugin</artifactId>
				<version>3.7.0</version>
				<configuration>
					<target>1.8</target>
					<source>1.8</source>
				</configuration>
			</plugin>
		</plugins>
	</build>
</project>
```
将mybatis的配置文件放置在`src/main/resources`目录中,命名为`mybatis-config.xml`,其内容如下:

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
  PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
  "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
	<!--加载外部配置文件-->
	<properties resource="dataSource.properties">
	</properties>
	<!--给实体类取别名，这里通过扫描包路径-->
	<typeAliases>
		<package name="com.mybatis.beans"/>
	</typeAliases>
	<!--
		mybatis连接数据库相关配置
		default属性指定了默认的数据库环境,其值与子标签environment的id对应
	-->
	<environments default="development">
		<environment id="development">
			<!---指定事物管理类型,mybatis的事务管理可以指定几种,此时本人还未学习到这一步配置->
			<transactionManager type="JDBC" />
			<!--
				加载数据源并指定类型,POOLED代表数据库连接是从数据源池中获取,内部的属性配置从之前引用的外部配置文件中获取，通过"${}"表达引入
			-->
			<dataSource type="POOLED">
				<property name="driver" value="${mybatis.driver}" />
				<property name="url" value="${mybatis.url}" />
				<property name="username" value="${mybatis.username}" />
				<property name="password"
					value="${mybatis.password}" />
			</dataSource>
		</environment>
		</environments>
	<!--加载mybatis的映射文件,mybaits加载映射文件有好几种方式，这里采用了包扫描方式,扫描:com.mybatis.mapper路径下的所用映射文件及对应接口(因为是包扫描方式，所用映射文件和对应接口文件命名需要一致)-->
	<mappers>
		<package name="com.mybatis.mapper"/>
	</mappers>
</configuration>
```

外部properties文件的内容如下:

```properties
# 数据库驱动,这里指定了mysql
mybatis.driver=com.mysql.jdbc.Driver
# 指定数据库
mybatis.url=jdbc:mysql://localhost:3306/sqls?
characterEncoding=utf8
# 数据库账户名
mybatis.username=root
# 数据库连接密码
mybatis.password=admin
```
然后创建实体类Vendor,实体类代码如下:

```java
public class Vendor {
	private String vend_id;
	private String vend_name;
	private String vend_address;
	private String vend_city;
	private String vend_state;
	private String vend_zip;
	private String vend_country;
	public String getVend_id() {
		return vend_id;
	}
	public void setVend_id(String vend_id) {
		this.vend_id = vend_id;
	}
	public String getVend_name() {
		return vend_name;
	}
	public void setVend_name(String vend_name) {
		this.vend_name = vend_name;
	}
	public String getVend_address() {
		return vend_address;
	}
	public void setVend_address(String vend_address) {
		this.vend_address = vend_address;
	}
	public String getVend_city() {
		return vend_city;
	}
	public void setVend_city(String vend_city) {
		this.vend_city = vend_city;
	}
	public String getVend_state() {
		return vend_state;
	}
	public void setVend_state(String vend_state) {
		this.vend_state = vend_state;
	}
	public String getVend_zip() {
		return vend_zip;
	}
	public void setVend_zip(String vend_zip) {
		this.vend_zip = vend_zip;
	}
	public String getVend_country() {
		return vend_country;
	}
	public void setVend_country(String vend_country) {
		this.vend_country = vend_country;
	}
	@Override
	public String toString() {
		return "Vendors [vend_id=" + vend_id + ", vend_name=" + vend_name + ", vend_address=" + vend_address
				+ ", vend_city=" + vend_city + ", vend_state=" + vend_state + ", vend_zip=" + vend_zip
				+ ", vend_country=" + vend_country + "]";
	}
	
}
```

xml映射文件内容如下:

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
  PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
  "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
  <!--通过包扫描方式加载映射文件需要告诉mybaits对应的接口在哪儿,所以在namespace属性指定接口全路径名来告诉mybaits接口所在位置-->
<mapper namespace="com.mybatis.mapper.VendorMapper">
<!--
	select标签代表查询SQL,id对应接口的同名抽象方法
	resultType属性指定返回值得实体类型，因为此处返回的是一个Vendor对象，之前在mybatis配置文件中已经指定了实体类的别名，所以此处可以不需要写实体类的全路径名
-->
  <select id="findVendorByVendorId" resultType="vendor">
    select * from Vendors where vend_id = #{vend_id} and vend_name = #{vend_name}
  </select>
</mapper>
```

xml对应的接口文件内容如下:

```java
public interface VendorMapper {
	Vendors findVendorByVendorId(@Param("vend_id") String vend_id,@Param("vend_name")String vend_name);
}

```

测试方法代码如下:

```java
try {
			String resource = "mybatis-config.xml";
			InputStream resourceAsStream = Resources.getResourceAsStream(resource);
			SqlSessionFactory build = new SqlSessionFactoryBuilder().build(resourceAsStream,"development2");
			//获取SQLSession
			SqlSession openSession = build.openSession();
			
			//获取映射接口
			VendorMapper mapper = openSession.getMapper(VendorMapper.class);
			Vendors findVendorByVendorId = mapper.findVendorByVendorId("BRE02","Bear Emporium");
			System.out.println(findVendorByVendorId);
		} catch (IOException e) {
			System.out.println("加载配置文件失败");
			e.printStackTrace();
		}
```

运行结果:

```console
Mon Jun 04 16:42:41 CST 2018 WARN: Establishing SSL connection without server's identity verification is not recommended. According to MySQL 5.5.45+, 5.6.26+ and 5.7.6+ requirements SSL connection must be established by default if explicit option isn't set. For compliance with existing applications not using SSL the verifyServerCertificate property is set to 'false'. You need either to explicitly disable SSL by setting useSSL=false, or set useSSL=true and provide truststore for server certificate verification.
Vendors [vend_id=BRE02, vend_name=Bear Emporium, vend_address=500 Park Street, vend_city=Anytown, vend_state=OH, vend_zip=44333, vend_country=USA]
```

`重点: 当传入单个参数，sql语句中#{}表达式内可以是任意有效字符串。当传入的参数为多个时，如果不指定对应参数的参数名时，则#{}表达式中值为:"arg0"、"arg1"...或"param1","param2"....以此类推。"argX"从索引0开始，"paramX"则是从索引1开始。`

当接口文件中指定了入参参数名时，则#{}表达式中可以使用入参参数名了。
接口文件通过`@Param`注解来标注入参参数名,参考格式如下:

```java
	Vendors findVendorByVendorId(@Param("vend_id") String vend_id,@Param("vend_name")String vend_name);
```

通过注解标注了入参参数名后，则#{}表达式中就可以使用参数名了:

```xml
<select id="findVendorByVendorId" resultType="vendor">
    select * from Vendors where vend_id = #{vend_id} and vend_name = #{vend_name}
  </select>
```