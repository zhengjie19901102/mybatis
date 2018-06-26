## Mybatis动态SQL之set标签

同`where`标签功能类似，`where`用去去除第一个条件中出现的`and`前缀，那么`set`标签就是去除最后一个更新字段语句中出现的`,[逗号]`后缀。

XML映射文件中update部分:

```xml
<!--set修改标签-->
<update id="updateUseSetTag" parameterType="com.heiketu.testpackage.pojo.Product">
    UPDATE
      Products
        <set>
            <if test="vendId != null and vendId != ''">
                vend_id = #{vendId},
            </if>
            <if test="prodName != null and prodName != ''">
                prod_name = #{prodName},
            </if>
            <if test="prodDesc != null and prodDesc != ''">
                prod_desc = #{prodDesc},
            </if>
        </set>
      <where>
          prod_id = #{prodId}
      </where>
</update>
```

**`where 和 set 完全都可以被trim替换使用。如下代码: `**

```xml
<!--set修改标签-->
<update id="updateUseSetTag" parameterType="com.heiketu.testpackage.pojo.Product">
    UPDATE
      Products
        <trim prefix="set" prefixOverrides="," suffixOverrides=",">
            <if test="vendId != null and vendId != ''">
                vend_id = #{vendId},
            </if>
            <if test="prodName != null and prodName != ''">
                prod_name = #{prodName},
            </if>
            <if test="prodDesc != null and prodDesc != ''">
                prod_desc = #{prodDesc},
            </if>
        </trim>
      <where>
          prod_id = #{prodId}
      </where>
</update>
```
