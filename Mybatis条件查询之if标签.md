## Mybatis条件查询之if标签

Mybaits中通过if标签配合OGNL表达式可以进行选择行条件添加。

xml映射文件if标签部分代码:

```xml
<!--if条件判断表达式-->
<select id="selectIfCondition" resultType="com.heiketu.testpackage.pojo.Product">
    SELECT
        prod_id prodId,
        vend_id vendId,
        prod_name prodName,
        prod_desc prodDesc
    FROM Products
    <where>
        <if test="prodId != null and prodId != ''">
            prod_id = #{prodId}
        </if>
        AND
        <if test="prodName != null and prodName != ''">
            prod_name = #{prodName}
        </if>
    </where>
</select>
```

因为if标签不会去掉最前面的多余关键字部分。例如`and`关键字。所以配合`where`标签一起使用。

映射接口文件部分代码:

```java
//if条件查询
List<Product> selectIfCondition(Product product);
```

测试代码:

```java
ProductsMapper mapper = sqlSession.getMapper(ProductsMapper.class);
Product product = new Product();
product.setProdName("Fish bean bag toy");
List<Product> products = mapper.selectIfCondition(product);
//打印查询结果
System.out.println(products);
```
**`仅做笔录`**