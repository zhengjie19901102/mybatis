## mybatis查询之bind标签

示例:

接口部分:

```java
 public Users selectById(Users users);
```

xml映射部分:

```xml
<select id="selectById" resultType="com.heiketu.pojo.Users">
     <bind name="abc" value="id"/>
     select * from usrs where id = #{abc}
</select>
```

`bind标签中，value对应传入实体类的某个字段，name属性既给对应字段取的变量名。在value属性中可以使用字符串拼接等特殊处理。`

```xml
特殊处理:
<bind name="xxx" value="'%'+ id + '%'"/>

```