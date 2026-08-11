---
title: "jmx - first demo"
description: "使用JDK7运行在Win XP"
date: 2020-05-16T03:58:08+08:00
---

# 1. pom.xml
```xml
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>
  <groupId>jmxbook</groupId>
  <artifactId>jmxInAction</artifactId>
  <version>0.0.1-SNAPSHOT</version>
  <build/>

  <repositories>
    <repository>
        <id>datanucleus.org/downloads/maven2</id>
        <name>datanucleus.org Maven Repository</name>
        <url>http://www.datanucleus.org/downloads/maven2/</url>
    </repository>
  </repositories>
  <dependencies>
    <!-- https://mvnrepository.com/artifact/com.sun.jdmk/jmxtools -->
    <dependency>
        <groupId>com.sun.jdmk</groupId>
        <artifactId>jmxtools</artifactId>
        <version>1.2.1</version>
    </dependency>

  </dependencies>
</project>
```
![Jmx architecture](https://gitee.com/xiaobin80/cnblogs/raw/master/images/Jmxarchitecture.png)


# 2. The Agent level
HelloAgent.java
```java
package jmxbook.ch2;

import javax.management.*;
import com.sun.jdmk.comm.HtmlAdaptorServer;

/**
 *
 * @url http://localhost:9092
 * @author Benjamin G. Sullins && Mark B. Whipple
 *
 */
public class HelloAgent implements NotificationListener
{
  private MBeanServer mbs = null;

  public HelloAgent ( )
  {
    mbs = MBeanServerFactory.createMBeanServer( "HelloAgent" );
    HtmlAdaptorServer adapter = new HtmlAdaptorServer();
    HelloWorld hw = new HelloWorld();
    ObjectName adapterName = null;
    ObjectName helloWorldName = null;

    try
    {
      adapterName = new ObjectName( "HelloAgent:name=htmladapter,port=9092" );
      mbs.registerMBean( adapter, adapterName );
      adapter.setPort( 9092 );
      adapter.start();
      helloWorldName = new ObjectName( "HelloAgent:name=helloWorld1" );
      mbs.registerMBean( hw, helloWorldName );
      hw.addNotificationListener( this, null, null );
    }
    catch( Exception e )
    {
      e.printStackTrace();
    }

  }//constructor

  public void handleNotification( Notification notif, Object handback )
  {
    System.out.println( "Receiving notification..." );
    System.out.println( notif.getType() );
    System.out.println( notif.getMessage() );
  }

  /**
   * @exec java -cp jmxInAction.jar;jmxtools.jar jmxbook.ch2.HelloAgent
   * @param args
   */
  public static void main( String args[] )
  {
    HelloAgent agent = new HelloAgent();
  }

}//class
```

# 3. The Probe level

## 1) Standard MBeans
### MBean
```java
package jmxbook.ch2;

/**
 *
 * @author Benjamin G. Sullins && Mark B. Whipple
 *
 */
public interface HelloWorldMBean
{
  public void setGreeting( String greeting );
  public String getGreeting();
  public void printGreeting();
}
```

### impl
```java
package jmxbook.ch2;
import javax.management.*;

/**
 *
 * @author Benjamin G. Sullins && Mark B. Whipple
 *
 */
public class HelloWorld extends NotificationBroadcasterSupport implements HelloWorldMBean
{

  public HelloWorld()
  {
    this.greeting = "Hello World! I am a Standard MBean";
  }

  public HelloWorld( String greeting )
  {
    this.greeting = greeting;
  }

  public void setGreeting( String greeting )
  {
    this.greeting = greeting;
    Notification notification = new Notification( "jmxbook.ch2.helloWorld.test", this, -1, System.currentTimeMillis(), greeting );
    sendNotification( notification );
  }

  public String getGreeting()
  {
    return greeting;
  }

  public void printGreeting()
  {
    System.out.println( greeting );
  }

  private String greeting;
}//class
```
diagram：[Standard-MBeans](https://pan.baidu.com/s/1Rg3lZTatY8G6emJ4g7uH_w)
![Standard-MBeans](https://gitee.com/xiaobin80/cnblogs/raw/master/images/20180825023547867.png)

# 4. run
## 1) exec java
```bash
#java -cp jmxInAction.jar;jmxtools.jar jmxbook.ch2.HelloAgent
```

## 2) browser
http://localhost:9092
![browser info](https://gitee.com/xiaobin80/cnblogs/raw/master/images/20180823020207094.png)

# Reference
- [jmx - wiki](https://en.wikipedia.org/wiki/Java_Management_Extensions)
- [jmx in action](https://www.manning.com/books/jmx-in-action) - ISBN 9781930110564
