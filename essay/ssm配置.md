### SSM的各种配置文件

### Mybatis逆向工程

首先我们要在项目中导入`mybatis-generator-core`，maven坐标为：	

```xml
<dependency>
    <groupId>org.mybatis.generator</groupId>
    <artifactId>mybatis-generator-core</artifactId>
</dependency>
```

配置文件`mbg.xml`直接放在该项目下，内容在官网中可以找到，自己的配置如下：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE generatorConfiguration
  PUBLIC "-//mybatis.org//DTD MyBatis Generator Configuration 1.0//EN"
  "http://mybatis.org/dtd/mybatis-generator-config_1_0.dtd">

<generatorConfiguration>


  <context id="DB2Tables" targetRuntime="MyBatis3">
  	<commentGenerator>
		<property name="suppressAllComments" value="true"/>
	</commentGenerator>
	<!--配置jdbc-->
    <jdbcConnection driverClass="com.mysql.jdbc.Driver"
        connectionURL="jdbc:mysql://localhost:3306/zc"
        userId="root"
        password="123">
    </jdbcConnection>

    <javaTypeResolver >
      <property name="forceBigDecimals" value="false" />
    </javaTypeResolver>

	<!-- pojo生成的地方 -->
    <javaModelGenerator 
    targetPackage="com.kk.zc.manage.entity" 
    targetProject="..\manage-pojo\src\main\java">
      <property name="enableSubPackages" value="true" />
      <property name="trimStrings" value="true" />
    </javaModelGenerator>

	<!-- sql映射文件生成的地方 -->
    <sqlMapGenerator targetPackage="mybatis.mapper"
     targetProject=".\src\main\resources">
      <property name="enableSubPackages" value="true" />
    </sqlMapGenerator>

	<!-- 接口生成的地方 -->
    <javaClientGenerator type="XMLMAPPER" 
    targetPackage="com.kk.zc.manage.mapper"  
    targetProject=".\src\main\java">
      <property name="enableSubPackages" value="true" />
    </javaClientGenerator>
	<!--要逆向生成的表-->
	<table tableName="t_user" domainObjectName="TUser"></table>
  </context>
</generatorConfiguration>

```

运行下面的程序即可生成代码：

```java
   public static void main(String[] args) throws Exception {
       List<String> warnings = new ArrayList<String>();
       boolean overwrite = true;
       // 只要修改xml的名字即可
       File configFile = new File("mbg.xml");
       ConfigurationParser cp = new ConfigurationParser(warnings);
       Configuration config = cp.parseConfiguration(configFile);
       DefaultShellCallback callback = new DefaultShellCallback(overwrite);
       MyBatisGenerator myBatisGenerator = new MyBatisGenerator(config, callback, warnings);
       myBatisGenerator.generate(null);
       System.out.println("success");
   }
```

### Mybatis的配置文件

`mybatis-config.xml`配置如下：

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
  PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
  "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
	<settings>
		<setting name="mapUnderscoreToCamelCase" value="true" />
	</settings>
	<!-- 分页插件 -->
	<plugins>
		<plugin interceptor="com.github.pagehelper.PageInterceptor"></plugin>
	</plugins>
</configuration>
```



### web.xml

```
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://java.sun.com/xml/ns/javaee" xsi:schemaLocation="http://java.sun.com/xml/ns/javaee http://java.sun.com/xml/ns/javaee/web-app_2_5.xsd" id="WebApp_ID" version="2.5">

	<!-- 配置spring -->
	<!-- needed for ContextLoaderListener -->
	<context-param>
		<param-name>contextConfigLocation</param-name>
		<param-value>classpath:spring-*.xml</param-value>
	</context-param>

	<!-- Bootstraps the root web application context before servlet initialization -->
	<listener>
		<listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
	</listener>

	<!-- 配置MVC -->
	<!-- The front controller of this Spring Web application, responsible for handling all application requests -->
	<servlet>
		<servlet-name>springDispatcherServlet</servlet-name>
		<servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
		<init-param>
			<param-name>contextConfigLocation</param-name>
			<param-value>classpath:applicationContext_*.xml</param-value>
		</init-param>
		<load-on-startup>1</load-on-startup>
	</servlet>

	<!-- Map all requests to the DispatcherServlet for handling -->
	<servlet-mapping>
		<servlet-name>springDispatcherServlet</servlet-name>
		<url-pattern>/</url-pattern>
	</servlet-mapping>
	
	<!-- 加上字符编码过滤器 -->
	<filter>
		<filter-name>CharacterEncodingFilter</filter-name>
		<filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
		<!-- 只是指定了编码格式 -->
		<init-param>
			<param-name>encoding</param-name>
			<param-value>utf-8</param-value>
		</init-param>
		<!-- 进行请求乱码解决 -->
		<init-param>
			<param-name>forceRequestEncoding</param-name>
			<param-value>true</param-value>
		</init-param>
		<init-param>
			<param-name>forceResponseEncoding</param-name>
			<param-value>true</param-value>
		</init-param>
	</filter>
	<filter-mapping>
		<filter-name>CharacterEncodingFilter</filter-name>
		<url-pattern>/*</url-pattern>
	</filter-mapping>
</web-app>
```

