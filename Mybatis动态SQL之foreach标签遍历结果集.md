## Mybatis动态SQL之foreach标签遍历结果集

当我们传入一个集合作为参数时，我们可以采用`foreach`标签将结果集遍历出来再设置进SQL语句中。

xml映射文件foreach部分内容:

```xml
<!--foreach遍历标签
    标签属性解析:
    1.collection  传入的集合名
    2.item  foreach标签每遍历一次collection后，每次的结果存入item指定的变量中
    3.open  foreach标签生成的sql语句开始部分
    4.close foreach标签生成的sql语句结束部分
    5.separator  每次遍历出来的结果以separator属性中指定的值分割
    6.index  foreach标签每次遍历时该值在集合的位置。如果是List则值为下标索引，如果是Map则值为Map的Key值。
-->
<select id="selectByForEachTag" resultType="com.heiketu.testpackage.pojo.Product">
    SELECT
        p.prod_id prodId,
        p.vend_id vendId,
        p.prod_name prodName
    FROM Products p
    <foreach collection="ibs" item="coll" open="where prod_id in(" close=")" separator=",">
        #{coll}
    </foreach>
</select>
```

**`重点注意: 因为mybatis会对集合参数进行特殊处理，索引在collection属性值填的集合名需要使用@Param注解手动指定。`**