## Mybatis插件示例

**`拦截器部分`**

```java
@Intercepts(
	value = {
			/**
			 * statementHandler --会调用--> PreparedStatementHandler
			 */
			@Signature(/** 要拦截的四大组件之一的对象 */ type=StatementHandler.class,
			/** 要拦截的四大组件之一的对象的某个方法 */
			method="parameterize",
			/** 要拦截的四大组件之一的对象的某个方法的参数 */
			args={java.sql.Statement.class})
	}
)
public class MyInteceptor implements Interceptor {

	/**
	 * 当调用目标方法前执行intercept方法，调用目标方法并返回调用后的结果
	 * @param invocation <code>目标方法的代理方法</code>
	 */
	@Override
	public Object intercept(Invocation invocation) throws Throwable {
		//System.err.println("intercept方法:" + invocation.getMethod().getName());
		
		Object target = invocation.getTarget();
		MetaObject metaObject = SystemMetaObject.forObject(target);
		Object value = metaObject.getValue("parameterHandler.parameterObject");
		System.err.println(value);
		
		metaObject.setValue("parameterHandler.parameterObject", 2);
		Object value2 = metaObject.getValue("parameterHandler.parameterObject");
		System.err.println(value2);
		
		Object proceed = invocation.proceed();
		return proceed;
	}

	/**
	 * 每次调用目标对象的方法时会调用plugin方法，可以对<code>target</code>参数设置代理对象，并返回代理对象
	 * @param target <code>目标方法</code>
	 *  Map<Class<?>, Set<Method>> signatureMap = getSignatureMap(interceptor);
	 *  Class<?> type = target.getClass();
	 *  Class<?>[] interfaces = getAllInterfaces(type, signatureMap);
	 *  if (interfaces.length > 0) {
	 *    return Proxy.newProxyInstance(
	 *        type.getClassLoader(),
	 *        interfaces,
	 *        new Plugin(target, interceptor, signatureMap));
	 *  }
	 *  return target;
	 */
	@Override
	public Object plugin(Object target) {
		System.err.println("target方法:" + target);
		Object wrap = Plugin.wrap(target, this);
		return wrap;
	}

	/**
	 * Mybatis在设置插件时会将调用setProperties方法设置properties相关数据
	 * @param properties <code>在XML的plugin标签中设置的property标签相关值</code>
	 */
	@Override
	public void setProperties(Properties properties) {
		System.err.println("setProperties方法:" + properties);
	}

}
```

通过`mybatis`的`Plugin`工具类将目标对象包装成代理对象。

**`核心配置部分`**

```xml
<configuration>

	<settings>
		<setting name="cacheEnabled" value="true" />
	</settings>
	
	<!-- plugins在settings之后 -->
	<plugins>
		<plugin interceptor="com.test.intercept.MyInteceptor">
			<property name="username" value="root"/>
			<property name="password" value="admin"/>
		</plugin>
	</plugins>
	
	<environments default="development">
		<environment id="development">
			<transactionManager type="JDBC" />
			<dataSource type="POOLED">
				<property name="driver" value="com.mysql.jdbc.Driver" />
				<property name="url"
					value="jdbc:mysql://127.0.0.1:3306/sqls?useUnicode=true&amp;characterEncoding=utf-8" />
				<property name="username" value="root" />
				<property name="password" value="admin" />
			</dataSource>
		</environment>
	</environments>
	<databaseIdProvider type="DB_VENDOR">
		<property name="MySQL" value="mysql" />
	</databaseIdProvider>
	<mappers>
		<package name="com.test.mapper" />
	</mappers>
</configuration>
```

**`重点: plugin标签在settings标签之后`**

> 多个插件会对目标对象进行多次包装。

> 多个插件会按照插件的配置顺序对目标对象进行创建代理对象。(层层包装)

> 执行时是以从外向内的顺序进行执行。(包装完层层解开,像拆箱子一样。要箱子里面的东西[方法]则需要先拆开外面的包装[执行外层的方法，也就是最后的代理])。  