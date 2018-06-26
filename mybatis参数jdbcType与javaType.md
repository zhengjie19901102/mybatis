##Mybatis参数理解:jdbcType与javaType
mybaits在指定SQL参数时其中可以指定以下了俩种类型：

*  jdbcType
*  javaType

jdbcType指定对应参数在数据库的数据类型

javaType指定对应java的数据类型

使用示例如下:

```sql
select * from Vendors where vend_id = #{id,jdbcType=VARCHAR,javaType=integer} 
```

jdbcType的值可以使用JdbcType枚举类其中的值，其全部可选值如下:

```java
ARRAY(Types.ARRAY),
BIT(Types.BIT),
TINYINT(Types.TINYINT),
SMALLINT(Types.SMALLINT),
INTEGER(Types.INTEGER),
BIGINT(Types.BIGINT),
FLOAT(Types.FLOAT),
REAL(Types.REAL),
DOUBLE(Types.DOUBLE),
NUMERIC(Types.NUMERIC),
DECIMAL(Types.DECIMAL),
CHAR(Types.CHAR),
VARCHAR(Types.VARCHAR),
LONGVARCHAR(Types.LONGVARCHAR),
DATE(Types.DATE),
TIME(Types.TIME),
TIMESTAMP(Types.TIMESTAMP),
BINARY(Types.BINARY),
VARBINARY(Types.VARBINARY),
LONGVARBINARY(Types.LONGVARBINARY),
NULL(Types.NULL),
OTHER(Types.OTHER),
BLOB(Types.BLOB),
CLOB(Types.CLOB),
BOOLEAN(Types.BOOLEAN),
CURSOR(-10), // Oracle
UNDEFINED(Integer.MIN_VALUE + 1000),
NVARCHAR(Types.NVARCHAR), // JDK6
NCHAR(Types.NCHAR), // JDK6
NCLOB(Types.NCLOB), // JDK6
STRUCT(Types.STRUCT),
JAVA_OBJECT(Types.JAVA_OBJECT),
DISTINCT(Types.DISTINCT),
REF(Types.REF),
DATALINK(Types.DATALINK),
ROWID(Types.ROWID), // JDK6
LONGNVARCHAR(Types.LONGNVARCHAR), // JDK6
SQLXML(Types.SQLXML), // JDK6
DATETIMEOFFSET(-155); // SQL Server 2008
```

`JdbcType枚举类在mybatis框架的org.apache.ibatis.type包下`

