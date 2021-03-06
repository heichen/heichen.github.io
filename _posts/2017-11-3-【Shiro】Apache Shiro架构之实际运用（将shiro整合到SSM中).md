﻿---
layout: post
title:  "【Shiro】Apache Shiro架构之实际运用（将shiro整合到SSM中)"
categories: java
tags:  ssm java
author: DarkChen
---

* content
{:toc}

其实Shiro整合到Spring中并没有想象的那么困难，整到Spring中后，我们自定义的realm啊、securityManager啊等等都会交给spring去管理了，包括我们需要指定哪些url需要做什么样的验证，都是交给spring，也就是说，完全可以摆脱原来的那个.ini配置文件了，会觉得非常清爽，非常有逻辑。本此整合采用maven进行包管理，下面开始整合，并用登录操作来验证是否整合成功！




## 配置文件

既然是用maven做的整合那首先来看一下pom.xml

### pom.xml
```java
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>FFMS</groupId>
    <artifactId>FFMS</artifactId>
    <version>1.0-SNAPSHOT</version>
    <properties>
        <!-- spring版本号 -->
        <spring.version>4.3.9.RELEASE</spring.version>
        <!-- mybatis版本号 -->
        <mybatis.version>3.4.3</mybatis.version>
        <!-- log4j日志文件管理包版本 -->
        <slf4j.version>1.7.7</slf4j.version>
        <log4j.version>1.2.17</log4j.version>
        <shiro.version>1.2.3</shiro.version>
        <maven.compiler.source>1.8</maven.compiler.source>
        <maven.compiler.target>1.8</maven.compiler.target>
    </properties>

    <build>
        <finalName>FFMS</finalName>
    </build>

    <dependencies>

        <!-- https://mvnrepository.com/artifact/junit/junit -->
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.12</version>

        </dependency>

        <!-- shiro -->
        <dependency>
            <groupId>org.apache.shiro</groupId>
            <artifactId>shiro-spring</artifactId>
            <version>${shiro.version}</version>
        </dependency>
        <dependency>
            <groupId>org.apache.shiro</groupId>
            <artifactId>shiro-ehcache</artifactId>
            <version>${shiro.version}</version>
        </dependency>
        <dependency>
            <groupId>org.apache.shiro</groupId>
            <artifactId>shiro-core</artifactId>
            <version>${shiro.version}</version>
        </dependency>
        <dependency>
            <groupId>org.apache.shiro</groupId>
            <artifactId>shiro-web</artifactId>
            <version>${shiro.version}</version>
        </dependency>
        <dependency>
            <groupId>org.apache.shiro</groupId>
            <artifactId>shiro-quartz</artifactId>
            <version>${shiro.version}</version>
        </dependency>

        <!-- 导入dbcp的jar包，用来在applicationContext.xml中配置数据库 -->
        <!-- https://mvnrepository.com/artifact/org.apache.commons/commons-dbcp2 -->
        <dependency>
            <groupId>org.apache.commons</groupId>
            <artifactId>commons-dbcp2</artifactId>
            <version>2.1.1</version>
        </dependency>
        <!-- spring核心包 -->
        <!-- https://mvnrepository.com/artifact/aopalliance/aopalliance -->
        <dependency>
            <groupId>aopalliance</groupId>
            <artifactId>aopalliance</artifactId>
            <version>1.0</version>
        </dependency>
        <!-- https://mvnrepository.com/artifact/org.aspectj/aspectjrt -->
        <dependency>
            <groupId>org.aspectj</groupId>
            <artifactId>aspectjrt</artifactId>
            <version>1.8.10</version>
        </dependency>
        <!-- https://mvnrepository.com/artifact/org.aspectj/aspectjweaver -->
        <dependency>
            <groupId>org.aspectj</groupId>
            <artifactId>aspectjweaver</artifactId>
            <version>1.8.10</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-instrument</artifactId>
            <version>${spring.version}</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-instrument-tomcat</artifactId>
            <version>${spring.version}</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-core</artifactId>
            <version>${spring.version}</version>
        </dependency>


        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-web</artifactId>
            <version>${spring.version}</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-oxm</artifactId>
            <version>${spring.version}</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-tx</artifactId>
            <version>${spring.version}</version>
        </dependency>


        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-jdbc</artifactId>
            <version>${spring.version}</version>
        </dependency>


        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-webmvc</artifactId>
            <version>${spring.version}</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-aop</artifactId>
            <version>${spring.version}</version>
        </dependency>


        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-context-support</artifactId>
            <version>${spring.version}</version>
        </dependency>


        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-test</artifactId>
            <version>${spring.version}</version>
        </dependency>
        <!-- https://mvnrepository.com/artifact/org.springframework/spring-aspects -->
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-aspects</artifactId>
            <version>${spring.version}</version>
        </dependency>
        <!-- https://mvnrepository.com/artifact/junit/junit -->
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-webmvc-portlet</artifactId>
            <version>${spring.version}</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-websocket</artifactId>
            <version>${spring.version}</version>
        </dependency>
        <!-- https://mvnrepository.com/artifact/org.mybatis/mybatis -->
        <dependency>
            <groupId>org.mybatis</groupId>
            <artifactId>mybatis</artifactId>
            <version>${mybatis.version}</version>
        </dependency>
        <!-- mybatis/spring包 -->
        <dependency>
            <groupId>org.mybatis</groupId>
            <artifactId>mybatis-spring</artifactId>
            <version>1.3.1</version>
        </dependency>

        <dependency>
            <groupId>com.github.pagehelper</groupId>
            <artifactId>pagehelper</artifactId>
            <version>5.0.3</version>
        </dependency>

        <!-- 导入Mysql数据库链接jar包 -->
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>5.1.44</version>
        </dependency>

        <dependency>
            <groupId>log4j</groupId>
            <artifactId>log4j</artifactId>
            <version>${log4j.version}</version>
        </dependency>
        <dependency>
            <groupId>org.slf4j</groupId>
            <artifactId>slf4j-api</artifactId>
            <version>${slf4j.version}</version>
        </dependency>
        <dependency>
            <groupId>org.slf4j</groupId>
            <artifactId>slf4j-log4j12</artifactId>
            <version>${slf4j.version}</version>
        </dependency>

        <dependency>
            <groupId>commons-fileupload</groupId>
            <artifactId>commons-fileupload</artifactId>
            <version>1.2.1</version>
        </dependency>

        <!-- https://mvnrepository.com/artifact/commons-io/commons-io -->
        <dependency>
            <groupId>commons-io</groupId>
            <artifactId>commons-io</artifactId>
            <version>2.5</version>
        </dependency>
        <dependency>
            <groupId>com.fasterxml.jackson.core</groupId>
            <artifactId>jackson-core</artifactId>
            <version>2.8.9</version>
        </dependency>
        <dependency>
            <groupId>com.fasterxml.jackson.core</groupId>
            <artifactId>jackson-annotations</artifactId>
            <version>2.8.9</version>
        </dependency>

        <dependency>
            <groupId>com.fasterxml.jackson.core</groupId>
            <artifactId>jackson-databind</artifactId>
            <version>2.8.9</version>
        </dependency>

        <dependency>
            <groupId>org.codehaus.jackson</groupId>
            <artifactId>jackson-mapper-asl</artifactId>
            <version>1.9.13</version>
        </dependency>

        <dependency>
            <groupId>javax.servlet</groupId>
            <artifactId>javax.servlet-api</artifactId>
            <version>4.0.0-b07</version>
            <scope>provided</scope>
        </dependency>
        <dependency>
            <groupId>org.freemarker</groupId>
            <artifactId>freemarker</artifactId>
            <version>2.3.22</version>
        </dependency>
    </dependencies>

</project>

```
通过maven引入相应的依赖包后，便可以进行相应的Spring和shiro的配置

