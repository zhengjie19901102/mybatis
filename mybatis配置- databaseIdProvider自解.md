## mybatis配置: databaseIdProvider自解

```xml
<configuration>
  //....一堆的配置  
  <databaseIdProvider type="DB_VENDOR">
  	<property name="MySQL" value="mysql"/>
  </databaseIdProvider>
  //....一堆的配置  
 </configuration>
```

`databaseIdProvider `的作用就是指定多个数据库厂商，mybatis进行切换到其他数据时可以动态的指定SQL语句进行执行。

在映射文件中需要指定对应的`databaseId`属性：

```xml
<select id="getVendorById" resultType="com.mybatis.bean.Vendor" databaseId="mysql">
    select * from Vendors where vend_id = #{id}
</select>
```
databaseId属性的值与全局配置中`databaseIdProvider `子标签的value对应。