## Mybatis之一级缓存与二级缓存
`一级缓存: `sqlSession级别的缓存范围,默认开启。

`二级缓存: `namespace级别的缓存范围,需要在mybatis核心配置中开启缓存机制`cacheEnabled `，开启全局地开启或关闭配置文件中的所有映射器已经配置的任何缓存。并且在需要使用二级缓存的Mapper中加上`cache标签`,启用mapper的二级缓存。[实际默认是隐式开启的，但是为了日后更新版本后也能维持开启，所以如果要开启缓存也最好显示设置]

核心配置文件配置位置:

```xml
...
<settings>
	<setting name="cacheEnabled" value="true"/>
</settings>
...
```

`mapper`映射文件:

```xml
<cache eviction="" flushInterval="" readOnly="" size=""></cache>
```

  属性名 |   属性解释
--------|-------------
eviction| 缓存回收策略
flushInterval|缓存刷新间隔
readOnly|缓存是否只读
size | 缓存最大引用数量[就是缓存数量]

`eviction可用的选项:`

值   |  解释
---|---
LRU|最近最少使用的:移除最长时间不被使用的对象。
FIFO|先进先出:按对象进入缓存的顺序来移除它们。
SOFT|软引用:移除基于垃圾回收器状态和软引用规则的对象。
WEAK|弱引用:更积极地移除基于垃圾收集器状态和弱引用规则的对象。
默认值: `LRU`

`flushInterval以毫秒为单位计算:`

```xml
flushInterval=60000  <!--每60秒刷新一次-->
```

`size`(引用数目)可以被设置为任意正整数,要记住你缓存的对象数目和你运行环境的 可用内存资源数目。默认值是 **`1024`**。

`readOnly`(只读)属性可以被设置为 `true` 或 `false`:

值 | 解释
---|----
true|缓存会给所有调用者返回缓，存对象的相同实例。因此这些对象表示不能被修改。性能最高。但是如果修改会有安全问题。
false|可读写的缓存,会返回缓存对象的拷贝(通过序列化) 。这会慢一些,但是安全。

`readOnly默认值: false`

mybatis可以让用户自定义缓存策略:

```xml
<cache type="缓存实现类全路径名"/>
```

这样，我们可用采用第三方缓存实现来使用缓存。例如:`ehcahe`

如果要使用二级缓存，则实体entity需要实现`Serializable接口`。

mybatis还允许引用其他mapper中的缓存配置:

```xml
<cache-ref namespace="[指定mapper的namespace]"/>
```

`缓存概念总结:引用尚硅谷对应文件资料: `