### applicationCotext.xml

```java
<?xml version="1.0" encoding="UTF-8" ?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:tx="http://www.springframework.org/schema/tx" xmlns:mvc="http://www.springframework.org/schema/mvc"
       xsi:schemaLocation="http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop-4.0.xsd
        http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans-4.0.xsd
        http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context-4.0.xsd
        http://www.springframework.org/schema/jee http://www.springframework.org/schema/jee/spring-jee-4.0.xsd
        http://www.springframework.org/schema/tx http://www.springframework.org/schema/tx/spring-tx-4.0.xsd http://www.springframework.org/schema/mvc http://www.springframework.org/schema/mvc/spring-mvc.xsd">

    <tx:annotation-driven/>
    <context:component-scan base-package="com.FFMS.service"/>
    <bean
            class="org.mybatis.spring.mapper.MapperScannerConfigurer">
        <property name="basePackage" value="com.FFMS.mapper"/>
    </bean>

    <!-- 配置数据源 -->
    <bean id="dataSource"
          class="org.springframework.jdbc.datasource.DriverManagerDataSource">
        <property name="driverClassName" value="com.mysql.jdbc.Driver"/>
        <property name="url" value="jdbc:mysql://localhost:3306/shiro"/>
        <property name="username" value="root"/>
        <property name="password" value="123456"/>
    </bean>

    <!-- 配置mybatis的sqlSessionFactory -->
    <bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
        <property name="dataSource" ref="dataSource" />
        <!-- 自动扫描mappers.xml文件 -->
        <property name="mapperLocations" value="classpath:/mappers/*.xml"/>
        <!-- mybatis配置文件 -->
        <property name="configLocation" value="classpath:mybatis-config.xml"/>
    </bean>

    <!-- DAO接口所在包名，Spring会自动查找其下的类 -->
    <bean class="org.mybatis.spring.mapper.MapperScannerConfigurer">
        <property name="basePackage" value="com.FFMS.mapper" />
        <property name="sqlSessionFactoryBeanName" value="sqlSessionFactory"/>
    </bean>

    <!--(事务管理)transaction manager, use JtaTransactionManager for global tx-->
    <bean id="transactionManager"
          class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
        <property name="dataSource" ref="dataSource" />
    </bean>

    <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver">
        <property name="prefix" value="/WEB-INF/page/" />
        <property name="suffix" value=".html" />
    </bean>
</beans>

```
### spring-mvc.xml

