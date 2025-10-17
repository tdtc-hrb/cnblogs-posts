---
title: "jmx - first demo"
description: "使用Maven构建; jdk7是最后支持WinXP的java版本"
date: 2023-02-08T08:08:08+08:00
---
Starting with the J2SE platform 5.0, JMX technology is included in the Java SE platform.

![Jmx architecture](https://gitee.com/xiaobin80/cnblogs/raw/master/images/Jmxarchitecture.png)

- jmxtools
Download the [jar](https://www.datanucleus.org/downloads/maven2/com/sun/jdmk/jmxtools/1.2.1/jmxtools-1.2.1.jar) 
and [pom](https://www.datanucleus.org/downloads/maven2/com/sun/jdmk/jmxtools/1.2.1/jmxtools-1.2.1.pom) files,
Then copy it to the M2 directory.
M2:
```
C:\Users\tdtc\.m2\repository\com\sun\jdmk\jmxtools\1.2.1
```

- [Source](https://www.manning.com/downloads/737)


# project
.    
|____src    
| |____main    
| | |____java    
| | | |____jmxbook.ch2    
|____pom.xml


Gen jar:
```
mvn package
```

- pom.xml
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

## The Agent level
HelloAgent.java:
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
   * @exec java -cp jmxInAction-0.0.1-SNAPSHOT.jar;jmxtools.jar jmxbook.ch2.HelloAgent
   * @param args
   */
  public static void main( String args[] )
  {
    HelloAgent agent = new HelloAgent();
  }

}//class
```

## The Probe level

### 1) Standard MBeans
- MBean
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

- impl
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


# run
exec java:
```bash
#java -cp jmxInAction-0.0.1-SNAPSHOT.jar;jmxtools.jar jmxbook.ch2.HelloAgent
```

- browser
[localhost](http://localhost:9092)
![browser info](https://gitee.com/xiaobin80/cnblogs/raw/master/images/20180823020207094.png)


# Reference
- [Java Management Extensions (JMX) Technology](https://www.oracle.com/java/technologies/javase/javamanagement.html)
- [jmx in action](https://github.com/siren0413/JMXExample/blob/master/JMX%20in%20Action.pdf) - ISBN 9781930110564
- [Eclpise MVN project prompted error](https://stackoverflow.com/questions/9047949/missing-artifact-com-sun-jdmkjmxtoolsjar1-2-1)
