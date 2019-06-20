# SSM三大框架融合

## 导入所需要的包

### Spring的五个包

```xml
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-context</artifactId>
    <version>4.3.14.RELEASE</version>
</dependency>

<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-context-support</artifactId>
    <version>4.3.14.RELEASE</version>
</dependency>

<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-core</artifactId>
    <version>4.3.14.RELEASE</version>
</dependency>

<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-beans</artifactId>
    <version>4.3.14.RELEASE</version>
</dependency>

<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-expression</artifactId>
    <version>4.3.14.RELEASE</version>
</dependency>
```

### 面向切面的三个包

```xml
<dependency>
    <groupId>aopalliance</groupId>
    <artifactId>aopalliance</artifactId>
    <version>1.0</version>
</dependency>
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-aspects</artifactId>
    <version>4.3.14.RELEASE</version>
</dependency>
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-aop</artifactId>
    <version>4.3.14.RELEASE</version>
</dependency>
```

### mybatis及其相关的包

```xml
<!--mybatis-->
<dependency>
    <groupId>org.mybatis</groupId>
    <artifactId>mybatis</artifactId>
    <version>${mybatis.version}</version>
</dependency>
<!-- mybatis 整合入spring 的包 -->
<dependency>
    <groupId>org.mybatis</groupId>
    <artifactId>mybatis-spring</artifactId>
    <version>1.2.2</version>
</dependency>
<!-- 单元测试 -->
<dependency>
    <groupId>junit</groupId>
    <artifactId>junit</artifactId>
    <version>${junit.version}</version>
    <scope>test</scope>
</dependency>
<!-- spring 测试包 junit 被spring管理 -->
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-test</artifactId>
    <version>4.3.14.RELEASE</version>
</dependency>
<!-- 日志处理 -->
<dependency>
    <groupId>org.slf4j</groupId>
    <artifactId>slf4j-log4j12</artifactId>
    <version>1.6.6</version>
</dependency>
<!-- MySql -->
<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
    <version>${mysql.version}</version>
</dependency>
<!-- 事务切面 -->
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-jdbc</artifactId>
    <version>4.3.14.RELEASE</version>
</dependency>
<!-- 帶有连接池功能的數據源 -->
<dependency>
    <groupId>com.alibaba</groupId>
    <artifactId>druid</artifactId>
    <version>1.0.9</version>
</dependency>
<dependency>
    <groupId>com.mchange</groupId>
    <artifactId>c3p0</artifactId>
    <version>0.9.5-pre10</version>
</dependency>
<!-- mybatis 的分页插件 -->
<dependency>
    <groupId>com.github.pagehelper</groupId>
    <artifactId>pagehelper</artifactId>
    <version>4.1.6</version>
</dependency>
<dependency>
    <groupId>com.github.jsqlparser</groupId>
    <artifactId>jsqlparser</artifactId>
    <version>0.9.6</version>
</dependency>
```

### JSP/Servlet相关

```xml
<dependency>					
    <groupId>javax.servlet</groupId>					
    <artifactId>javax.servlet-api</artifactId>					
    <version>3.0.1</version>					
    <scope>provided</scope>					
</dependency>					
<dependency>					
    <groupId>javax.servlet</groupId>					
    <artifactId>jsp-api</artifactId>					
    <version>2.0</version>					
    <scope>provided</scope>
    <exclusions>
        <exclusion>
            <groupId>javax.servlet</groupId>
            <artifactId>servlet-api</artifactId>
        </exclusion>
    </exclusions>					
</dependency>
<dependency>					
    <groupId>jstl</groupId>					
    <artifactId>jstl</artifactId>					
    <version>1.2</version>					
</dependency>					
<dependency>					
    <groupId>taglibs</groupId>			
    <artifactId>standard</artifactId>			
    <version>1.1.2</version>			
</dependency>
```

### Spring MVC 相关

```xml
<!-- spring mvc -->
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-webmvc</artifactId>
    <version>4.3.14.RELEASE</version>
</dependency>
<!-- jackson -->
<dependency>
    <groupId>com.fasterxml.jackson.core</groupId>
    <artifactId>jackson-core</artifactId>
    <version>2.9.8</version>
</dependency>
<dependency>
    <groupId>com.fasterxml.jackson.core</groupId>
    <artifactId>jackson-databind</artifactId>
    <version>2.9.8</version>
</dependency>
```

### 其他依赖

```xml
<!-- fastjson -->
<dependency>					
    <groupId>com.alibaba</groupId>					
    <artifactId>fastjson</artifactId>					
    <version>1.2.47</version>					
</dependency>
```

### 插件

