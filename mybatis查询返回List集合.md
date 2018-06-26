##Mybatis查询返回List集合
实体对象如下:

```java
private String vend_id;
private String vend_name;
private String vend_address;
private String vend_city;
private String vend_state;
private String vend_zip;
private String vend_country;
private String id;
private String name;

//getter与setter方法...

```

XML映射文件如下:

```xml
<select id="findVendorAll"  resultType="vendors">
  	select * from Vendors
</select>
```

接口文件方法如下:

```java
//查询所有记录
List<Vendors> findVendorAll();
```

测试文件如下:

```java
try {
	String resource = "mybatis-config.xml";
	InputStream resourceAsStream = Resources.getResourceAsStream(resource);
	SqlSessionFactory build = new SqlSessionFactoryBuilder().build(resourceAsStream,"development2");
	//获取SQLSession
	SqlSession openSession = build.openSession();
	VendorMapper mapper = openSession.getMapper(VendorMapper.class);
	List<Vendors> findVendorAll = mapper.findVendorAll();
	
	System.out.println(findVendorAll);
	
} catch (IOException e) {
	System.out.println("加载配置文件失败");
	e.printStackTrace();
}
```

笔记:
XML中只需`resultType`属性值为实体对象别名或全路径名。
mybatis会通过接口文件的返回值类型来判断返回的是集合还是对象。如果是对象，则按常规查询并返回；如果是List集合，mybatis则会将查询到的多条记录设置进集合中并返回。

