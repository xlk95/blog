## 什么是cxf？

cxf是Apache旗下的一款webservice框架。

## 使用cxf写一个provider

### 1、 写一个普通的Spring maven项目

![1.png](https://i.loli.net/2019/06/03/5cf4deb1c544488466.png)

### 2、在interface 下依赖cxf

```xml
<dependency>
		<groupId>org.apache.cxf</groupId>
		<artifactId>cxf-rt-frontend-jaxws</artifactId>
		<version>3.1.8</version>
	</dependency>
	<dependency>
		<groupId>org.apache.cxf</groupId>
		<artifactId>cxf-rt-transports-http</artifactId>
		<version>3.1.8</version>
	</dependency>
	<dependency>
		<groupId>org.apache.cxf</groupId>
		<artifactId>cxf-core</artifactId>
		<version>3.1.8</version>
	</dependency>
```

### 3、把接口配成一个webservice

直接在接口上加上`@WebService`注解。

### 4、把cxf融入Spring 

其他框架融入Spring其实就是加入不同的配置文件，cxf的配置文件如下:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xmlns:context="http://www.springframework.org/schema/context"
	xmlns:jaxws="http://cxf.apache.org/jaxws"
	xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
		http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context-4.3.xsd
		http://cxf.apache.org/jaxws http://cxf.apache.org/schemas/jaxws.xsd">

	<!-- 扫描包 -->
	<context:component-scan base-package="com.qf"/>
	
	<!-- 配置webservice访问地址 -->
	<jaxws:server address="/test1">
		<jaxws:serviceBean>
			<ref bean="userServiceImpl"/>
		</jaxws:serviceBean>
	</jaxws:server>
</beans>

```

### 5、配置web.xml文件

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://java.sun.com/xml/ns/javaee" xsi:schemaLocation="http://java.sun.com/xml/ns/javaee http://java.sun.com/xml/ns/javaee/web-app_2_5.xsd" version="2.5">
  <display-name>cs_express_webservicetest_service</display-name>

	<!-- 容器初始化的时候加载Spring容器 -->
	<!-- needed for ContextLoaderListener -->
	<context-param>
		<param-name>contextConfigLocation</param-name>
		<param-value>classpath*:application_*.xml</param-value>
	</context-param>

	<!-- Bootstraps the root web application context before servlet initialization -->
	<listener>
		<listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
	</listener>
	
	<!-- 加入cxf 这个框架的 给我们提供的 一个中央控制器 -->
	 <!-- cxf webservices -->		
    <servlet>		
	  	<servlet-name>cxf</servlet-name>	
	  	<servlet-class>org.apache.cxf.transport.servlet.CXFServlet</servlet-class>	
  	</servlet>		
	<servlet-mapping>		
	  <servlet-name>cxf</servlet-name>	
	  <url-pattern>/service/*</url-pattern>	
	 </servlet-mapping>		
</web-app>
```

