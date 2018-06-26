## mybatis: 简单增删改

Mapper映射文件:

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
  PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
  "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.mybatis.mapper.VendorMapper">
  
  <!-- 查询
  resultType : 指定查询出来的类型
  databaseId : 配合databaseIdProvider动态选择数据库SQL语句
   -->
  <select id="getVendorById" resultType="com.mybatis.bean.Vendor" databaseId="mysql">
    select * from Vendors where vend_id = #{id}
  </select>
  
  <!-- 增加: 
  	parameterType: 传入的参数类型(可以省略，mybatis会自动映射)
  -->
  <insert id="insertVendor">
  	INSERT INTO Vendors VALUES(#{vendId},#{vendName},#{vendAddress},#{vendCity},#{vendState},#{vendZip},#{vendCountry})
  </insert>
  
  <!-- 修改: 
  	parameterType: 传入的参数类型(可以省略，mybatis会自动映射)
  -->
  <update id="updateById">
  	UPDATE Vendors SET vend_name = #{vendName} WHERE vend_id = #{vendId}
  </update>
  
  <!-- 删除:
  parameterType: 传入的参数类型(可以省略，mybatis会自动映射)
   -->
   <delete id="deleteById">
   	DELETE FROM Vendors WHERE vend_id = #{vendId}
   </delete>
  
</mapper>
```

接口文件:

```java
	/*
	 * Mybatis中Insert、update、delete操作可以有返回值,返回值类型可以有以下几种:
	 * Boolean(boolean) | Integer(int) | Long(long) | void
	 * 前三种类型(包装类型或普通数据类型)
	 * 如果是Integer 或 Long 类型，则返回的是影响行数，如果是boolean类型，mybatis会将数值类型转换成boolean类型返回(
	 * 影响行数>0为true,影响行数=0为false)
	 * */
	//查询
	public Vendor getVendorById(String vendId);
	//增加
	public Boolean insertVendor(Vendor vendor);
	//更新
	public Boolean updateById(Vendor vendor);
	//删除
	public Boolean deleteById(Vendor vendor);
```


实体类:

```java

//属性
	private String vendId;
	private String vendName;
	private String vendAddress;
	private String vendCity;
	private String vendState;
	private String vendZip;
	private String vendCountry;
	
	//无参构造器
	public Vendor() {
		super();
	}
	
	//带参构造器
	public Vendor(String vendId, String vendName, String vendAddress, String vendCity, String vendState, String vendZip,
			String vendCountry) {
		super();
		this.vendId = vendId;
		this.vendName = vendName;
		this.vendAddress = vendAddress;
		this.vendCity = vendCity;
		this.vendState = vendState;
		this.vendZip = vendZip;
		this.vendCountry = vendCountry;
	}
	
	//getter、setter方法
	public String getVendId() {
		return vendId;
	}
	public void setVendId(String vendId) {
		this.vendId = vendId;
	}
	public String getVendName() {
		return vendName;
	}
	public void setVendName(String vendName) {
		this.vendName = vendName;
	}
	public String getVendAddress() {
		return vendAddress;
	}
	public void setVendAddress(String vendAddress) {
		this.vendAddress = vendAddress;
	}
	public String getVendCity() {
		return vendCity;
	}
	public void setVendCity(String vendCity) {
		this.vendCity = vendCity;
	}
	public String getVendState() {
		return vendState;
	}
	public void setVendState(String vendState) {
		this.vendState = vendState;
	}
	public String getVendZip() {
		return vendZip;
	}
	public void setVendZip(String vendZip) {
		this.vendZip = vendZip;
	}
	public String getVendCountry() {
		return vendCountry;
	}
	public void setVendCountry(String vendCountry) {
		this.vendCountry = vendCountry;
	}
	
	//覆盖toString方法
	@Override
	public String toString() {
		return "Vendor [vendId=" + vendId + ", vendName=" + vendName + ", vendAddress=" + vendAddress + ", vendCity="
				+ vendCity + ", vendState=" + vendState + ", vendZip=" + vendZip + ", vendCountry=" + vendCountry + "]";
	}
```

测试文件:

```java
InputStream inputStream = null;
		SqlSessionFactory sqlSessionFactory = null;
		SqlSession session = null;
		try {
			inputStream = Resources.getResourceAsStream("mybatis-config.xml");
			sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
			session = sqlSessionFactory.openSession();
			VendorMapper mapper = session.getMapper(VendorMapper.class);
			/*Vendor vendorById = mapper.getVendorById("BRE02"); */
			
			/*mapper.insertVendor(new Vendor("BER10", "PEOPLE", "CHINA", "GUANGZHOU", "20O", "ABC", "GUANGZ"));*/
			Vendor v = new Vendor();
			v.setVendId("BER10");
			
			/*v.setVendName("我类个擦");
			Boolean b = mapper.updateById(v);
			System.out.println(b);*/
			
			Boolean deleteById = mapper.deleteById(v);
			System.out.println(deleteById);
			
			session.commit();
		} catch (IOException e) {
			e.printStackTrace();
		}finally {
			try {
				session.close();
				inputStream.close();
			} catch (IOException e) {
				e.printStackTrace();
			}
		}
```

`如果通过mybatis的openSession()手动获取SqlSession的，则需要手动commit或rollback。如果是openSession(true)获取的SQLSession，则表示自动提交，无效手动commit`。与spring整合后mybatis的	`SQLSession`创建交给了spring容器来管理，所以就无需手动commit了

