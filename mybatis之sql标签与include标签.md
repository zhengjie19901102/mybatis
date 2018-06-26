## mybatis之sql标签与include标签

mybatis中`sql`标签与`include`标签进行配合，灵活的查询需要的数据。

```xml
<sql id="ref">
    id,name,age,address,companyId
</sql>

<select id="selectbyId" resultType="com.heiketu.pojo.Users">
    select
    <include refid="ref"/>
    from
    usrs where id = #{id}
</select>
```

`sql标签`中id属性对应`include标签`中的refid属性。通过include标签将`sql片段`和原sql片段进行拼接成一个完成的sql语句进行执行。

`include标签中还可以用property标签`,用以指定自定义属性。

```xml
<select id="selectbyId" resultType="com.heiketu.pojo.Users">
    select
    <include refid="ref">
        <property name="abc" value="id"/>
    </include>
    from
    usrs where id = #{id}
</select>
```

此时，可以在`sql标签`中取出对应设置的自定义属性中的值，例如接上代码例子:

```xml
<sql id="ref">
    ${abc},name,age,address,companyId
</sql>

<select id="selectbyId" resultType="com.heiketu.pojo.Users">
    select
    <include refid="ref">
        <property name="abc" value="id"/>
    </include>
    from
    usrs where id = #{id}
</select>
```

在`sql`标签中通过`${}`取出对应`include标签中`设置的属性值。