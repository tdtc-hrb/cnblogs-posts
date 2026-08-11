---
title: "cas client设置"
description: "cas设置分2篇：cas server、cas client"
date: 2020-05-16T12:08:08+08:00
---
> The client and server are the same [thekeystore](https://xiaobin80.gitee.io/cnblogs/post/cas-server-config/)

cas client version: 3.5.1([cas-client-core-3.5.1](https://github.com/apereo/java-cas-client/tree/master/cas-client-core))
```bash
git clone https://gitee.com/xiaobin80/cas-sample-java-webapp
```

# 1. config
web.xml(src\main\webapp\WEB-INF)

## 1) CAS Single Sign Out Filter
```bash
https://localhost:8443/cas
```

## 2) CAS Authentication Filter
```bash
https://localhost:8443/cas/login
```

## 3) CAS Validation Filter
```bash
https://localhost:8443/cas
```

## 4) server name
```bash
localhost
```

# 2. keystore
  copy
```bash
/etc/cas/jetty/thekeystore
```
   to    

The disk root directory of the application.    
  For example:
  my application:
```bash
D:\projects\cas-sample-java-webapp
```
then    
thekeystore file:
```bash
D:\etc\cas\jetty\thekeystore
```
![win info](https://gitee.com/xiaobin80/cnblogs/raw/master/images/20181113192532190.png)

# 3. build
  alter context path(pom.xml):
```xml
<webApp>
  <contextPath>/loginInfo</contextPath>
  <overrideDescriptor>${basedir}/etc/jetty/web.xml</overrideDescriptor>
</webApp>
```

```bash
mvn clean package jetty:run-forked
```

## 1) Run directly
```bash
mvn jetty:run-forked
```

# 4. test
http://localhost:9080/loginInfo
![browser info](https://gitee.com/xiaobin80/cnblogs/raw/master/images/20190811082322077.png)
![cas info](https://gitee.com/xiaobin80/cnblogs/raw/master/images/20181115204456890.png)

# Source
> web.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app version="2.4" xmlns="http://java.sun.com/xml/ns/j2ee" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://java.sun.com/xml/ns/j2ee http://java.sun.com/xml/ns/j2ee/web-app_2_4.xsd">

<!--
   <context-param>
       <param-name>renew</param-name>
       <param-value>true</param-value>
   </context-param>
-->

    <filter>
        <filter-name>CAS Single Sign Out Filter</filter-name>
        <filter-class>org.jasig.cas.client.session.SingleSignOutFilter</filter-class>
        <init-param>
            <param-name>casServerUrlPrefix</param-name>
            <param-value>https://localhost:8443/cas</param-value>
        </init-param>
    </filter>

    <listener>
        <listener-class>org.jasig.cas.client.session.SingleSignOutHttpSessionListener</listener-class>
    </listener>

    <filter>
        <filter-name>CAS Authentication Filter</filter-name>
        <!--<filter-class>org.jasig.cas.client.authentication.Saml11AuthenticationFilter</filter-class>-->
        <filter-class>org.jasig.cas.client.authentication.AuthenticationFilter</filter-class>
        <init-param>
            <param-name>casServerLoginUrl</param-name>
            <param-value>https://localhost:8443/cas/login</param-value>
        </init-param>
        <init-param>
            <param-name>serverName</param-name>
            <param-value>localhost</param-value>
        </init-param>
    </filter>

    <filter>
        <filter-name>CAS Validation Filter</filter-name>
        <!--<filter-class>org.jasig.cas.client.validation.Saml11TicketValidationFilter</filter-class>-->
        <filter-class>org.jasig.cas.client.validation.Cas30ProxyReceivingTicketValidationFilter</filter-class>
        <init-param>
            <param-name>casServerUrlPrefix</param-name>
            <param-value>https://localhost:8443/cas</param-value>
        </init-param>
        <init-param>
            <param-name>serverName</param-name>
            <param-value>localhost</param-value>
        </init-param>
        <init-param>
            <param-name>redirectAfterValidation</param-name>
            <param-value>true</param-value>
        </init-param>
        <init-param>
            <param-name>useSession</param-name>
            <param-value>true</param-value>
        </init-param>
        <!--
        <init-param>
            <param-name>acceptAnyProxy</param-name>
            <param-value>true</param-value>
        </init-param>
        <init-param>
            <param-name>proxyReceptorUrl</param-name>
            <param-value>/sample/proxyUrl</param-value>
        </init-param>
        <init-param>
            <param-name>proxyCallbackUrl</param-name>
            <param-value>https://mmoayyed.unicon.net:9443/sample/proxyUrl</param-value>
        </init-param>
        -->
        <init-param>
            <param-name>authn_method</param-name>
            <param-value>mfa-duo</param-value>
        </init-param>
    </filter>

    <filter>
        <filter-name>CAS HttpServletRequest Wrapper Filter</filter-name>
        <filter-class>org.jasig.cas.client.util.HttpServletRequestWrapperFilter</filter-class>
    </filter>

    <filter-mapping>
        <filter-name>CAS Single Sign Out Filter</filter-name>
        <url-pattern>/*</url-pattern>
    </filter-mapping>

    <filter-mapping>
        <filter-name>CAS Validation Filter</filter-name>
        <url-pattern>/*</url-pattern>
    </filter-mapping>

    <filter-mapping>
        <filter-name>CAS Authentication Filter</filter-name>
        <url-pattern>/*</url-pattern>
    </filter-mapping>

    <filter-mapping>
        <filter-name>CAS HttpServletRequest Wrapper Filter</filter-name>
        <url-pattern>/*</url-pattern>
    </filter-mapping>

    <welcome-file-list>
        <welcome-file>
            index.jsp
        </welcome-file>
    </welcome-file-list>
</web-app>
```
