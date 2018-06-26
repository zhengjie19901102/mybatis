## Mybatis以Map返回，value存各个结果集对象，以指定的字段名作为key

数据库建表语句:

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

数据库数据:

```sql
INSERT INTO sqls.Products (prod_id, vend_id, prod_name, prod_price, prod_desc) VALUES ('BNBG01', 'DLL01', 'Fish bean bag toy', 3.49, 'Fish bean bag toy, complete with bean bag worms with which to feed it');
INSERT INTO sqls.Products (prod_id, vend_id, prod_name, prod_price, prod_desc) VALUES ('BNBG02', 'DLL01', 'Bird bean bag toy', 3.49, 'Bird bean bag toy, eggs are not included');
INSERT INTO sqls.Products (prod_id, vend_id, prod_name, prod_price, prod_desc) VALUES ('BNBG03', 'DLL01', 'Rabbit bean bag toy', 3.49, 'Rabbit bean bag toy, comes with bean bag carrots');
INSERT INTO sqls.Products (prod_id, vend_id, prod_name, prod_price, prod_desc) VALUES ('BR01', 'BRS01', '8 inch teddy bear', 5.99, '8 inch teddy bear, comes with cap and jacket');
INSERT INTO sqls.Products (prod_id, vend_id, prod_name, prod_price, prod_desc) VALUES ('BR02', 'BRS01', '12 inch teddy bear', 8.99, '12 inch teddy bear, comes with cap and jacket');
INSERT INTO sqls.Products (prod_id, vend_id, prod_name, prod_price, prod_desc) VALUES ('BR03', 'BRS01', '18 inch teddy bear', 11.99, '18 inch teddy bear, comes with cap and jacket');
INSERT INTO sqls.Products (prod_id, vend_id, prod_name, prod_price, prod_desc) VALUES ('RGAN01', 'DLL01', 'Raggedy Ann', 4.99, '18 inch Raggedy Ann doll');
INSERT INTO sqls.Products (prod_id, vend_id, prod_name, prod_price, prod_desc) VALUES ('RYL01', 'FNG01', 'King doll', 9.49, '12 inch king doll with royal garments and crown');
INSERT INTO sqls.Products (prod_id, vend_id, prod_name, prod_price, prod_desc) VALUES ('RYL02', 'FNG01', 'Queen doll', 9.49, '12 inch queen doll with royal garments and crown');
```


映射文件:

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.heiketu.testpackage.mapper.ProductsMapper">
    
    <select id="getResultUseMapValue" resultType="com.heiketu.testpackage.pojo.Product">
        SELECT * FROM Products
    </select>
</mapper>
```

映射接口:

```java
public interface ProductsMapper {
    //结果集以Map形式返回，且对应的value为每一个Product对象
    @MapKey("prodId")
    Map<String,Product> getResultUseMapValue();
}
```

实体类对象:

```java
public class Product {

  private String prodId;
  private String vendId;
  private String prodName;
  private Double prodPrice;
  private String prodDesc;


    @Override
    public String toString() {
        return "Product{" +
                "prodId='" + prodId + '\'' +
                ", vendId='" + vendId + '\'' +
                ", prodName='" + prodName + '\'' +
                ", prodPrice=" + prodPrice +
                ", prodDesc='" + prodDesc + '\'' +
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

    public Double getProdPrice() {
        return prodPrice;
    }

    public void setProdPrice(Double prodPrice) {
        this.prodPrice = prodPrice;
    }

    public String getProdDesc() {
        return prodDesc;
    }

    public void setProdDesc(String prodDesc) {
        this.prodDesc = prodDesc;
    }
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
    Map<String, Product> resultUseMapValue = mapper.getResultUseMapValue();
    System.out.println(resultUseMapValue);

} catch (IOException e) {
    e.printStackTrace();
}finally {
    sqlSession.close();
}
```

**`该测试已经在mybaits配置文件中开启驼峰命名法规则`**

测试结果:

```java
{RGAN01=Product{prodId='RGAN01', vendId='DLL01', prodName='Raggedy Ann', prodPrice=4.99, prodDesc='18 inch Raggedy Ann doll'}, BNBG01=Product{prodId='BNBG01', vendId='DLL01', prodName='Fish bean bag toy', prodPrice=3.49, prodDesc='Fish bean bag toy, complete with bean bag worms with which to feed it'}, BNBG02=Product{prodId='BNBG02', vendId='DLL01', prodName='Bird bean bag toy', prodPrice=3.49, prodDesc='Bird bean bag toy, eggs are not included'}, RYL02=Product{prodId='RYL02', vendId='FNG01', prodName='Queen doll', prodPrice=9.49, prodDesc='12 inch queen doll with royal garments and crown'}, RYL01=Product{prodId='RYL01', vendId='FNG01', prodName='King doll', prodPrice=9.49, prodDesc='12 inch king doll with royal garments and crown'}, BR03=Product{prodId='BR03', vendId='BRS01', prodName='18 inch teddy bear', prodPrice=11.99, prodDesc='18 inch teddy bear, comes with cap and jacket'}, BR02=Product{prodId='BR02', vendId='BRS01', prodName='12 inch teddy bear', prodPrice=8.99, prodDesc='12 inch teddy bear, comes with cap and jacket'}, BNBG03=Product{prodId='BNBG03', vendId='DLL01', prodName='Rabbit bean bag toy', prodPrice=3.49, prodDesc='Rabbit bean bag toy, comes with bean bag carrots'}, BR01=Product{prodId='BR01', vendId='BRS01', prodName='8 inch teddy bear', prodPrice=5.99, prodDesc='8 inch teddy bear, comes with cap and jacket'}}
```

**`总结:`**`如果想返回值的key为实体类的主键名，value为各个实体类对象，那么需要在mapper的映射文件中resultType设置为实体类全路径名。在映射接口文件的抽象类方法上加上@MayKey注解并指定以哪个属性名作为key的值。`

