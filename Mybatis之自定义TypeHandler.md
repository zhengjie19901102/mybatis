## Mybatis之自定义TypeHandler

要使用自定义的`typeHandler`，需要在mybaits配置文件中把自定义的`typeHandler`配置上:

```xml
<!-- 
	设置类型处理器
	handler属性指定自定义类型处理，自定义类型处理器实现TypeHandler接口或继承BaseTypeHandler类
	javaType属性指定当遇到指定Java类型时才用当前设置的TypeHandler处理
 -->
<typeHandlers>
	<typeHandler handler="org.apache.ibatis.type.EnumOrdinalTypeHandler" javaType="com.test.enumbean.EnumStatus"/>
</typeHandlers>
	
<!--
	按一下顺序进行配置:
	properties,
	settings,
	typeAliases,
	typeHandlers,
	objectFactory,
	objectWrapperFactory,
	reflectorFactory,
	plugins,
	environments,
	databaseIdProvider,
	mappers
-->
```

`实现tTypeHandler接口`

```java
public class CustomTypeHandler implements TypeHandler<EnumStatus> {

	/**
	 * SQL语句的参数设置
	 */
	@Override
	public void setParameter(PreparedStatement ps, int i, EnumStatus parameter, JdbcType jdbcType) throws SQLException {
		// TODO Auto-generated method stub
		
	}

	/**
	 * 通过列名SQL返回值
	 */
	@Override
	public EnumStatus getResult(ResultSet rs, String columnName) throws SQLException {
		// TODO Auto-generated method stub
		return null;
	}

	/**
	 * 通过索引SQL返回值
	 */
	@Override
	public EnumStatus getResult(ResultSet rs, int columnIndex) throws SQLException {
		// TODO Auto-generated method stub
		return null;
	}

	/**
	 * 通过存储过程返回值
	 */
	@Override
	public EnumStatus getResult(CallableStatement cs, int columnIndex) throws SQLException {
		// TODO Auto-generated method stub
		return null;
	}
}
```

**`也可以才用继承BaseTypeHandler类来实现自定义TypeHandler`**