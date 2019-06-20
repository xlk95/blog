## Springboot整合Dubbo

本文用一个小Demo来整合Springboot、Dubbo、mybatis。

### 1、首先写一个父工程

父工程的pom工程如下，定义了所需依赖的版本。

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <modules>
        <module>user_provider</module>
    </modules>


    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.1.5.RELEASE</version>
        <relativePath/> <!-- lookup parent from repository -->
    </parent>
    <packaging>pom</packaging>
    <groupId>com.qf</groupId>
    <artifactId>testuser</artifactId>
    <version>1.0-SNAPSHOT</version>
    <properties>
        <java.version>1.8</java.version>
        <mysql.version>5.1.44</mysql.version>
        <mybatis-spring-boot-starter.version>2.0.1</mybatis-spring-boot-starter.version>
        <pagehelper.version>1.2.3</pagehelper.version>
        <druid.version>1.1.0</druid.version>
        <log4j.version>1.2.17</log4j.version>
        <dubbo.version>1.0.0</dubbo.version>
    </properties>

</project>
```

### 2、写Provider

提供者Provider是一个普通的聚合工程，它的模块有model、dao、api、service，下面主要来看一下service的pom文件：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <parent>
        <artifactId>user_provider</artifactId>
        <groupId>com.qf</groupId>
        <version>1.0-SNAPSHOT</version>
    </parent>

    <artifactId>provider_service</artifactId>

    <name>provider_service</name>


    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>

        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <scope>runtime</scope>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
        <dependency>
            <groupId>com.qf</groupId>
            <artifactId>provider_api</artifactId>
            <version>1.0-SNAPSHOT</version>
        </dependency>
        <dependency>
            <groupId>com.qf</groupId>
            <artifactId>provider_dao</artifactId>
            <version>1.0-SNAPSHOT</version>
        </dependency>
        <!--druid  pagehelper log4j logging dubbo-->
        <dependency>
            <groupId>log4j</groupId>
            <artifactId>log4j</artifactId>
            <version>${log4j.version}</version>
        </dependency>
        <dependency>
            <groupId>io.dubbo.springboot</groupId>
            <artifactId>spring-boot-starter-dubbo</artifactId>
            <version>${dubbo.version}</version>
        </dependency>
        <dependency>
            <groupId>com.github.pagehelper</groupId>
            <artifactId>pagehelper-spring-boot-starter</artifactId>
            <version>${pagehelper.version}</version>
        </dependency>
        <dependency>
            <groupId>com.alibaba</groupId>
            <artifactId>druid-spring-boot-starter</artifactId>
            <version>${druid.version}</version>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-logging</artifactId>
        </dependency>
    </dependencies>

    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
        </plugins>
    </build>

</project>

```

#### 2.1 配置文件application.properties

```properties
server.port=8080
##########################  mysql 数据源  ##########################
spring.datasource.url=jdbc:mysql://127.0.0.1:3306/express?characterEncoding=utf-8
spring.datasource.username=root
spring.datasource.password=123
spring.datasource.driver-class-name=com.mysql.jdbc.Driver
# 告诉系统使用的数据源的类型
spring.datasource.type=com.alibaba.druid.pool.DruidDataSource
# 初始化大小，最小，最大
spring.datasource.initialSize=5
spring.datasource.minIdle=5
spring.datasource.maxActive=20
# 配置获取连接等待超时的时间
spring.datasource.maxWait=60000
# 配置间隔多久才进行一次检测，检测需要关闭的空闲连接，单位是毫秒
spring.datasource.timeBetweenEvictionRunsMillis=60000
# 配置一个连接在池中最小生存的时间，单位是毫秒
spring.datasource.minEvictableIdleTimeMillis=300000
# 校验SQL，Oracle配置 spring.datasource.validationQuery=SELECT 1 FROM DUAL，如果不配validationQuery项，则下面三项配置无用
spring.datasource.validationQuery=SELECT '1'
spring.datasource.testWhileIdle=true
spring.datasource.testOnBorrow=false
spring.datasource.testOnReturn=false
# 打开PSCache，并且指定每个连接上PSCache的大小
spring.datasource.poolPreparedStatements=true
spring.datasource.maxPoolPreparedStatementPerConnectionSize=20
# 配置监控统计拦截的filters，去掉后监控界面sql无法统计，'wall'用于防火墙
spring.datasource.filters=stat,wall,log4j
# 通过connectProperties属性来打开mergeSql功能；慢SQL记录
spring.datasource.connectionProperties=druid.stat.mergeSql=true;druid.stat.slowSqlMillis=1
# 合并多个DruidDataSource的监控数据
spring.datasource.useGlobalDataSourceStat=true

##########################  mybatis   ##########################
mybatis.mapper-locations=classpath:mapping/*.xml
pagehelper.helperDialect=mysql
pagehelper.reasonable=true
pagehelper.supportMethodsArguments=true
pagehelper.params=count=countSql
##########################log#################################
logging.level.com.qf.testuser.mapper=debug
##########################dubbo################################
# 配置dubbo
# 这个注册到注册中心的名字
spring.dubbo.application.name=user_provider
# 注册中心的地址  2181 是zookeeper的默认端口
spring.dubbo.registry.address=zookeeper://192.168.25.128:2181
# 协议是dubbo
spring.dubbo.protocol.name=dubbo
# 提供服务真实的端口 服务在哪里运行 就是ip地址
spring.dubbo.protocol.port=20880
# 去哪里扫面 阿里巴巴@Service 注解 扫到一个这个的注解 意味这个service 要要注册的
#spring.dubbo.scan=com.qf.testuser.service.impl


```



