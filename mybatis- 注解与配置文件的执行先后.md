## mybatis: 注解与配置文件

mybatis使用了注解后，同时扫描到映射文件则执行语句会报错。

```java
Exception in thread "main" java.lang.NullPointerException
	at com.mybatis.TestDay01.main(TestDay01.java:37)
```

`如果使用注解后则不能使用映射文件，反之同理。`