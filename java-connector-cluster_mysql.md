---
title: "cluster connector"
description: "mysql"
date: 2020-05-16T05:58:08+08:00
---

# Elementary
> 在没有出来NDB之前，都是使用这个组件。    

[C-JDBC](http://www.frogthinker.org/projects/sequoia)     
> [c-jdbc文档](http://c-jdbc.ow2.org/index.html)    
> [src-Sequoia 2.10.10](https://sourceforge.net/projects/sequoiadb/files/)

![Sequoia architecture](https://gitee.com/xiaobin80/cnblogs/raw/master/images/Sequoia_architecture.jpg)


# NDB Cluster
> 关于：
>> JPA、JDBC省略不表。
![mysql dev](https://dev.mysql.com/doc/ndbapi/en/images/mccj-java-to-cluster.png)

## ClusterJ
> ClusterJ requires Java 1.7 or 1.8.

![Figure 4.2 ClusterJ User View Of Application And Environment](https://dev.mysql.com/doc/ndbapi/en/images/mccj-clusterj-user-view.png)

首先使用[SessionFactory](https://dev.mysql.com/doc/ndbapi/en/mccj-clusterj-sessionfactory.html)建立一个会话实例，    
然后用[Session](https://dev.mysql.com/doc/ndbapi/en/mccj-clusterj-session.html)提供的方法操作数据。

### 会话实例

读取配置文件，然后建立Session实例。

```java
File propsFile = new File("clusterj.properties");
InputStream inStream = new FileInputStream(propsFile);
Properties props = new Properties();
props.load(inStream);
```

```java
SessionFactory factory = ClusterJHelper.getSessionFactory(props);

Session session = factory.getSession();
```

#### 配置信息
> clusterj.properties

```properties
com.mysql.clusterj.connectstring=localhost:1186
com.mysql.clusterj.database=mydb
```

### 操作数据
参考[4.2.2.4 ClusterJ Basic Operations](https://dev.mysql.com/doc/ndbapi/en/mccj-using-clusterj-operations.html)文档。


# Reference
- [4.2.2 Using ClusterJ](https://dev.mysql.com/doc/ndbapi/en/mccj-using-clusterj.html)