```java
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:context="http://www.springframework.org/schema/context"
       xmlns:mvc="http://www.springframework.org/schema/mvc"
       xsi:schemaLocation="
              http://www.springframework.org/schema/mvc
              http://www.springframework.org/schema/mvc/spring-mvc-3.0.xsd
              http://www.springframework.org/schema/beans
              http://www.springframework.org/schema/beans/spring-beans-3.0.xsd
              http://www.springframework.org/schema/context
              http://www.springframework.org/schema/context/spring-context-3.0.xsd">

    <!-- 配置静态文件-->
    <mvc:default-servlet-handler/>

    <!-- 扫描所有的 controller -->
    <context:component-scan base-package="com.FFMS.controller" />

    <!-- 启动注解驱动 SpringMVC 功能 -->
    <mvc:annotation-driven />

    <!--避免IE执行AJAX时，返回JSON出现下载文件 -->
    <bean id="jsonMapping"
          class="org.springframework.http.converter.json.MappingJackson2HttpMessageConverter">
        <property name="supportedMediaTypes">
            <list>
                <value>text/html;charset=gbk</value>
            </list>
        </property>
    </bean>

    <!-- 配置文件上传，如果没有使用文件上传可以不用配置，当然如果不配，那么配置文件中也不必引入上传组件包 -->
    <bean id="multipartResolver"
          class="org.springframework.web.multipart.commons.CommonsMultipartResolver">
        <!-- 默认编码 -->
        <property name="defaultEncoding" value="gbk" />
        <!-- 文件大小最大值 -->
        <property name="maxUploadSize" value="10485760000" />
        <!-- 内存中的最大值 -->
        <property name="maxInMemorySize" value="40960" />
    </bean>

    <!-- shiro为集成springMvc 拦截异常，使用注解时无权限的跳转 -->
    <bean
            class="org.springframework.web.servlet.handler.SimpleMappingExceptionResolver">
        <property name="exceptionMappings">
            <props>
                <!-- 这里你可以根据需要定义N多个错误异常转发 -->
                <prop key="org.apache.shiro.authz.UnauthorizedException">redirect:/unauthorized</prop>
                <prop key="org.apache.shiro.authz.UnauthenticatedException">redirect:/unauthorized</prop>
                <prop key="java.lang.IllegalArgumentException">/error</prop>  <!-- 参数错误(bizError.jsp) -->
                <prop key="java.lang.Exception">/error</prop>  <!-- 其他错误为'未定义错误'(unknowError.jsp) -->
            </props>
        </property>
    </bean>

</beans>

```

