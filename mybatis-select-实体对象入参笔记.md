##Mybatis实体对象入参查询笔记
实体类对象结构如下:

```java
public class Vendor {

	private String vend_id;
	private String vend_name;
	private String vend_address;
	private String vend_city;
	private String vend_state;
	private String vend_zip;
	private String vend_country;
	private Integer id;
	private String name;
	
	//getter和setter方法....
	
	@Override
	public String toString() {
	//....
	}
	
}
```

XML映射文件

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
  PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
  "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.mybatis.mapper.VendorMapper">  
  <select id="findByVendorEntity" parameterType="vendor" resultType="vendor">
  	select * from Vendors where vend_id = #{id} and vend_name = #{name}
  </select>
</mapper>
```

接口文件

```java
public interface VendorMapper {
	//通过Vendor对象查询
	Vendors findByVendorEntity(Vendor vendor);
}
```

测试文件内容:

```
try {
			String resource = "mybatis-config.xml";
			InputStream resourceAsStream = Resources.getResourceAsStream(resource);
			SqlSessionFactory build = new SqlSessionFactoryBuilder().build(resourceAsStream,"development2");
			//获取SQLSession
			SqlSession openSession = build.openSession();
			
			VendorMapper mapper = openSession.getMapper(VendorMapper.class);
			Vendor vendor = new Vendor();
			vendor.setId("BRE02");
			vendor.setName("Bear Emporium");
			
			Vendor findByVendorEntity = mapper.findByVendorEntity(vendor);
			System.out.println(findByVendorEntity);
		} catch (IOException e) {
			System.out.println("加载配置文件失败");
			e.printStackTrace();
		}
```

笔记:

当对象作为参数传入查询时(不一定指定`parameterType`属性值为实体对象的别名或全路径名,typeHandler貌似会自动识别),SQL查询语句的`#{}`中内容需要与实体类的字段属性一一对应(并非实体类的属性一定是数据库表中的字段,只要填入的值对应即可。mybatis查询条件是看sql语句的where后的查询条件)，如果表达式中的值没有对应，则会报错。
错误示例如下:

`....Cause:org.apache.ibatis.reflection.ReflectionException: There is no getter for property named 'ids' in 'class com.mybatis.beans.Vendors'
....Cause:org.apache.ibatis.reflection.ReflectionException: There is no getter for property named 'ids' in 'class com.mybatis.beans.Vendors'...`