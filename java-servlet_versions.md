---
title: "servlet version choose"
description: ""
date: 2023-09-18T08:08:08+08:00
---

- [Servlet API history](https://en.wikipedia.org/wiki/Jakarta_Servlet#History)

|Servlet API version|Released|Specification|Platform|Important Changes|
|-|-|-|-|-|
|Jakarta Servlet 6.0  |May 31, 2022  |6.0    |Jakarta EE 10       |remove deprecated features and implement requested enhancements|
|Jakarta Servlet 5.0  |Oct 9, 2020   |5.0    |Jakarta EE 9        |API moved from package javax.servlet to jakarta.servlet|
|Jakarta Servlet 4.0.3|Sep 10, 2019  |4.0    |Jakarta EE 8        |Renamed from "Java" trademark|
|Java Servlet 4.0     |Sep 2017      |JSR 369|Java EE 8           |HTTP/2|
|Java Servlet 3.1     |May 2013      |JSR 340|Java EE 7           |Non-blocking I/O, HTTP protocol upgrade mechanism (WebSocket)|
|Java Servlet 3.0     |December 2009 |JSR 315|Java EE 6, Java SE 6|Pluggability, Ease of development, Async Servlet, Security, File Uploading|
|Java Servlet 2.5     |September 2005|JSR 154|Java EE 5, Java SE 5|Requires Java SE 5, supports annotation|
|Java Servlet 2.4     |November 2003 |JSR 154|J2EE 1.4, J2SE 1.3  |web.xml uses XML Schema|


## Spring
- v6.1    
6.1.x is the upcoming feature branch ([November 2023](https://calendar.spring.io/)).
- v6.0    
As a major revision of the core framework, Spring Framework 6.0 comes with a Java 17+ baseline and a move to Jakarta EE 9+ (in the jakarta namespace),    
with a focus on the recently released Jakarta EE 10 APIs such as Servlet 6.0 and JPA 3.1.

- v5.3    
5.3.x is the final feature branch of the 5th generation, with long-term support provided on JDK 8, JDK 11, JDK 17 and the Java EE 8 level.

- v4.3    
The 4.3.x line will enjoy an extended support life until 2020, within the general Spring Framework 4 system requirements (JDK 6+, Servlet 2.5+) 
but with a focus on recent servers such as Tomcat 8.5+ and WebSphere 8.5+ on JDK 7 and 8. 

- v2.5.x    
As of Spring 2.5, support for JDK 1.3 has been removed, following Sun's official deprecation of JDK 1.3 in late 2006.    
If you haven't done so already, upgrade to JDK 1.4.2 or higher.


## Repository
- v6.0
```xml
<!-- https://mvnrepository.com/artifact/jakarta.servlet/jakarta.servlet-api -->
<dependency>
    <groupId>jakarta.servlet</groupId>
    <artifactId>jakarta.servlet-api</artifactId>
    <version>6.0.0</version>
    <scope>provided</scope>
</dependency>
```
- v3.1
```xml
<!-- https://mvnrepository.com/artifact/javax.servlet/javax.servlet-api -->
<dependency>
    <groupId>javax.servlet</groupId>
    <artifactId>javax.servlet-api</artifactId>
    <version>3.1.0</version>
    <scope>provided</scope>
</dependency>
```
- v2.5
```xml
<!-- https://mvnrepository.com/artifact/javax.servlet/servlet-api -->
<dependency>
    <groupId>javax.servlet</groupId>
    <artifactId>servlet-api</artifactId>
    <version>2.5</version>
    <scope>provided</scope>
</dependency>
```

## Namespace Declaration and Schema Location
web.xml

- v6.0
```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="https://jakarta.ee/xml/ns/jakartaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="https://jakarta.ee/xml/ns/jakartaee
         web-app_6.0.xsd"
         version="6.0">
</web-app>
```
- v3.1
```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee
                      http://xmlns.jcp.org/xml/ns/javaee/web-app_3_1.xsd"
  version="3.1">
</web-app>
```
- v2.5
```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://java.sun.com/xml/ns/j2ee"
xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
xsi:schemaLocation="http://java.sun.com/xml/ns/j2ee
http://java.sun.com/xml/ns/j2ee/web-app_2_5.xsd”
version=”2.5”>
</web-app>
```


# Ref
- [web.xml deployment descriptor examples](https://mkyong.com/web-development/the-web-xml-deployment-descriptor-examples/)
- [servlet v2.5](https://jcp.org/en/jsr/detail?id=154)
- [servlet v3.1](https://github.com/apache/tomcat/blob/8.5.x/conf/web.xml)
- [servlet v6.0](https://jakarta.ee/specifications/servlet/6.0/jakarta-servlet-spec-6.0.html#a-basic-example)
- [servlet v4.0](https://jcp.org/en/jsr/detail?id=369)
- [servlet v5.0](https://jakarta.ee/specifications/servlet/5.0/jakarta-servlet-spec-5.0.html#a-basic-example)
- [Spring Framework 6.0 goes GA](https://spring.io/blog/2022/11/16/spring-framework-6-0-goes-ga)
- [Spring Framework Versions](https://github.com/spring-projects/spring-framework/wiki/Spring-Framework-Versions)
- [Upgrading to Spring Framework 4.x](https://github.com/spring-projects/spring-framework/wiki/Upgrading-to-Spring-Framework-4.x)
- [2.7.1.1. Supported JDK versions](https://docs.spring.io/spring-framework/docs/2.5.6/reference/new-in-2.html)
- [Changes in version 2.5.6 (2008-10-31)](https://docs.spring.io/spring-framework/docs/2.5.x/changelog.txt)
