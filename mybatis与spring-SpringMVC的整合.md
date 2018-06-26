## mybatis与spring-SpringMVC的整合

`spring.xml`

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context" xmlns:tx="http://www.springframework.org/schema/tx"
       xmlns:mybatis="http://mybatis.org/schema/mybatis-spring"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd http://www.springframework.org/schema/tx http://www.springframework.org/schema/tx/spring-tx.xsd http://mybatis.org/schema/mybatis-spring http://mybatis.org/schema/mybatis-spring.xsd">

    <context:component-scan base-package="com.test.mybatis">
        <context:exclude-filter type="annotation" expression="org.springframework.stereotype.Controller"/>
    </context:component-scan>


    <context:property-placeholder location="classpath:dataSource.properties"/>
    <!--设置数据源-->
    <bean id="dataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource">
        <property name="user" value="${data.user}"/>
        <property name="password" value="${data.password}"/>
        <property name="jdbcUrl" value="${data.url}"/>
        <property name="driverClass" value="${data.class}"/>
    </bean>

    <!--配置事物管理器-->
    <bean id="dataSourceTransactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
        <property name="dataSource" ref="dataSource"/>
    </bean>

    <!--开启注解事物驱动-->
    <tx:annotation-driven transaction-manager="dataSourceTransactionManager"/>

    <!--配置Mybatis的SqlSessionFactoryBean-->
    <bean class="org.mybatis.spring.SqlSessionFactoryBean">
        <property name="dataSource" ref="dataSource"/>
        <!--配置mybatis核心配置文件-->
        <property name="configLocation" value="classpath:mybatis-config.xml"/>
        <!--配置mybatis的Mapper映射文件-->
        <property name="mapperLocations" value="classpath:com/test/mybatis/mapper/*.xml"/>
    </bean>

    <!--原Mybatis的Mapper包扫描Bean-->
    <!--<bean class="org.mybatis.spring.mapper.MapperScannerConfigurer">
        <property name="basePackage" value="com.test.mybatis.mapper"/>
    </bean>-->

    <!--mybatis的Mapper包扫描，将mapper接口加入到spring容器中，这样子就可以使用Autowried了-->
    <mybatis:scan base-package="com.test.mybatis.mapper" />

</beans>
```

`springmvc.xml`

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:mvc="http://www.springframework.org/schema/mvc"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd http://www.springframework.org/schema/mvc http://www.springframework.org/schema/mvc/spring-mvc.xsd">

<context:component-scan base-package="com.test.mybatis" use-default-filters="false">
    <context:include-filter type="annotation" expression="org.springframework.stereotype.Controller"/>
</context:component-scan>
<!--开启注解驱动-->
<mvc:annotation-driven/>
<!--设置默认servlet处理器-->
<mvc:default-servlet-handler/>

<!--配置视图解析器-->
<bean id="viewResolver" class="org.springframework.web.servlet.view.InternalResourceViewResolver">
    <property name="prefix" value="/WEB-INF/jsp/"/>
    <property name="suffix" value=".jsp"/>
</bean>

</beans>
```

**`在maven中，xml文件是不会被拷贝到包路径下的，所有在maven的resources插件中需要配置如下:`**

```xml
<plugin>
<artifactId>maven-resources-plugin</artifactId>
<version>3.0.2</version>
<executions>
<execution>
  <id>copy-xmls</id>
  <phase>process-sources</phase>
  <goals>
    <goal>copy-resources</goal>
  </goals>
  <!--使maven将xml文件能够拷贝到包路径下-->
  <configuration>
    <outputDirectory>${basedir}/target/classes</outputDirectory>
    <resources>
      <resource>
        <directory>${basedir}/src/main/java</directory>
        <includes>
          <include>**/*.xml</include>
        </includes>
      </resource>
    </resources>
  </configuration>
</execution>
</executions>
</plugin>
```

`log4j配置文件:`

```xml
# Global logging configuration
log4j.rootLogger=INFO, stdout

# My logging configuration...
log4j.logger.org.mybatis.jpetstore=TRACE
log4j.logger.org.mybatis.spring=TRACE

## Console output...
log4j.appender.stdout=org.apache.log4j.ConsoleAppender
log4j.appender.stdout.layout=org.apache.log4j.PatternLayout
log4j.appender.stdout.layout.ConversionPattern=%r [%t] %-5p %c %x - %m%n
```

`web.xml中:`