```xml
<!-- java编译插件 -->
<plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-compiler-plugin</artifactId>
    <version>3.2</version>
    <configuration>
        <source>1.8</source>
        <target>1.8</target>
        <encoding>UTF-8</encoding>
    </configuration>
</plugin>

<plugin>		
    <groupId>org.apache.tomcat.maven</groupId>
    <artifactId>tomcat7-maven-plugin</artifactId>
    <version>2.2</version>
    <configuration>
        <path>/1202</path>
        <port>8083</port>
    </configuration>
</plugin>	
```

## 融合Mybatis

### 使用代码生成工具生成实体类和Mapper

使用generatorSqlmapCustom。

### 配置`applicationContext_dao.xml`

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xmlns:aop="http://www.springframework.org/schema/aop"
	xmlns:context="http://www.springframework.org/schema/context"
	xmlns:tx="http://www.springframework.org/schema/tx"
	xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
		http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop-4.3.xsd
		http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context-4.3.xsd
		http://www.springframework.org/schema/tx http://www.springframework.org/schema/tx/spring-tx-4.3.xsd">
	
	<context:annotation-config></context:annotation-config>
	<context:component-scan base-package="com.qf"/>
	
	<!-- 配置數據源  。。。。  c3p0  druid -->
	<bean id="datasource" class="com.mchange.v2.c3p0.ComboPooledDataSource">
		<property name="driverClass" value="com.mysql.jdbc.Driver"/>
		<property name="jdbcUrl" value="jdbc:mysql://localhost:3306/bank?characterEncoding=utf8"/>
		<property name="user" value="root"/>
		<property name="password" value="123"/>
	</bean>
	
	<!-- 配置session 工廠 -->
	<bean id="sessionfactory" class="org.mybatis.spring.SqlSessionFactoryBean">
		<!-- 設置數據源 -->
		<property name="dataSource" ref="datasource"/>
		<!-- 告訴數據源mb。xml wenjian de 位置 -->
		<property name="configLocation" value="classpath:mb.xml"/>
		<!-- sql的音聲文件在哪裡 -->
		<property name="mapperLocations" value="classpath:mapper/*.xml"/>
	</bean>
	
	<!-- 掃描mapper的接口 把mapper 配置成spring de bean -->
	<bean id="mapperscan" class="org.mybatis.spring.mapper.MapperScannerConfigurer">
		<property name="sqlSessionFactory" ref="sessionfactory"/>
		<!-- mapper 的接口去哪裡掃描 -->
		<property name="basePackage" value="com.qf.mapper"/>
	</bean>
	
	<!-- 事物相關 -->
	<!-- 配置事物切面的bean -->
	<bean id="dtm" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
		<property name="dataSource" ref="datasource"/>
	</bean>
	
	<!-- 開啟註解班的事物 -->
	<tx:annotation-driven transaction-manager="dtm"/>
	
</beans>

```

## 写Service层

## 写测试

```java
@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration("classpath:applicationContext_dao.xml")
public class ProvinceServiceTest {
	@Autowired
	private ProvinceService proviceService;
	
	@Test
	public void findAllP() {
		List<Province> list = proviceService.findAll();
		for (Province province : list) {
			System.out.println(province.getPname());
		}
	} 
	
	@Test
	public void findCityTest() {
		List<City> list = proviceService.findCitysByPid(2);
		for (City city : list) {
			System.out.println(city.getCname());
		}
	} 
}

```

## 把Spring MVC融入Spring

在`web.xml` 中配置`DispatherServlet`

`<param-value>classpath:applicationContext_*.xml</param-value>`可以在`servlet`中读取所有的spring配置文件。

```java
	<servlet>
		<servlet-name>springDispatcherServlet</servlet-name>
		<servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
		<init-param>
			<param-name>contextConfigLocation</param-name>
			<param-value>classpath:applicationContext_*.xml</param-value>
		</init-param>
		<load-on-startup>1</load-on-startup>
	</servlet>
		<servlet-mapping>
		<servlet-name>springDispatcherServlet</servlet-name>
		<url-pattern>*.do</url-pattern>
	</servlet-mapping>
```

## 写Controller

```java
@Controller
@RequestMapping("/province")
public class ProvinceController {

	@Autowired
	private ProvinceService provinceService;
	
	@RequestMapping("/findAll.do")
	@ResponseBody
	private List<Province> findAll(){
		return provinceService.findAll();
	}
	
	@RequestMapping("/findCitys.do")
	@ResponseBody
	private List<City> findCitys(Integer pid){
		return provinceService.findCitysByPid(pid);
	}
	
}
```

