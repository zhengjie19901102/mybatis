## Mybatis返回一对一结果集

mybaits返回一对一结果集有两种方式:

> 方式1: 通过resultMap以OGNL表达式的点属性取值方式设置对象结果集

映射文件

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.heiketu.testpackage.mapper.ProductsMapper">


    <resultMap id="products" type="com.heiketu.testpackage.pojo.Product">
        <id column="prod_id" property="prodId"/>
        <result column="vend_id" property="vendId"/>
        <result column="prod_name" property="prodName"/>
        <result column="prod_desc" property="prodDesc"/>

        <result column="vendId" property="vendor.vendId"/>
        <result column="vend_address" property="vendor.vendAddress"/>
        <result column="vend_country" property="vendor.vendCountry"/>
        <result column="vend_city" property="vendor.vendCity"/>

    </resultMap>

    <select id="getResultByAssociation" resultMap="products">
        select
            p.prod_id prod_id,
            p.vend_id vend_id,
            p.prod_name prod_name,
            v.vend_id vendId,
            v.vend_address vend_address,
            v.vend_city vend_city,
            v.vend_country vend_country
        from Products p,Vendors v where p.vend_id = v.vend_id and p.prod_id = #{productId}
    </select>
</mapper>
```

映射接口:

```java
public interface ProductsMapper {

    //数据一对一查询
    Product getResultByAssociation(@Param("productId") String productId);

}
```

实体对象:

```java

public class Product {

  private String prodId;
  private String vendId;
  private String prodName;
  private String prodDesc;

  private Vendor vendor;

    public Vendor getVendor() {
        return vendor;
    }

    public void setVendor(Vendor vendor) {
        this.vendor = vendor;
    }

    @Override
    public String toString() {
        return "Product{" +
                "prodId='" + prodId + '\'' +
                ", vendId='" + vendId + '\'' +
                ", prodName='" + prodName + '\'' +
                ", prodDesc='" + prodDesc + '\'' +
                ", vendor=" + vendor +
                '}';
    }

    public String getProdId() {
        return prodId;
    }

    public void setProdId(String prodId) {
        this.prodId = prodId;
    }

    public String getVendId() {
        return vendId;
    }

    public void setVendId(String vendId) {
        this.vendId = vendId;
    }

    public String getProdName() {
        return prodName;
    }

    public void setProdName(String prodName) {
        this.prodName = prodName;
    }

    public String getProdDesc() {
        return prodDesc;
    }

    public void setProdDesc(String prodDesc) {
        this.prodDesc = prodDesc;
    }
}
```

测试:

```java
SqlSession sqlSession = null;

try {
    String configFile = "mybatis-config.xml";
    InputStream inputStream = Resources.getResourceAsStream(configFile);
    SqlSessionFactory build = new SqlSessionFactoryBuilder().build(inputStream);
    sqlSession = build.openSession();

    //以一对一的结果形式返回
    ProductsMapper mapper = sqlSession.getMapper(ProductsMapper.class);
    Product product = mapper.getResultByAssociation("BNBG01");

    System.out.println(product);


} catch (IOException e) {
    e.printStackTrace();
}finally {
    sqlSession.close();
}
```

结果:

```java
Product{prodId='BNBG01', vendId='DLL01', prodName='Fish bean bag toy', prodDesc='null', vendor=Vendor{vendId='DLL01', vendName='null', vendAddress='555 High Street', vendCity='Dollsville', vendState='null', vendZip='null', vendCountry='USA'}}
```
> 方式2: 通过resultMap的子标签association链表查询

将映射文件(xml)改为:

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.heiketu.testpackage.mapper.ProductsMapper">

    <resultMap id="products" type="com.heiketu.testpackage.pojo.Product">
        <id column="prod_id" property="prodId"/>
        <result column="vend_id" property="vendId"/>
        <result column="prod_name" property="prodName"/>
        <result column="prod_desc" property="prodDesc"/>

        <association property="vendor" javaType="com.heiketu.testpackage.pojo.Vendor">
            <id property="vendId" column="vendId"/>
            <result property="vendAddress" column="vend_address"/>
            <result property="vendCountry" column="vend_country"/>
            <result property="vendCity" column="vend_city"/>
        </association>
        
    </resultMap>

    <select id="getResultByAssociation" resultMap="products">
        select
            p.prod_id prod_id,
            p.vend_id vend_id,
            p.prod_name prod_name,
            v.vend_id vendId,
            v.vend_address vend_address,
            v.vend_city vend_city,
            v.vend_country vend_country
        from Products p,Vendors v where p.vend_id = v.vend_id and p.prod_id = #{productId}
    </select>
</mapper>
```

`association`子标签指定对应的链表对象。
它主要有以下几个属性:
`property`属性指定返回对象的某个属性，这个属性对应链表的对象。`javaType`属性指定链表对象的全路径名。

**`采用association子标签结构清晰，如果链表对应多个字段更能快速找出对应的字段。`**
