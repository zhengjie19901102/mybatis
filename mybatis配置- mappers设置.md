## mybatis配置: mappers设置

在mybatis下mapper映射配置有四种方式:

1. 通过`resource`属性从类路径下注册映射文件。

```xml
<mappers>
    <mapper resource="mybatisMapper.xml"/>
</mappers>
```

2. 通过`url`属性从网络或本来磁盘处注册映射文件:

```xml
<mappers>
	<mapper url="file:////mybatisday01/src/main/resources/mybatisMapper.xml"/>
</mappers>
```

3. 通过`class`属性以接口的方式注册映射文件:

```xml
<mappers>
	<mapper class="com.mybatis.mapper.VendorMapper"/>
</mappers>
```

**`注: 以接口的方式注册映射文件，mybatis需要从接口全类名中获得映射文件，所以接口文件和映射文件放在同一包路径下,文件名必须相同。`**

4. 通过包扫描的方式注册映射文件:

```xml
<mappers>
	<package name="com.mybatis.mapper"/>    
</mappers>
```

**`注: 实际上，包扫描的方式注册与通过接口文件的方式注册相同，都需要将映射文件与接口文件放在同一路径下,文件名必须相同。(感觉包扫描注册就是接口文件注册的一个变体)`**

如果把映射文件与接口文件都放在同一包下，项目一大可能会相当的混乱。那么这个时候可以在源文件配置目录下新建一个同名的包路径用来存放映射文件，应用打包后实际上会将同名的包合并为同一个包路径。

**`注: 在eclipse中，项目最终会把源路径下同名的包合并，所以实际上源文件配置目录中的所有文件会被放置到类路径的根目录下，此时如果配置目录中如果有相同路径则会跟源码的同包路径合并成同一个路径。`**