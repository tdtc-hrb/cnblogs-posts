---
title: "cas server设置"
description: "cas设置分2篇：cas server、cas client"
date: 2020-05-16T10:08:08+08:00
---
- Tomcat: V8.5.x
- Java: 1.8 x64
- MySQL: 5.5.x+
- OS: Win10 x64

# 下载部署包
> 使用gradle 的repo最高只支持gradle V4.3！

## 0. clone
```bash
git clone https://github.com/apereo/cas-gradle-overlay-template.git
```

## 1. set gradle
### 1) extract
[gradle-4.10.3-bin.zip](https://gradle.org/next-steps/?version=4.10.3&format=bin)

### 2) set system variable
```bash
  $PATH=$PATH;%GRADLE_HOME%\bin
```

## 2. build war
> File location: cas\build\libs\cas.war

### 1) config file
gradle.properties
```bash
cas.version=5.3.2
=>
cas.version=5.3.15.1

gradle.version=4.8
=>
gradle.version=4.10.3

springboot.version=1.5.14.RELEASE
=>
springboot.version=1.5.18.RELEASE
```
### 2) build
```bash
cd cas-gradle-overlay-template
$gradle build
```

# II. Run

## 0. key/certificate

### 1) genkey
> info:
-dname "CN=localhost,OU=test.com,O=test,L=WH,ST=HB,C=CN"

```bash
keytool -genkey -keyalg RSA -alias thekeystore -keypass changeit -storepass changeit -keysize 2048 -keystore e:/etc/cas/thekeystore
```

### 2) export
```bash
keytool -export -alias thekeystore -file e:/etc/cas/cas5311.crt -keystore e:/etc/cas/thekeystore -storepass changeit
```

### 3) import
```bash
keytool -import -alias thekeystore -storepass changeit -file e:/etc/cas/cas5311.crt -keystore "%JAVA_HOME%\jre\lib\security\cacerts"
```

## 1. set tomcat
### 1) thekeystore
  copy thekeystore file to $TOMCAT_HOME/conf

### 2) add https
server.xml
```bash
<Connector port="8443" protocol="org.apache.coyote.http11.Http11NioProtocol"
               maxThreads="150" SSLEnabled="true">
  <SSLHostConfig>
    <Certificate certificateKeystoreFile="conf/thekeystore"
              keystorePass="changeit"
              type="RSA" />
    </SSLHostConfig>
</Connector>
```

## 2. let's it
### 1) tomcat startup
```bash
startup.bat
```

### 2) login
http://localhost:8080/cas    
https://localhost:8443/cas

Enter username and password
```bash
casuser/Mellon
```

If you want to change the user/password, stop Tomcat and change it in Application.properties(webapps\cas\WEB-INF\classes).

```bash
##
# CAS Authentication Credentials
#
cas.authn.accept.users=casuser::Mellon
```
![cas info](https://gitee.com/xiaobin80/cnblogs/raw/master/images/20181027044736986.png)

---

# III. Database Authentication

## 0. config files folder
  copy cas-gradle-overlay-template/etc to The drive letter where Tomcat is located.

For example：
```bash
e:\
```
![win info1](https://gitee.com/xiaobin80/cnblogs/raw/master/images/20181113041752923.png)
![win info2](https://gitee.com/xiaobin80/cnblogs/raw/master/images/20181113041828650.png)

## 1. service register
### 0) ON Services
> cas.properties

Append the following Key-Value:
```bash
cas.serviceRegistry.initFromJson=true
cas.serviceRegistry.json.location=classpath:/services
```

### 1) services file
  webapps\cas\WEB-INF\classes\services
- cas spring secured app    
    casSecuredApp-19991.json
- cas client for java    
    loginInfo-10000004.json

## 2. JDBC Support
### 1) down jar
  MySQL Connector/J is 5.x;

  cas-server-support-jdbc for matching ' cas.server '.

If the cas.authn.jdbc.query[0].passwordEncoder.type is not <strong>NONE</strong>, add the <strong>cas-server-support-jdbc-authentication</stong> package.
```bash
https://mvnrepository.com/artifact/mysql/mysql-connector-java

https://mvnrepository.com/artifact/org.apereo.cas/cas-server-support-jdbc

https://mvnrepository.com/artifact/org.apereo.cas/cas-server-support-jdbc-authentication
```
%%%Copy the above three files (.jar) to the lib(..\webapps\cas\WEB-INF\lib) folder.%%%

### 2) properties
    application.properties(..\webapps\cas\WEB-INF\classes)
```bash
##
# CAS Authentication Credentials
#
#cas.authn.accept.users=casuser::Mellon
```

#### mysql5.5-mysql5.7
> cas.properties

```bash
# server.port = 8443
cas.server.name: https://localhost:8443
cas.server.prefix: https://localhost:8443/cas

cas.adminPagesSecurity.ip=127\.0\.0\.1

logging.config: file:/etc/cas/config/log4j2.xml
cas.serviceRegistry.initFromJson=true
cas.serviceRegistry.json.location=classpath:/services

# SSL
# server.ssl.enabled=false

cas.authn.accept.users=
cas.authn.accept.name=
#CAS Database Authentication Property
#cas.authn.jdbc.query[0].sql=SELECT * FROM users WHERE email = ?
cas.authn.jdbc.query[0].sql=SELECT * FROM app_user WHERE email = ?
cas.authn.jdbc.query[0].url=jdbc:mysql://127.0.0.1:3306/test
cas.authn.jdbc.query[0].dialect=org.hibernate.dialect.MySQLDialect
cas.authn.jdbc.query[0].user=DBAdmin
cas.authn.jdbc.query[0].password=xbfirst
cas.authn.jdbc.query[0].ddlAuto=none
cas.authn.jdbc.query[0].driverClass=com.mysql.jdbc.Driver
## mysql8+
#cas.authn.jdbc.query[0].driverClass=com.mysql.cj.jdbc.Driver
cas.authn.jdbc.query[0].fieldPassword=password
#cas.authn.jdbc.query[0].passwordEncoder.type=NONE
cas.authn.jdbc.query[0].passwordEncoder.type=BCRYPT
# cas.authn.jdbc.query[0].passwordEncoder.characterEncoding=
# cas.authn.jdbc.query[0].passwordEncoder.encodingAlgorithm=
# cas.authn.jdbc.query[0].passwordEncoder.secret=
cas.authn.jdbc.query[0].passwordEncoder.strength=10
```

#### mysql8.0+
> cas.properties

```bash
# server.port = 8443
cas.server.name: https://localhost:8443
cas.server.prefix: https://localhost:8443/cas

cas.adminPagesSecurity.ip=127\.0\.0\.1

logging.config: file:/etc/cas/config/log4j2.xml
cas.serviceRegistry.initFromJson=true
cas.serviceRegistry.json.location=classpath:/services

# SSL
# server.ssl.enabled=false

cas.authn.accept.users=
cas.authn.accept.name=
#CAS Database Authentication Property
#cas.authn.jdbc.query[0].sql=SELECT * FROM users WHERE email = ?
cas.authn.jdbc.query[0].sql=SELECT * FROM app_user WHERE email = ?
#cas.authn.jdbc.query[0].url=jdbc:mysql://127.0.0.1:3306/test
cas.authn.jdbc.query[0].url=jdbc:mysql://127.0.0.1:3306/test?serverTimezone=UTC
cas.authn.jdbc.query[0].dialect=org.hibernate.dialect.MySQLDialect
cas.authn.jdbc.query[0].user=DBAdmin
cas.authn.jdbc.query[0].password=xbfirst
cas.authn.jdbc.query[0].ddlAuto=none
##cas.authn.jdbc.query[0].driverClass=com.mysql.jdbc.Driver
## mysql8+
cas.authn.jdbc.query[0].driverClass=com.mysql.cj.jdbc.Driver
cas.authn.jdbc.query[0].fieldPassword=password
#cas.authn.jdbc.query[0].passwordEncoder.type=NONE
cas.authn.jdbc.query[0].passwordEncoder.type=BCRYPT
# cas.authn.jdbc.query[0].passwordEncoder.characterEncoding=
# cas.authn.jdbc.query[0].passwordEncoder.encodingAlgorithm=
# cas.authn.jdbc.query[0].passwordEncoder.secret=
cas.authn.jdbc.query[0].passwordEncoder.strength=10
```

### 3) table
APP_USER
```sql
create table APP_USER (
   id BIGINT NOT NULL AUTO_INCREMENT,
   sso_id VARCHAR(30) NOT NULL,
   password VARCHAR(100) NOT NULL,
   first_name VARCHAR(30) NOT NULL,
   last_name  VARCHAR(30) NOT NULL,
   email VARCHAR(30) NOT NULL,
   state VARCHAR(30) NOT NULL,  
   PRIMARY KEY (id),
   UNIQUE (sso_id)
);
```
> insert data
```sql
INSERT INTO APP_USER(sso_id, password, first_name, last_name, email, state)
VALUES ('sam','$2a$10$4eqIF5s/ewJwHK1p8lqlFOEm2QIA0S8g6./Lok.pQxqcxaBZYChRm', 'Sam','Smith','samy@xyz.com', 'Active');
```

## 3. test
[cas client](https://xiaobin80.gitee.io/cnblogs/post/cas-client-config)
![cas info3](https://gitee.com/xiaobin80/cnblogs/raw/master/images/20181115204456890.png)

## Source
  [configure file](https://gitee.com/xiaobin80/cas-server)
- casSecuredApp-19991.json
```json
{
    "@class" : "org.apereo.cas.services.RegexRegisteredService",
    "serviceId" : "^http://localhost:9000/login/cas",
    "name" : "CAS Spring Secured App",
    "description": "This is a Spring App that usses the CAS Server for it's authentication",
    "id" : 19991,
    "evaluationOrder" : 1
}
```

- loginInfo-10000004.json
```json
{
  "@class" : "org.apereo.cas.services.RegexRegisteredService",
  "serviceId" : "^http://localhost:9080/loginInfo/",
  "name" : "Login Information",
  "id" : 10000004,
  "description" : "Sample service for Cas client",
  "evaluationOrder" : 1
}
```

# Reference
##  0. [Database Authentication](https://apereo.github.io/cas/5.3.x/installation/Database-Authentication.html)
### 1) [configure](https://apereo.github.io/cas/5.3.x/installation/Configuration-Properties.html#database-authentication)
### 2) [password-encode](https://apereo.github.io/cas/5.3.x/installation/Configuration-Properties-Common.html#password-encoding)

## 1. [Service Registry](https://apereo.github.io/cas/5.3.x/installation/Configuration-Properties.html#service-registry)
### 1) [JSON Service Registry](https://apereo.github.io/cas/5.3.x/installation/Configuration-Properties.html#json-service-registry)
## 2. keytool - del
```bash
keytool -delete -alias thekeystore -keystore "%JAVA_HOME%\jre\lib\security\cacerts" -storepass changeit
```
## 3. [bcrypt tool](https://bcrypt-generator.com/)
