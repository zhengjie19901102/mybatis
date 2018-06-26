## Mybatis一对多结果集
xml映射文件:

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.heiketu.testpackage.mapper.VendorsMapper">

    <!--结果集一对多或多对多-->
    <resultMap id="manyToMany" type="com.heiketu.testpackage.pojo.Vendor">
        <id column="vend_id" property="vendId"/>
        <result column="vend_name" property="vendName"/>
        <result column="vend_address" property="vendAddress"/>
        <result column="vend_city" property="vendCity"/>
        <result column="vend_state" property="vendState"/>
        <result column="vend_zip" property="vendZip"/>
        <result column="vend_country" property="vendCountry"/>

        <!--链表子结果集-->
        <collection property="productList" ofType="com.heiketu.testpackage.pojo.Product">
            <id column="prod_id" property="prodId"/>
            <result column="vend" property="vendId"/>
            <result column="prod_name" property="prodName"/>
            <result column="prod_desc" property="prodDesc"/>
        </collection>
    </resultMap>

    <select id="getResultManyToMany" resultMap="manyToMany">
      select
         v.*,
         p.prod_id,
         p.vend_id vend,
         p.prod_name prod_name,
         p.prod_desc prod_desc
      from Vendors v,Products p where v.vend_id = p.vend_id and v.vend_id = #{vendorId}
    </select>
</mapper>
```

映射接口:

```java

public interface VendorsMapper {
    //数据一对多或多对多关系
    Vendor getResultManyToMany(@Param("vendorId") String vendorId);
}
```

测试文件:

```java
 SqlSession sqlSession = null;
try {
    String configFile = "mybatis-config.xml";
    InputStream inputStream = Resources.getResourceAsStream(configFile);
    SqlSessionFactory build = new SqlSessionFactoryBuilder().build(inputStream);
    sqlSession = build.openSession();

    //以一对多的结果形式返回
    VendorsMapper mapper = sqlSession.getMapper(VendorsMapper.class);
    Vendor vendor = mapper.getResultManyToMany("FNG01");
    System.out.println(vendor);

} catch (IOException e) {
    e.printStackTrace();
}finally {
    sqlSession.close();
}
```

结果:

```java
Vendor{vendId='FNG01', vendName='Fun and Games', vendAddress='42 Galaxy Road', vendCity='London', vendState='null', vendZip='N16 6PS', vendCountry='England'}
```

**`mybatis获取结果集一对多或多对多，可以使用resultMap的collection。通过指定collection，获取对应的实体类的子结果集。`**

`collection`标签中需指定俩个属性: `property`属性指定对应的子结果集在实体类中的属性名,`ofType`属性指定每个子结果集的对象类型。