**注意**

- 这个domo的Druid需要高版本log4j的支持，还需要写一个配置类来注入Druid数据源。

```java
package com.qf.testuser.service.myconfig;

import com.alibaba.druid.pool.DruidDataSource;
import org.springframework.boot.context.properties.ConfigurationProperties;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

import javax.sql.DataSource;

@Configuration
public class DSconfig {

    @Bean
    @ConfigurationProperties(prefix = "spring.datasource")
    public DataSource getDataSource(){
        return new DruidDataSource();
    }
}

```

- 整合mybatis要在主程序中扫描Mapper 需要加`@MapperScan("com.qf.testuser.mapper")`
- `@Service` 使用阿里巴巴的

这时候就可以把provider跑起来了，在Dubbo控制台可以看到提供者的信息。

### 3、写消费者

消费者需要依赖提供者的接口、Dubbo和log4j，依赖如下。

```xml
    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-thymeleaf</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
        <dependency>
            <groupId>com.qf</groupId>
            <artifactId>provider_api</artifactId>
            <version>1.0-SNAPSHOT</version>
        </dependency>
        <dependency>
            <groupId>log4j</groupId>
            <artifactId>log4j</artifactId>
            <version>${log4j.version}</version>
        </dependency>
        <dependency>
            <groupId>io.dubbo.springboot</groupId>
            <artifactId>spring-boot-starter-dubbo</artifactId>
            <version>${dubbo.version}</version>
        </dependency>
    </dependencies>
```

#### 3.1写Controller

在Controller中应该使用`@Reference`引用提供者，而不是`@Autowired`注入，代码如下。

```
public class UserController {
    @Reference
    private UserService userService;b
    @GetMapping("/tolist")
    public String findusers(ModelMap map){
        List<Users> usersList = userService.findall();
        map.put("users",usersList);
        return "list";
    }
}
```

#### 3.2配置文件

```

#dubbo
spring.dubbo.application.name=user_web
spring.dubbo.registry.address=zookeeper://192.168.25.128:2181
spring.dubbo.scan=com.qf

########################################################
###THYMELEAF (ThymeleafAutoConfiguration)
#  。html（伪静态化）
########################################################
#spring.thymeleaf.prefix=classpath:/templates/
spring.thymeleaf.suffix=.html
#spring.thymeleaf.mode=HTML5
#spring.thymeleaf.encoding=UTF-8
# ;charset=<encoding> is added
#spring.thymeleaf.content-type=text/html
# set to false for hot refresh
# 是否使用页面缓存(是) 开发的时候设置为false
spring.thymeleaf.cache=false

#port
server.port=8081

```

### 以上代码的坑

- Dubbo采用的协议是Dubbo协议，它不能跨语言，并且采用io流进行传输，所以实体类必须实现`Serializable`接口。
- 由于在Service中使用的是Dubbo的注解，会导致事务失效，下面提供一种解决方案。

首先把`@Service`换成Spring的，再写一个Dubbo的配置文件。

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:dubbo="http://code.alibabatech.com/schema/dubbo"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
	http://www.springframework.org/schema/beans/spring-beans-4.2.xsd
	http://code.alibabatech.com/schema/dubbo
	http://code.alibabatech.com/schema/dubbo/dubbo.xsd">

    <!-- 声明需要暴露的服务接口 -->

    <dubbo:service interface="com.qf.testuser.service.UserService" ref="userServiceImpl" timeout="600000"/>


</beans>
```

然后在提供者运行主程序加上`@ImportResource("classpath*:applocationContext_dubbo.xml")`读取此文件即可。

此时一个整合Springboot+Dubbo+Mybatis+Druid的Demo就完成了，enjoy it!