### 配置bean

`spring-bean.xml`，它需要XSD namespace有`beans`和`context`

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xmlns:context="http://www.springframework.org/schema/context"
	xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
		http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context-4.3.xsd">

	<!-- 负责包扫描  组件扫描 -->
	<!-- 不扫描controller -->
	<context:component-scan base-package="com.kk.zc.manage">
		<context:exclude-filter type="annotation"
		expression="org.springframework.stereotype.Controller"/>
	</context:component-scan>
	
	<!-- 配置数据源 -->
	<bean id="dataSource" class="com.alibaba.druid.pool.DruidDataSource">
		<property name="username" value="root"></property>
		<property name="password" value="123"></property>
		<property name="url" value="jdbc:mysql://localhost:3306/zc"></property>
		<property name="driverClassName" value="com.mysql.jdbc.Driver"></property>
		<property name="initialSize" value="5"></property>
	</bean>
</beans>

```

### 配置事务切面

`spring-tx`,它需要XSD namespace有`aop`、`beans`和`tx`

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xmlns:aop="http://www.springframework.org/schema/aop"
	xmlns:tx="http://www.springframework.org/schema/tx"
	xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
		http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop-4.3.xsd
		http://www.springframework.org/schema/tx http://www.springframework.org/schema/tx/spring-tx-4.3.xsd">

	<!-- Spring 一启动  加载所有spring配置文件 -->
	<!-- 配置事务管理器 -->
	
	<bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
		<!-- 配置数据源 -->
		<property name="dataSource" ref="dataSource"></property>
	</bean>
	
	<!-- 配置事务切面 -->
	<aop:config>
		<!-- 配置切入点 -->
		<!-- 切入点表达式 -->
		<aop:pointcut expression="execution(* com.kk.zc.manage.service.*.*(..))" 
			id="txPoint"/>
		<!-- 事务增强 -->
		<aop:advisor advice-ref="myAdvice" pointcut-ref="txPoint"/>
	</aop:config>
	<!-- 配置事务增强 -->
	<tx:advice id="myAdvice" transaction-manager="transactionManager">
		<!-- 事务属性 -->
		<tx:attributes>
			<tx:method name="*"/>
			<tx:method name="get*" propagation="SUPPORTS" read-only="true"/>
		</tx:attributes>
	</tx:advice>
</beans>

```

### 配置Spring-mybatis

`spring-mybatis.xml`内容如下：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xmlns:mybatis-spring="http://mybatis.org/schema/mybatis-spring"
	xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
		http://mybatis.org/schema/mybatis-spring http://mybatis.org/schema/mybatis-spring-1.2.xsd">


	<!-- 配置用mybatis操作数据库 -->
	<!-- 配置sqlSessionFactory -->
	<bean  id="sqlSessionFactoryBean" class="org.mybatis.spring.SqlSessionFactoryBean">
		<!-- 指定mybatis全局配置文件 -->
		<property name="configLocation" value="classpath:mybatis/mybatis-config.xml"/>
		<!-- 指定数据源 -->
		<property name="dataSource" ref="dataSource"></property>
		<!-- 指定映射文件的位置 -->
		<property name="mapperLocations" value="classpath:mybatis/mapper/*.xml"></property>
	</bean>
	<!-- 扫描dao的接口 的实现类自动加入到ioc容器中-->
	<bean class="org.mybatis.spring.mapper.MapperScannerConfigurer">
		<property name="basePackage" value="com.kk.zc.manage.mapper"></property>
	</bean>
</beans>

```

### SpringMVC的配置

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xmlns:context="http://www.springframework.org/schema/context"
	xmlns:mvc="http://www.springframework.org/schema/mvc"
	xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
		http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context-4.3.xsd
		http://www.springframework.org/schema/mvc http://www.springframework.org/schema/mvc/spring-mvc-4.3.xsd">


	<!-- 配置扫描controller -->
	<context:component-scan base-package="com.kk.zc.manage.controller" use-default-filters="false">
		<context:include-filter type="annotation" expression="org.springframework.stereotype.Controller"/>
	</context:component-scan>
	
	<!-- 配置视图解析器 -->
	<bean class="org.springframework.web.servlet.view.InternalResourceViewResolver">
		<property name="prefix" value="/WEB-INF/jsps/"></property>
		<property name="suffix" value=".jsp"></property>
	</bean>
	<!-- 映射动态资源 -->
	<mvc:annotation-driven/>
	<!-- 映射静态资源 -->
	<mvc:default-servlet-handler/>
</beans>

```

