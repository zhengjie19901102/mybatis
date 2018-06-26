## mybatis分步查询与懒加载

开启懒加载需要在maybatis的配置文件中开启懒加载(延迟)模式，关闭积极(自编的= =)加载模式。

```xml
<settings>
	<!--开启懒加载模式-->
    <setting name="lazyLoadingEnabled" value="true"/>
    <!--关闭默认的积极加载模式-->
    <setting name="aggressiveLazyLoading" value="false"/>
</settings>
```

**`也可以通过在association和collection中配置fetchType属性，然后值设置为lazy(懒加载) | eager(饥饿加载)`**

mybatis支持分步查询，要想使用该查询方式首先修改映射文件中的`collection`或`association`子标签(`collection`和`association`的设置类似):

```xml
<collection property="productList" column="vend_id"
	select="com.heiketu.testpackage.mapper.ProductsMapper.selectByVendId">
</collection>
```
属性解释:
`property`属性指定实体类集合字段的字段名

`column`属性指定要传入参数的主键名称(如果主键取了别名，那么就指定要传入的是别名)

`select`属性指定要分步查询的sql语句，mybatis通过`namespace + 查询语句的id`来指定，mybatis会通过拼接的全路径去查找对应映射文件中的sql进行语句执行。

在分步查询的指定映射文件中配置：

```xml
<select id="selectByVendId" resultType="com.heiketu.testpackage.pojo.Product">
    SELECT
        p.prod_id prodId,
        p.vend_id vendId,
        p.prod_name prodName
    FROM
        Products p
    WHERE p.vend_id = #{vend_id}
</select>
```

属性`id`对应分步指定的`select`属性中`namespace + id`的id,`resultType`属性指定返回值类型(集合中泛型限定的类型)
`#{...}`中填写的是分步查询标签中对应的`column`属性。多字段传递采用如下格式:
`{sql语句中的#{}中名称=当前sql中条件字段,sql语句中的#{}中名称=当前sql中条件字段...}`