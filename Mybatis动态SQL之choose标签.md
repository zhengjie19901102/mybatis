## Mybatis动态SQL之choose标签

在mybatis中`if`标签是判断，那么`choose`结合`when`和`otherwhen`标签就是分支判断。

```xml
<!--choose分支选择标签-->
<select id="selectByChooseTag" resultType="com.heiketu.testpackage.pojo.Product">
    SELECT
        prod_id prodId,
        vend_id vendId,
        prod_name prodName,
        prod_desc prodDesc
    FROM Products
    <where>
        <choose>
            <when test="prodId!=null and prodId != ''">
                prod_id = #{prodId}
            </when>
            <when test="prod_name!=null and prod_name!= ''">
                prod_name = #{prodName}
            </when>
            <otherwise>
                1=1
            </otherwise>
        </choose>
    </where>
</select>
```

`choose`标签功能是分支判断,配合`when`标签与`otherwhen`标签进行条件过滤。与`if`标签功能不同的是:`if`标签是进行一个一个判断，只要匹配就会执行。而`choose | when | otherwhen`标签则是单个匹配只要有一个配置则会执行匹配的部分，而后面的条件匹配都会被忽略。

**`简单概括: if是匹配所有执行的是所有匹配的部分，choose|when|otherwhen逐个匹配只执行第一个匹配的部分`**