### spring-shiro.xml

```java
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns="http://www.springframework.org/schema/beans" xmlns:util="http://www.springframework.org/schema/util"
       xmlns:context="http://www.springframework.org/schema/context" xmlns:p="http://www.springframework.org/schema/p"
       xmlns:tx="http://www.springframework.org/schema/tx" xmlns:mvc="http://www.springframework.org/schema/mvc"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
    http://www.springframework.org/schema/beans/spring-beans-4.0.xsd http://www.springframework.org/schema/tx
    http://www.springframework.org/schema/tx/spring-tx-4.0.xsd http://www.springframework.org/schema/context
    http://www.springframework.org/schema/context/spring-context-4.0.xsd http://www.springframework.org/schema/mvc
    http://www.springframework.org/schema/mvc/spring-mvc.xsd http://www.springframework.org/schema/aop
    http://www.springframework.org/schema/aop/spring-aop-4.0.xsd http://www.springframework.org/schema/util
    http://www.springframework.org/schema/util/spring-util.xsd">

    <!-- 配置shiro的过滤器工厂类，id- shiroFilter要和我们在web.xml中配置的过滤器一致 -->
    <bean id="shiroFilter" class="org.apache.shiro.spring.web.ShiroFilterFactoryBean">
        <!-- 调用我们配置的权限管理器 -->
        <property name="securityManager" ref="securityManager" />
        <!-- 配置我们的登录请求地址 -->
        <property name="loginUrl" value="/login.html" />
        <!-- 配置我们在登录页登录成功后的跳转地址，如果你访问的是非/login地址，则跳到您访问的地址 -->
        <property name="successUrl" value="/success.jsp" />
        <!-- 如果您请求的资源不再您的权限范围，则跳转到/403请求地址 -->
        <property name="unauthorizedUrl" value="/unauthorized.jsp" />
        <property name="filters">
            <util:map>
                <entry key="logout" value-ref="logoutFilter" />
            </util:map>
        </property>
        <!-- 权限配置 -->
        <property name="filterChainDefinitions">
            <value>
                 <!--anon表示此地址不需要任何权限即可访问 -->
                /login=anon
                /icon/**=anon
                /js/**=anon
                /logout=logout
                <!--所有的请求(除去配置的静态资源请求或请求地址为anon的请求)都要通过登录验证,如果未登录则跳到/login -->
                /** = anon
            </value>
        </property>
    </bean>
    <bean id="logoutFilter" class="org.apache.shiro.web.filter.authc.LogoutFilter">
        <property name="redirectUrl" value="/login.html" />
    </bean>

    <!-- 凭证匹配器 -->
    <!--<bean id="passwordMatcher" class="org.apache.shiro.authc.credential.PasswordMatcher">-->
        <!--<property name="passwordService" ref="passwordService" />-->
    <!--</bean>-->
    <!--<bean id="passwordService"-->
          <!--class="org.apache.shiro.authc.credential.DefaultPasswordService">-->
        <!--<property name="hashService" ref="hashService"/>-->
        <!--<property name="hashFormat" ref="hashFormat"/>-->
        <!--<property name="hashFormatFactory" ref="hashFormatFactory"/>-->
    <!--</bean>-->
    <!--<bean id="hashService" class="org.apache.shiro.crypto.hash.DefaultHashService"/>-->
    <!--<bean id="hashFormat" class="org.apache.shiro.crypto.hash.format.Shiro1CryptFormat"/>-->
    <!--<bean id="hashFormatFactory"-->
          <!--class="org.apache.shiro.crypto.hash.format.DefaultHashFormatFactory">-->
    <!--</bean>-->

    <!-- 会话ID生成器 -->
    <bean id="sessionIdGenerator"
          class="org.apache.shiro.session.mgt.eis.JavaUuidSessionIdGenerator" />
    <!-- 会话Cookie模板 关闭浏览器立即失效 -->
    <bean id="sessionIdCookie" class="org.apache.shiro.web.servlet.SimpleCookie">
        <constructor-arg value="sid" />
        <property name="httpOnly" value="true" />
        <property name="maxAge" value="-1" />
    </bean>
    <!-- 会话DAO -->
    <bean id="sessionDAO"
          class="org.apache.shiro.session.mgt.eis.EnterpriseCacheSessionDAO">
        <property name="sessionIdGenerator" ref="sessionIdGenerator" />
    </bean>
    <!-- 会话验证调度器，每30分钟执行一次验证 ，设定会话超时及保存 -->
    <bean name="sessionValidationScheduler"
          class="org.apache.shiro.session.mgt.ExecutorServiceSessionValidationScheduler">
        <property name="interval" value="1800000" />
        <property name="sessionManager" ref="sessionManager" />
    </bean>
    <!-- 会话管理器 -->
    <bean id="sessionManager"
          class="org.apache.shiro.web.session.mgt.DefaultWebSessionManager">
        <!-- 全局会话超时时间（单位毫秒），默认30分钟 -->
        <property name="globalSessionTimeout" value="1800000" />
        <property name="deleteInvalidSessions" value="true" />
        <property name="sessionValidationSchedulerEnabled" value="true" />
        <property name="sessionValidationScheduler" ref="sessionValidationScheduler" />
        <property name="sessionDAO" ref="sessionDAO" />
        <property name="sessionIdCookieEnabled" value="true" />
        <property name="sessionIdCookie" ref="sessionIdCookie" />
    </bean>

    <!-- 安全管理器 -->
    <bean id="securityManager" class="org.apache.shiro.web.mgt.DefaultWebSecurityManager">
        <property name="realm" ref="userRealm" />
        <!-- 使用下面配置的缓存管理器 -->
        <property name="cacheManager" ref="cacheManager" />
        <property name="sessionManager" ref="sessionManager" />
    </bean>

    <!--启用shiro注解 -->
    <bean
            class="org.springframework.aop.framework.autoproxy.DefaultAdvisorAutoProxyCreator"
            depends-on="lifecycleBeanPostProcessor">
        <property name="proxyTargetClass" value="true" />
    </bean>
    <bean
            class="org.apache.shiro.spring.security.interceptor.AuthorizationAttributeSourceAdvisor">
        <property name="securityManager" ref="securityManager" />
    </bean>

    <!-- 相当于调用SecurityUtils.setSecurityManager(securityManager) -->
    <bean
            class="org.springframework.beans.factory.config.MethodInvokingFactoryBean">
        <property name="staticMethod"
                  value="org.apache.shiro.SecurityUtils.setSecurityManager" />
        <property name="arguments" ref="securityManager" />
    </bean>

    <!-- 注册自定义的Realm，并把密码匹配器注入，使用注解的方式自动注解会无法正确匹配密码 -->
    <bean id="userRealm" class="com.FFMS.shiro.CustomRealm">
        <!--<property name="credentialsMatcher" ref="passwordMatcher"/>-->
        <property name="cachingEnabled" value="false"/>
    </bean>

    <bean id="cacheManager" class="org.apache.shiro.cache.MemoryConstrainedCacheManager" />
    <!-- 保证实现了Shiro内部lifecycle函数的bean执行 -->
    <bean id="lifecycleBeanPostProcessor" class="org.apache.shiro.spring.LifecycleBeanPostProcessor" />
</beans>

```
### mybatis-config.xml

