## Mybatis插件PageHelper使用简单总结

`mybatis-pagehelper`插件能方便的让我们对一批数据进行分页操作。

`maven仓库坐标:`

```xml
<dependency>
    <groupId>com.github.pagehelper</groupId>
    <artifactId>pagehelper</artifactId>
    <version>5.1.4</version>
</dependency>
```

简单使用:

因为是Mybatis插件,所以需要在Mybatis核心配置文件中进行插件的相关配置。

未和`spring`框架整合的需要在mybatis-config的xml文件中进行配置:

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
 ......
	<plugins>
		<plugin interceptor="com.github.pagehelper.PageInterceptor">
			<!-- 使用下面的方式配置参数，参考github上的文档 -->
        	<property name="param1" value="value1"/>
		</plugin>
	</plugins>
</configuration>
```

**`plugins标签位置在settings标签之后，environments标签之前。`**

与`spring`框架整合后，因为mybatis-config.xml文件可以不需要，所以此时需要配置`SqlSessionFactoryBean`到spring的ApplicationContext容器中。所以`plugins`也相应的在spring容器中的`SqlSessionFactoryBean`配置:

```xml
<bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
  <property name="plugins">
    <array>
      <bean class="com.github.pagehelper.PageInterceptor">
        <property name="properties">
          <value>
            params=value1
          </value>
        </property>
      </bean>
    </array>
  </property>
</bean>
```

**`以上配置二选一,整合spring后也只能选其中一种方式配置。`**

配置完成后就可以在代码中使用该该插件了。

```java
SqlSession sqlSession = null;
try {
	SqlSessionFactory sqlSessionFactory = getSqlSession();
	sqlSession = sqlSessionFactory.openSession();
	UserMapper mapper = sqlSession.getMapper(UserMapper.class);
	
	Page<Object> startPage = PageHelper.startPage(2,3);
	List<Users> usersOfPage = mapper.getUsersOfPage();
	
	/**
	 * 页数
	 */
	int pageNum = startPage.getPageNum();
	System.err.println("页码:"+pageNum);
	/**
	 * 总数
	 */
	long total = startPage.getTotal();
	System.err.println("数据总数:"+total);
	/**
     * 进行count查询的列名
     */
	String countColumn = startPage.getCountColumn();
	System.err.println(countColumn);
	/**
     * 排序
     */
	String orderBy = startPage.getOrderBy();
	System.err.println(orderBy);
	/**
     * 总页数
     */
	int pages = startPage.getPages();
	System.err.println(pages);
	/**
     ************* 通过pageInfo对页码导航进行相应的处理 ***************
     */
	PageInfo<Users> pageInfo = new PageInfo(usersOfPage,2);
	int[] navigatepageNums = pageInfo.getNavigatepageNums();
	for (int i : navigatepageNums) {
		System.err.println(i);
	}
	
}catch(Exception e) {
	e.printStackTrace();
}finally {
	sqlSession.close();
}
```

**`重点:`** `PageHelper.startPage`与`PageHelper.offsetPage`的区别: 

> startPage方法有两个参数。第一个参数是启始页,启始页从1开始计，当启始页设置为0时默认为1。第二个参数为一页显示几条数据。

> offsetPage同样有两个参数。但是第一个参数则为启始数据的条数，第二个参数则为从启始数据开始取几条数据。

**`部分摘录自插件作者GitHub上的代码。`**

**`更多参考Github地址:`** **[Mybatis-PageHelper](https://github.com/pagehelper/Mybatis-PageHelper)**