```xml
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
     xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
     xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee
     http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
     version="4.0">


<listener>
    <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
</listener>

<context-param>
    <param-name>contextConfigLocation</param-name>
    <param-value>classpath:ApplicationContext.xml</param-value>
</context-param>

<servlet>
    <servlet-name>springMVC</servlet-name>
    <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
    <init-param>
        <param-name>contextConfigLocation</param-name>
        <param-value>classpath:springmvc.xml</param-value>
    </init-param>
    <load-on-startup>1</load-on-startup>
</servlet>

<servlet-mapping>
    <servlet-name>springMVC</servlet-name>
    <url-pattern>/</url-pattern>
</servlet-mapping>
</web-app>
```

依赖包(有多余不需要的jar):

```xml
<!-- https://mvnrepository.com/artifact/org.mybatis/mybatis-spring -->
<dependency>
  <groupId>org.mybatis</groupId>
  <artifactId>mybatis-spring</artifactId>
  <version>1.3.2</version>
</dependency>
<!-- https://mvnrepository.com/artifact/log4j/log4j -->
<dependency>
  <groupId>log4j</groupId>
  <artifactId>log4j</artifactId>
  <version>1.2.17</version>
</dependency>
<!-- https://mvnrepository.com/artifact/org.apache.logging.log4j/log4j-api -->
<dependency>
  <groupId>org.apache.logging.log4j</groupId>
  <artifactId>log4j-api</artifactId>
  <version>2.0</version>
</dependency>
<!-- https://mvnrepository.com/artifact/org.apache.tomcat/tomcat-catalina -->
<dependency>
  <groupId>org.apache.tomcat</groupId>
  <artifactId>tomcat-catalina</artifactId>
  <version>8.0.52</version>
  <scope>provided</scope>
</dependency>
<!-- https://mvnrepository.com/artifact/org.slf4j/slf4j-api -->
<dependency>
  <groupId>org.slf4j</groupId>
  <artifactId>slf4j-api</artifactId>
  <version>1.7.25</version>
</dependency>
<!-- https://mvnrepository.com/artifact/mysql/mysql-connector-java -->
<dependency>
  <groupId>mysql</groupId>
  <artifactId>mysql-connector-java</artifactId>
  <version>5.1.46</version>
</dependency>
<!-- https://mvnrepository.com/artifact/org.aspectj/aspectjweaver -->
<dependency>
  <groupId>org.aspectj</groupId>
  <artifactId>aspectjweaver</artifactId>
  <version>1.9.1</version>
</dependency>
<!-- https://mvnrepository.com/artifact/commons-pool/commons-pool -->
<dependency>
  <groupId>commons-pool</groupId>
  <artifactId>commons-pool</artifactId>
  <version>1.6</version>
</dependency>
<!-- https://mvnrepository.com/artifact/com.mchange/c3p0 -->
<dependency>
  <groupId>com.mchange</groupId>
  <artifactId>c3p0</artifactId>
  <version>0.9.5.2</version>
</dependency>
<dependency>
  <groupId>javax.servlet</groupId>
  <artifactId>jstl</artifactId>
  <version>1.2</version>
</dependency>
<!-- https://mvnrepository.com/artifact/org.springframework/spring-tx -->
<dependency>
  <groupId>org.springframework</groupId>
  <artifactId>spring-tx</artifactId>
  <version>4.3.17.RELEASE</version>
</dependency>

<dependency>
  <groupId>org.apache.taglibs</groupId>
  <artifactId>taglibs-standard-spec</artifactId>
  <version>1.2.5</version>
</dependency>
<dependency>
  <groupId>org.apache.taglibs</groupId>
  <artifactId>taglibs-standard-impl</artifactId>
  <version>1.2.5</version>
</dependency>


<!-- https://mvnrepository.com/artifact/org.mybatis/mybatis -->
<dependency>
  <groupId>org.mybatis</groupId>
  <artifactId>mybatis</artifactId>
  <version>3.4.6</version>
</dependency>
<dependency>
  <groupId>org.springframework</groupId>
  <artifactId>spring-webmvc</artifactId>
  <version>4.3.17.RELEASE</version>
</dependency>
<dependency>
  <groupId>junit</groupId>
  <artifactId>junit</artifactId>
  <version>4.11</version>
  <scope>test</scope>
</dependency>
<!-- https://mvnrepository.com/artifact/org.springframework/spring-jdbc -->
<dependency>
  <groupId>org.springframework</groupId>
  <artifactId>spring-jdbc</artifactId>
  <version>4.3.17.RELEASE</version>
</dependency>
```





	