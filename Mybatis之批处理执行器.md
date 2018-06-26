## Mybatis之批处理执行器

使用Mybatis批量操作，需要在全局配置文件或获取sqlSession时设置执行类型为`BATCH`。

**`全局配置文件中配置`**

```xml
<configuration>
	<!--  ...  -->
	<settings>
		<setting name="defaultExecutorType" value="BATCH"/>
	</settings>
	<!-- ... -->
</configuration>

```

**`获取sqlSession时开启批处理执行`**

```objc
InputStream resourceAsStream2 = Resources.getResourceAsStream("mybatis-config.xml");
SqlSessionFactory build2 = new SqlSessionFactoryBuilder().build(resourceAsStream2);
SqlSession openSession2 = build2.openSession(ExecutorType.BATCH);
```

如果与spring框架整合后，那么批处理sqlSession可以配置在spring的bean容器中,使用时获取该sqlSession来进行dao操作。

```xml

<!--配置Mybatis的SqlSessionFactoryBean-->
<bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
    <property name="dataSource" ref="dataSource"/>
    <!--配置mybatis核心配置文件-->
    <property name="configLocation" value="classpath:mybatis-config.xml"/>
    <!--配置mybatis的Mapper映射文件-->
    <property name="mapperLocations" value="classpath:com/test/mybatis/mapper/*.xml"/>
</bean>

<!--  在spring的bean容器中通过sqlSessionFactory配置批处理sqlSession  -->
<bean id="sqlSession" class="org.mybatis.spring.SqlSessionTemplate">
    <constructor-arg name="sqlSessionFactory" ref="sqlSessionFactory"/>
    <constructor-arg name="executorType" value="BATCH"/>
</bean>
```

因为批处理sqlSession已经在spring容器中，所以在 需要批处理的service处直接将批处理sqlSession依赖注入进去，然后通过sqlSession的getMapper方法获取映射接口进行操作即可。

```java

//...
/**
 * 将批处理sqlSession通过依赖注入加入到当前Service中进行批处理dao操作。
 */
@Autowired
private SqlSession sqlSession;
@Override
//...

@Override
public void insertBatch(Users user) {
    UsersMapper usersMapper = sqlSession.getMapper(UsersMapper.class);
    //批量插入操作
    usersMapper.insertBatch(user);
}

```