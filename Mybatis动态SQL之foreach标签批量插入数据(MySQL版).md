## Mybatis动态SQL之foreach标签批量插入数据(MySQL版)

我们在MySQL中可以使用`foreach`标签进行批量插入数据。

`foreach`中可以有两种方式批量插入数据:

> 方式1: INTERT INTO TableName Values(值1，值2，值3....),(值1，值2，值3....)....

> 方式2: INTERT INTO TableName Values(值1，值2，值3....);INTERT INTO TableName Values(值1，值2，值3....)...

第一种方式的XML映射文件foreach标签部分如下:

```xml
<insert id="insertByForeachTag" parameterType="com.heiketu.testpackage.mapper.ProductsMapper">
    INSERT INTO Products(prod_id,vend_id,prod_name,prod_desc,prod_price)
      VALUES
    <foreach collection="prods" item="prod" separator=",">
        (#{prod.prodId},#{prod.vendId},#{prod.prodName},#{prod.prodDesc},#{prod.prodPrice})
    </foreach>
</insert>
```
第二种方式的XML映射文件foreach标签部分如下:

```xml
<insert id="insertByForeachTag" parameterType="com.heiketu.testpackage.mapper.ProductsMapper">
    <foreach collection="prods" item="prod" separator=";">
    INSERT INTO
        Products(prod_id,vend_id,prod_name,prod_desc,prod_price)
    VALUES
        (#{prod.prodId},#{prod.vendId},#{prod.prodName},#{prod.prodDesc},#{prod.prodPrice})
    </foreach>
</insert>
```

**`使用第一种方式最佳，每次批量插入数据应该是一次性发一条SQL语句，效率高于每次发同样一条SQL语句。`**
