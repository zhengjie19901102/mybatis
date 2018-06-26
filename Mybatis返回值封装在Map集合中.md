## Mybatis返回值封装在Map集合中

数据库表:

```sql
CREATE TABLE `Products` (
  `prod_id` char(10) COLLATE utf8_unicode_ci NOT NULL,
  `vend_id` char(10) COLLATE utf8_unicode_ci NOT NULL,
  `prod_name` char(255) COLLATE utf8_unicode_ci NOT NULL,
  `prod_price` decimal(8,2) NOT NULL,
  `prod_desc` text COLLATE utf8_unicode_ci,
  PRIMARY KEY (`prod_id`),
  KEY `FK_Products_Vendors` (`vend_id`),
  CONSTRAINT `FK_Products_Vendors` FOREIGN KEY (`vend_id`) REFERENCES `Vendors` (`vend_id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8 COLLATE=utf8_unicode_ci
```


映射文件:

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
    PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
    "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.heiketu.testpackage.mapper.ProductsMapper">

<select id="getResultUseMap" resultType="map">
    SELECT * FROM Products WHERE prod_id = #{prod_id}
</select>
</mapper>
```

映射接口:

```xml
public interface ProductsMapper {
    //结果集以Map形式返回
    public Map<String,Object> getResultUseMap(@Param("prod_id") String productId);
}
```

测试代码:

```java
SqlSession sqlSession = null;

try {
    String configFile = "mybatis-config.xml";
    InputStream inputStream = Resources.getResourceAsStream(configFile);
    SqlSessionFactory build = new SqlSessionFactoryBuilder().build(inputStream);
    sqlSession = build.openSession();

    //以Map形式返回
    ProductsMapper mapper = sqlSession.getMapper(ProductsMapper.class);
    Map<String, Object> useMap = mapper.getResultUseMap("BR03");

    //返回形式是key=字段名，value=字段的值
    System.out.println(useMap);

} catch (IOException e) {
    e.printStackTrace();
}finally {
    sqlSession.close();
}
```

结果:

```java
{vend_id=BRS01, prod_desc=18 inch teddy bear, comes with cap and jacket, prod_price=11.99, prod_id=BR03, prod_name=18 inch teddy bear}
```

总结:
`mybatis映射返回值封装成Map需要在映射文件中的resultType属性值设置为map(map为mybatis默认设置的Map集合类别名).`
`封装的结果结构为: {key=字段名，value=字段的值,key=字段名，value=字段的值...}`
