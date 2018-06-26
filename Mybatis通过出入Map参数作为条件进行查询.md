## Mybatis通过出入Map参数作为条件进行查询

映射文件中查询语句部分:

```xml
<!--通过map进行条件查询-->
<select id="selectByMap" resultType="com.heiketu.testpackage.pojo.Product">
    select * from Products where prod_price = #{prodPrice} and prod_desc = #{prodDesc}
</select>
```

接口文件中对应的查询方法:

```java
//输入参数为Map的条件查询
Product selectByMap(Map<String,Object> map);
```

测试代码:

```java
//...前面有创建sqlSessionFactory对象和SQLSession对象的代码

Map<String,Object> map = new HashMap<>();
map.put("prodPrice","11.99");
map.put("prodDesc","18 inch teddy bear, comes with cap and jacket");

ProductsMapper mapper = sqlSession.getMapper(ProductsMapper.class);
Product product = mapper.selectByMap(map);
System.out.println(product);
```

**`map中的key值与映射文件中的select语句#{}占位符中的值需要一一对应`**

在mybatis中，任何传入的参数都会被对应封装成Map集合，然后会以map的key=value形式取值。
对传入的参数是`List`集合，mybatis会对`list`集合进行特殊处理，其取值方式通过`list[下标]`或`collection[下标]`的方式入参取值。
对于数组，Mybatis同样会做特殊处理，它会对数组采用`array[下标]`的方式入参取值。

```xml
<!--如果是List集合(或set集合)就如下入参取值-->
<select id="selectByMap" resultType="com.heiketu.testpackage.pojo.Product">
    select * from Products where prod_price = #{list[0]} and prod_desc = #{collection[1]}
</select>
```


```xml
<!--如果是数组就如下入参取值-->
<select id="selectByMap" resultType="com.heiketu.testpackage.pojo.Product">
    select * from Products where prod_price = #{array[0]} and prod_desc = #{array[1]}
</select>
```
