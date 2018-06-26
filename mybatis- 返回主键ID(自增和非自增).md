## mybatis: 返回主键ID(自增和非自增)

mapper映射文件

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
  PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
  "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.mybatis.mapper.TableNameMapper">
	<!-- 插入数据并返回自增ID
		有自增ID功能数据库可以采用useGeneratedKeys="true"开启判断是否是自增ID
		 keyProperty="id"  指定插入数据后自增ID返回时赋值给实体类的那个属性(这里是id属性)
	 -->
	<insert id="insertData" useGeneratedKeys="true" keyProperty="id">
		insert into tableName values(null,#{name})
	</insert>
	<!-- 
		非自增主键
		像Oracle数据库采用序列来作为自增主键，通过 selectKey子来获取主键值
	 -->
	 <insert id="insertDataAgain" useGeneratedKeys="true" keyProperty="id">
	 	<!-- 
	 		selectKey中resultType属性指定期望主键的返回的数据类型，
	 		keyProperty属性指定实体类对象接收该主键的字段名
	 		order属性指定执行查询主键值SQL语句是在插入语句执行之前还是之后
	 	 -->
	 	<selectKey resultType="integer" keyProperty="id" order="AFTER">
	 		SELECT LAST_INSERT_ID()
	 	</selectKey>
		insert into tableName values(null,#{name})
	</insert>
</mapper>

```

mapper接口

```java
public interface TableNameMapper {
	//插入数据
	public Integer insertData(TableName tableName);
	//插入数据
	public Integer insertDataAgain(TableName tableName);
}
```

测试代码:

```java
InputStream inputStream = null;
SqlSessionFactory sqlSessionFactory = null;
SqlSession session = null;
try {
	inputStream = Resources.getResourceAsStream("mybatis-config.xml");
	sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
	session = sqlSessionFactory.openSession();
	TableNameMapper mapper = session.getMapper(TableNameMapper.class);
	TableName tableName = new TableName();
	tableName.setName("中文");
	//插入数据方式1
	//mapper.insertData(tableName);
	//插入数据方式2
	mapper.insertDataAgain(tableName);
	session.commit();
	System.out.println(tableName.getId());
	
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

**`注: ORACLE返回主键最好是在插入SQL执行之前执行，也就是order属性值设置为before`**

```xml
<!-- 从序列中查询出下一个序列，然后放入实体的对应属性中后再从属性中取出后放入插入语句的values中 -->
<selectKey resultType="integer" keyProperty="id" order="AFTER">
	 		SELECT EVE_SQU.nextval FROM dual
</selectKey>
<insert id="insertData" useGeneratedKeys="true" keyProperty="id">
		insert into tableName values(#{id},#{name})
</insert>
```
