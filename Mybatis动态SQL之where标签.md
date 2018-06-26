## Mybatis动态SQL之where标签

xml映射文件部分内容:

```xml
<select id="selectIfCondition" resultType="com.heiketu.testpackage.pojo.Product">
    SELECT
        prod_id prodId,
        vend_id vendId,
        prod_name prodName,
        prod_desc prodDesc
    FROM Products
    <where>
        <if test="prodId != null and prodId != ''">
            AND prod_id = #{prodId}
        </if>
        
        <if test="prodName != null and prodName != ''">
            AND prod_name = #{prodName}
        </if>
    </where>
</select>
```

映射文件中的`where`标签可以过滤掉条件语句中的第一个`and`或`or`关键字。以上SQL当`prodId`!=null时会被mybatis的`where`标签去除掉多余的`and`关键字，生成的sql如下:

```sql
SELECT
    prod_id prodId,
    vend_id vendId,
    prod_name prodName,
    prod_desc prodDesc
FROM Products
WHERE
	prod_id = ?
AND
	prod_name = ?
```

**`注: where标签只能去除第一个条件中出现的前置 and 关键字。像以下情况，where就无能为力了:`**

```xml
<select id="selectIfCondition" resultType="com.heiketu.testpackage.pojo.Product">
    SELECT
        prod_id prodId,
        vend_id vendId,
        prod_name prodName,
        prod_desc prodDesc
    FROM Products
    <where>
        <if test="prodId != null and prodId != ''">
            prod_id = #{prodId} AND
        </if>
        
        <if test="prodName != null and prodName != ''">
            prod_name = #{prodName} AND
        </if>
    </where>
</select>
```

Mybatis会把次SQL语句拼接成:

```sql
SELECT
    prod_id prodId,
    vend_id vendId,
    prod_name prodName,
    prod_desc prodDesc
FROM Products
WHERE
	prod_id = ? AND
	prod_name = ? AND
```

**`where无法去除掉后面的and关键字，此时sql语句出现语法错误。`**

要想解决以上`where`标签无法处理的问题，可以考虑使用`trim`标签。