```java

	/**
	 * 两级缓存：
	 * 一级缓存：（本地缓存）：sqlSession级别的缓存。一级缓存是一直开启的；SqlSession级别的一个Map
	 * 		与数据库同一次会话期间查询到的数据会放在本地缓存中。
	 * 		以后如果需要获取相同的数据，直接从缓存中拿，没必要再去查询数据库；
	 * 
	 * 		一级缓存失效情况（没有使用到当前一级缓存的情况，效果就是，还需要再向数据库发出查询）：
	 * 		1、sqlSession不同。
	 * 		2、sqlSession相同，查询条件不同.(当前一级缓存中还没有这个数据)
	 * 		3、sqlSession相同，两次查询之间执行了增删改操作(这次增删改可能对当前数据有影响)
	 * 		4、sqlSession相同，手动清除了一级缓存（缓存清空）
	 * 
	 * 二级缓存：（全局缓存）：基于namespace级别的缓存：一个namespace对应一个二级缓存：
	 * 		工作机制：
	 * 		1、一个会话，查询一条数据，这个数据就会被放在当前会话的一级缓存中；
	 * 		2、如果会话关闭；一级缓存中的数据会被保存到二级缓存中；新的会话查询信息，就可以参照二级缓存中的内容；
	 * 		3、sqlSession===EmployeeMapper==>Employee
	 * 						DepartmentMapper===>Department
	 * 			不同namespace查出的数据会放在自己对应的缓存中（map）
	 * 			效果：数据会从二级缓存中获取
	 * 				查出的数据都会被默认先放在一级缓存中。
	 * 				只有会话提交或者关闭以后，一级缓存中的数据才会转移到二级缓存中
	 * 		使用：
	 * 			1）、开启全局二级缓存配置：<setting name="cacheEnabled" value="true"/>
	 * 			2）、去mapper.xml中配置使用二级缓存：
	 * 				<cache></cache>
	 * 			3）、我们的POJO需要实现序列化接口
	 * 	
	 * 和缓存有关的设置/属性：
	 * 			1）、cacheEnabled=true：false：关闭缓存（二级缓存关闭）(一级缓存一直可用的)
	 * 			2）、每个select标签都有useCache="true"：
	 * 					false：不使用缓存（一级缓存依然使用，二级缓存不使用）
	 * 			3）、【每个增删改标签的：flushCache="true"：（一级二级都会清除）】
	 * 					增删改执行完成后就会清除缓存；
	 * 					测试：flushCache="true"：一级缓存就清空了；二级也会被清除；
	 * 					查询标签：flushCache="false"：
	 * 						如果flushCache=true;每次查询之后都会清空缓存；缓存是没有被使用的；
	 * 			4）、sqlSession.clearCache();只是清除当前session的一级缓存；
	 * 			5）、localCacheScope：本地缓存作用域：（一级缓存SESSION）；当前会话的所有数据保存在会话缓存中；
	 * 								STATEMENT：可以禁用一级缓存；
	 * 				
	 *第三方缓存整合：
	 *		1）、导入第三方缓存包即可；
	 *		2）、导入与第三方缓存整合的适配包；官方有；
	 *		3）、mapper.xml中使用自定义缓存
	 *		<cache type="org.mybatis.caches.ehcache.EhcacheCache"></cache>
	 * 
	 */
```
[以上引用自尚硅谷相关资料](http://www.atguigu.com/)


#### 使用第三方缓存: ehcache

需要jar包:

* mybatis-ehcache
* ehcache
* log4j
* mybatis
* slf4j-log4j12

```xml
<cache type="org.mybatis.caches.ehcache.EhcacheCache">
	<property name="timeToIdleSeconds" value="3600" /><!--1 hour -->
	<property name="timeToLiveSeconds" value="3600" /><!--1 hour -->
	<property name="maxEntriesLocalHeap" value="1000" />
	<property name="maxEntriesLocalDisk" value="10000000" />
	<property name="memoryStoreEvictionPolicy" value="LRU" />
</cache>
```

`cache标签中property标签指定对应mapper中特定的属性值`

引入`ehcache`还需要配置核心缓存配置文件:`ehcache.xml`

```xml
<?xml version="1.0" encoding="UTF-8"?>
<ehcache xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
 xsi:noNamespaceSchemaLocation="../config/ehcache.xsd">
 <!-- 磁盘保存路径 -->
 <diskStore path="/Users/zhengjie/Desktop/a" />
 
 <defaultCache 
   maxElementsInMemory="10000" 
   maxElementsOnDisk="10000000"
   eternal="false" 
   overflowToDisk="true" 
   timeToIdleSeconds="120"
   timeToLiveSeconds="120" 
   diskExpiryThreadIntervalSeconds="120"
   memoryStoreEvictionPolicy="LRU">
 </defaultCache>
</ehcache>

<!-- 
属性说明：
l diskStore：指定数据在磁盘中的存储位置。
l defaultCache：当借助CacheManager.add("demoCache")创建Cache时，EhCache便会采用<defalutCache/>指定的的管理策略
 
以下属性是必须的：
l maxElementsInMemory - 在内存中缓存的element的最大数目 
l maxElementsOnDisk - 在磁盘上缓存的element的最大数目，若是0表示无穷大
l eternal - 设定缓存的elements是否永远不过期。如果为true，则缓存的数据始终有效，如果为false那么还要根据timeToIdleSeconds，timeToLiveSeconds判断
l overflowToDisk - 设定当内存缓存溢出的时候是否将过期的element缓存到磁盘上
 
以下属性是可选的：
l timeToIdleSeconds - 当缓存在EhCache中的数据前后两次访问的时间超过timeToIdleSeconds的属性取值时，这些数据便会删除，默认值是0,也就是可闲置时间无穷大
l timeToLiveSeconds - 缓存element的有效生命期，默认是0.,也就是element存活时间无穷大
 diskSpoolBufferSizeMB 这个参数设置DiskStore(磁盘缓存)的缓存区大小.默认是30MB.每个Cache都应该有自己的一个缓冲区.
l diskPersistent - 在VM重启的时候是否启用磁盘保存EhCache中的数据，默认是false。
l diskExpiryThreadIntervalSeconds - 磁盘缓存的清理线程运行间隔，默认是120秒。每个120s，相应的线程会进行一次EhCache中数据的清理工作
l memoryStoreEvictionPolicy - 当内存缓存达到最大，有新的element加入的时候， 移除缓存中element的策略。默认是LRU（最近最少使用），可选的有LFU（最不常使用）和FIFO（先进先出）
 -->
```




