---
title: "Distributed traceability with Spring Cloud: Sleuth and Zipkin"
description: "zipkin 2.12/2.14+"
date: 2022-05-25T08:08:08+08:00
---

# I. Sleuth

## 0. Concept
- Trace    
  A set of spans that form a call tree structure, forms the trace of the request.
- Span    
  It is the basic unit of work, for example a call to a service.
They are identified with a span ID and a trace ID to which span is owned.
They have start and end, and with it you get track the response time between requests.

- Tag    
  Key/value pair that identifies certain information in the span.
It doesn't contain timestamps, it just identifies.
![ms info](https://gitee.com/xiaobin80/cnblogs/raw/master/images/20181202033721712.png)
<strong>Annotation:</strong> Used to record the existence of an event in time.
With [Brave](https://github.com/openzipkin/brave) instrumentation,
we no longer need to set special events for Zipkin to understand who the client and server are,
where the request started, and where it ended.
For learning purposes, however, we mark these events to highlight what kind of an action took place.

- cs: Client Sent.     
  The client has made a request. This annotation indicates the start of the span.
- sr: Server Received: The server side got the request and started processing it.    
Subtracting the cs timestamp from this timestamp reveals the network latency.
- ss: Server Sent.     
  Annotated upon completion of request processing (when the response got sent back to the client).
  Subtracting the sr timestamp from this timestamp reveals the time needed by the server side to process the request.
- cr: Client Received. Signifies the end of the span.     
The client has successfully received the response from the server side.
Subtracting the cs timestamp from this timestamp reveals the whole time needed by the client to receive the response from the server.

## 1. pom
```xml
<dependency>
  <groupId>org.springframework.cloud</groupId>
  <artifactId>spring-cloud-starter-sleuth</artifactId>
</dependency>
```

## 2. config
### 1) sampler
```yaml
---
spring:
  sleuth:
    sampler:
      probability: 1.0
```

# II. zipkin
- http
![http way](https://gitee.com/xiaobin80/cnblogs/raw/master/images/20181202025422633.png)
- Messaging Brokers
![Messaging Brokers way](https://gitee.com/xiaobin80/cnblogs/raw/master/images/20181202025518778.png)

## 0. zipkin client

### 1) pom
spring boot 2.4.x(2020.0) before:
```xml
<dependency>
  <groupId>org.springframework.cloud</groupId>
  <artifactId>spring-cloud-starter-zipkin</artifactId>
</dependency>
```

spring boot 2.4.x(2020.0) later:
```xml
<dependency>
	<groupId>org.springframework.cloud</groupId>
	<artifactId>spring-cloud-sleuth-zipkin</artifactId>
</dependency>
```

### 2) config
#### base url
old:
```yaml
---
spring:
  zipkin:
    base-url: http://localhost:9411
```

new:
```yaml
---
spring:
  zipkin:
    baseUrl: http://localhost:9411
```

#### sender
##### RabbitMQ
```yaml
---
spring:
  zipkin:
    sender:
      type: RABBIT
```
##### Kafka
```yaml
---
spring:
  zipkin:
    sender:
      type: KAFKA
```
##### Web
  default

## 1. zipkin server
[upgrade to Spring Boot 2.0 NoClassDefFoundError UndertowEmbeddedServletContainerFactory](https://github.com/openzipkin/zipkin/issues/1962)

### 1) down

Note: ES2.4.x最高只能匹配到[v2.12.9](https://repo1.maven.org/maven2/io/zipkin/java/zipkin-server/2.12.9/zipkin-server-2.12.9-exec.jar)

About v2.14+:    

[The Elasticsearch component uses Elasticsearch 5+ features, but is tested against Elasticsearch 6-7.x](https://repo1.maven.org/maven2/io/zipkin/zipkin-server/)

Simply put, only supports [v6-7.x](https://github.com/openzipkin/zipkin#elasticsearch).


### 2) run
> es2.4.x/7.x安装在WSL中。

详细 see[Elasticsearch 2.4 安装](https://tdtc-hrb.github.io/cnblogs/post/es24-install/)

详细 see[Elasticsearch 7.x 安装](https://tdtc-hrb.github.io/cnblogs/post/ES6p-install/)

#### es2.4.x
- for Rabbit    
```bash
java -jar zipkin-server-2.12.9-exec.jar --RABBIT_URI=amqp://admin:admin@192.168.0.19:5672/sleuth --STORAGE_TYPE=elasticsearch --ES_HOSTS=192.168.0.116:9200 --ES_HTTP_LOGGING=BASIC
```

- for kafka    
```bash
java -jar zipkin-server-2.12.9-exec.jar --KAFKA_BOOTSTRAP_SERVERS=192.168.188.107:9092,192.168.188.108:9092,192.168.188.109:9092 --STORAGE_TYPE=elasticsearch --ES_HOSTS=http://localhost:9200 --ES_HTTP_LOGGING=BASIC
```

#### es7.x
- for Rabbit    
```bash
java -jar zipkin-server-2.23.16-exec.jar --RABBIT_URI=amqp://admin:admin@192.168.3.50:5672/sleuth --STORAGE_TYPE=elasticsearch --ES_HOSTS=192.168.3.128:9200 --ES_HTTP_LOGGING=BASIC
```

- for kafka    
```bash
java -jar zipkin-server-2.23.16-exec.jar --KAFKA_BOOTSTRAP_SERVERS=192.168.3.107:9092,192.168.3.108:9092,192.168.3.109:9092 --STORAGE_TYPE=elasticsearch --ES_HOSTS=http://localhost:9200 --ES_HTTP_LOGGING=BASIC
```

# III. MQ

## 1. RabbitMQ
### 0) pom
```xml
<dependency>
  <groupId>org.springframework.cloud</groupId>
  <artifactId>spring-cloud-stream-binder-rabbit</artifactId>
</dependency>
```

### 1) config
```yaml
---
spring:
  rabbitmq:
    addresses: 127.0.0.1
    port: 5672
    username: admin
    password: admin
    virtual-host: sleuth
```

### 2) virtual hosts
切换到Admin选项卡，点击右侧的virtual hosts
![RabbitMQ info](https://gitee.com/xiaobin80/cnblogs/raw/master/images/20190702001931753.png)

Add a new virtual host:
```
name: sleuth
``

## 2. Kafka
### 0) pom
```xml
<dependency>
  <groupId>org.springframework.cloud</groupId>
  <artifactId>spring-cloud-stream-binder-kafka</artifactId>
</dependency>
```

### 1) config
```yaml
spring:
  kafka:
    bootstrap-servers:
      - 192.168.3.127:9092
      - 192.168.3.128:9092
      - 192.168.3.129:9092
```


# IV. Effect Diagram
## 0. gateway
http://localhost:8311/user/listPage
![browser info1](https://gitee.com/xiaobin80/cnblogs/raw/master/images/20181203030036197.png)

## 1. zipkin server
http://localhost:9411
![browser info2](https://gitee.com/xiaobin80/cnblogs/raw/master/images/20181203021235698.png)

## 2. RabbitMQ
> 连接了4个服务：zipkin server
>>  gateway->user-service->userDetails

http://192.168.3.19:15672/#/connections
![browser info3](https://gitee.com/xiaobin80/cnblogs/raw/master/images/20190703112507593.png)


## 3. ES2.4.x
http://localhost:9200/_plugin/elasticsearch-head
![browser info4](https://gitee.com/xiaobin80/cnblogs/raw/master/images/20190318221631051.png)




# Reference
- [Trazabilidad Distribuida con Spring Cloud: Sleuth y Zipkin](https://www.paradigmadigital.com/dev/trazabilidad-distribuida-spring-cloud-sleuth-zipkin/)
- [Spring Cloud Sleuth 3.0.0 released](https://toomuchcoding.com/articles/2021/01/04/spring-cloud-sleuth-3-0-0-released/)
- [why need Spring sleuth when spring-cloud-starter-zipkin uses zipkin.brave?](https://stackoverflow.com/questions/68927591/why-need-spring-sleuth-when-spring-cloud-starter-zipkin-uses-zipkin-brave)
