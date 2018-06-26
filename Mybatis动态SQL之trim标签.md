## Mybatis动态SQL之trim标签

`Mybatis中where标签只能去除第一个条件中出现的前缀AND关键字。当因为条件满足多出了一个条件关键字时，where无法去除会导致SQL语法报错。此时可以考虑trim标签[Mybatis中where标签底层也是使用了trim标签实现]。`

```xml
<!--trim修剪前后缀标签-->
<select id="selectTestByTrim" resultType="com.heiketu.testpackage.pojo.Product">
    SELECT
        prod_id prodId,
        vend_id vendId,
        prod_name prodName,
        prod_desc prodDesc
    FROM Products
    <trim prefix="WHERE" prefixOverrides="and|or" suffixOverrides="and|or">
        <if test="prodId != null and prodId != ''">
            AND prod_id = #{prodId} AND
        </if>

        <if test="prodName != null and prodName != ''">
            AND prod_name = #{prodName} AND
        </if>
    </trim>
</select>
```

以上SQL中多现了多处`AND`，按照以往采用WHERE标签，已无法解决修剪SQL语句的作用。此时采用了trim标签。

在trim标签中有以下几个属性:

属性名  |  属性解释
-------| --------
prefix |  要添加的前缀。比如这里因为没有使用**`WHERE`**，按照语法格式条件必须要有**`WHERE`**。此时就可以使用prefix属性给整个条件语句块前面添加一个**`WHERE`**关键字。
prefixOverrides | 要删除的前缀。如果多出来指定的前缀，则将这些多出来的前缀修剪去除。比如:如果在**整个**语句中需要修剪去`AND|OR`等相关关键字，则可以在此属性中指定对应要修剪去的前缀
suffix | 要添加的后缀。使用方式与prefix类似，只是prefix是增加前缀，而suffix是添加一个后缀在被包裹的语句块中。
suffixOverrides | 要删除的后缀。使用方式与prefixOverrides类似，只是一个是去除前面多余的关键字，suffixOverrides是删除多余后面的。

**`prefix如果有值，则会替换掉prefixOverrides中的部分。suffix也相同。如果没有值则是默认为空。`**