```java
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
    <!-- 别名 -->
    <typeAliases>
        <package name="demo.entity"/>
    </typeAliases>
</configuration>

```
### log4j.properties

```properties
log4j.rootLogger=DEBUG, Console

#Console
log4j.appender.Console=org.apache.log4j.ConsoleAppender
log4j.appender.Console.layout=org.apache.log4j.PatternLayout
log4j.appender.Console.layout.ConversionPattern=%d [%t] %-5p [%c] - %m%n

log4j.logger.java.sql.ResultSet=INFO
log4j.logger.org.apache=INFO
log4j.logger.java.sql.Connection=DEBUG
log4j.logger.java.sql.Statement=DEBUG
log4j.logger.java.sql.PreparedStatement=DEBUG

```
## 代码

编写完配置文件之后，下面通过一个登录的操作检验一下整合的是否有问题。

首先建一个UserController类

### UserController.java

```java
package com.FFMS.controller;
        import com.FFMS.po.ActiveUser;
        import com.FFMS.util.Md5Utils;
        import org.apache.shiro.SecurityUtils;
        import org.apache.shiro.authc.UsernamePasswordToken;
        import org.apache.shiro.authz.annotation.RequiresPermissions;
        import org.apache.shiro.subject.Subject;
        import org.springframework.stereotype.Controller;
        import org.springframework.ui.Model;
        import org.springframework.web.bind.annotation.RequestMapping;
        import org.springframework.web.bind.annotation.ResponseBody;
        import javax.servlet.http.HttpServletRequest;

/**
 * User:heichen
 * Date:2017/11/3
 * Time:20:38
 **/
@Controller
public class UserController {

    //用户登录
    @RequestMapping("/login.form")
    public String login(HttpServletRequest request,Model model) {
        System.out.println("login.form");
        String username=request.getParameter("username");
        String password= Md5Utils.MD5(request.getParameter("password"));
        System.out.println("密码加密前："+request.getParameter("password"));
        System.out.println("密码加密后："+password);
        Subject subject = SecurityUtils.getSubject();
        UsernamePasswordToken token = new UsernamePasswordToken(username, password);
        try{
            subject.login(token);//会跳到我们自定义的realm中
            //request.getSession().setAttribute("user", user);
            //取身份信息
            ActiveUser activeUser = (ActiveUser) subject.getPrincipal();
            //通过model传到页面
            model.addAttribute("activeUser", activeUser);
            return "index";
        }catch(Exception e){
            e.printStackTrace();
            //request.getSession().setAttribute("user", user);
            request.setAttribute("error", "用户名或密码错误！");
            return "login";
        }
    }
}

```
然后是自定义Realm

### CustomRealm.java

```java
package com.FFMS.shiro;

import com.FFMS.po.ActiveUser;
import com.FFMS.po.SysPermission;
import com.FFMS.po.SysUser;
import com.FFMS.service.SysService;
import org.apache.shiro.SecurityUtils;
import org.apache.shiro.authc.AuthenticationException;
import org.apache.shiro.authc.AuthenticationInfo;
import org.apache.shiro.authc.AuthenticationToken;
import org.apache.shiro.authc.SimpleAuthenticationInfo;
import org.apache.shiro.authz.AuthorizationInfo;
import org.apache.shiro.authz.SimpleAuthorizationInfo;
import org.apache.shiro.realm.AuthorizingRealm;
import org.apache.shiro.subject.PrincipalCollection;
import org.apache.shiro.util.ByteSource;
import org.springframework.beans.factory.annotation.Autowired;

import java.util.ArrayList;
import java.util.List;

public class CustomRealm extends AuthorizingRealm {
	
	//注入service
	@Autowired
	private SysService sysService;

	// 设置realm的名称
	@Override
	public void setName(String name) {
		super.setName("customRealm");
	}

	// 用于认证
	//没有连接数据库的方法
	//realm的认证方法，从数据库查询用户信息
	@Override
	protected AuthenticationInfo doGetAuthenticationInfo(
			AuthenticationToken token) throws AuthenticationException {
		System.out.println("认证++++++doGetAuthenticationInfo++++++");
		// token是用户输入的用户名和密码 
		// 第一步从token中取出用户名
		String userCode = (String) token.getPrincipal();

		// 第二步：根据用户输入的userCode从数据库查询
		SysUser sysUser = null;
		try {
			sysUser = sysService.findSysUserByUserCode(userCode);
		} catch (Exception e1) {
			// TODO Auto-generated catch block
			e1.printStackTrace();
		}

		// 如果查询不到返回null
		if(sysUser==null){//
			return null;
		}
		// 从数据库查询到密码
		String password = sysUser.getPassword();
		System.out.println("password:"+password);
		
		//盐
		String salt = sysUser.getSalt();

		// 如果查询到返回认证信息AuthenticationInfo
		
		//activeUser就是用户身份信息
		ActiveUser activeUser = new ActiveUser();
		
		activeUser.setUserid(sysUser.getId());
		activeUser.setUsercode(sysUser.getUsercode());
		activeUser.setUsername(sysUser.getUsername());
		//..
		
		//根据用户id取出菜单
		List<SysPermission> menus  = null;
		try {
			//通过service取出菜单 
			menus = sysService.findMenuListByUserId(sysUser.getId());
		} catch (Exception e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		}
		//将用户菜单 设置到activeUser
		activeUser.setMenus(menus);

		//将activeUser设置simpleAuthenticationInfo
		SimpleAuthenticationInfo simpleAuthenticationInfo = new SimpleAuthenticationInfo(
				activeUser, password,ByteSource.Util.bytes(salt), this.getName());

		return simpleAuthenticationInfo;
	}

	// 用于授权
	@Override
	protected AuthorizationInfo doGetAuthorizationInfo(
			PrincipalCollection principals) {
		System.out.println("授权++++++doGetAuthorizationInfo++++++");
		//从 principals获取主身份信息
		//将getPrimaryPrincipal方法返回值转为真实身份类型（在上边的doGetAuthenticationInfo认证通过填充到SimpleAuthenticationInfo中身份类型），
		ActiveUser activeUser =  (ActiveUser) principals.getPrimaryPrincipal();
		
		//根据身份信息获取权限信息
		//从数据库获取到权限数据
		List<SysPermission> permissionList = null;
		try {
			permissionList = sysService.findPermissionListByUserId(activeUser.getUserid());

			System.out.println("拥有权限："+permissionList);
		} catch (Exception e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		}
		//单独定一个集合对象
		List<String> permissions = new ArrayList<String>();
		if(permissionList!=null){
			for(SysPermission sysPermission:permissionList){
				//将数据库中的权限标签 符放入集合
				permissions.add(sysPermission.getPercode());
			}
		}
		
//		List<String> permissions = new ArrayList<String>();
//		permissions.add("user:create");//用户的创建
//		permissions.add("user:query");//商品查询权限
//		permissions.add("item:add");//商品添加权限
//		permissions.add("item:edit");//商品修改权限
		//....
		
		//查到权限数据，返回授权信息(要包括 上边的permissions)
		SimpleAuthorizationInfo simpleAuthorizationInfo = new SimpleAuthorizationInfo();
		//将上边查询到授权信息填充到simpleAuthorizationInfo对象中
		simpleAuthorizationInfo.addStringPermissions(permissions);

		return simpleAuthorizationInfo;
	}
	
	//清除缓存
	public void clearCached() {
		PrincipalCollection principals = SecurityUtils.getSubject().getPrincipals();
		super.clearCache(principals);
	}
}

```
最后别忘记添加一个MD5的算法工具类（加密就靠他了）

### Md5Utils.java

```java
package com.FFMS.util;

import java.security.MessageDigest;

/**
 * User:heichen
 * Date:2017/11/3
 * Time:14:28
 **/
public class Md5Utils {

    public final static String MD5(String s) {
        char hexDigits[]={'0','1','2','3','4','5','6','7','8','9','A','B','C','D','E','F'};
        try {
            byte[] btInput = s.getBytes();
            // 获得MD5摘要算法的 MessageDigest 对象
            MessageDigest mdInst = MessageDigest.getInstance("MD5");
            // 使用指定的字节更新摘要
            mdInst.update(btInput);
            // 获得密文
            byte[] md = mdInst.digest();
            // 把密文转换成十六进制的字符串形式
            int j = md.length;
            char str[] = new char[j * 2];
            int k = 0;
            for (int i = 0; i < j; i++) {
                byte byte0 = md[i];
                str[k++] = hexDigits[byte0 >>> 4 & 0xf];
                str[k++] = hexDigits[byte0 & 0xf];
            }
            return new String(str);
        } catch (Exception e) {
            e.printStackTrace();
            return null;
        }
    }
}

```	
## 成功

