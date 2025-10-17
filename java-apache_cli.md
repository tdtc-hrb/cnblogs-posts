---
title: "Apache CLI Demo"
description: "使用java制作cmd"
date: 2020-05-16T02:58:08+08:00
---

# 1. Options
```java
private Options options = new Options();
```

# 2. option
增加选项有两种方式：直接赋值给各个参数；    
另一种，就是赋值到一个实例中。
## 1) way1
```java
launcher.options.addOption("h", "help", false, "show help.");
```

## 2) way2
```java
launcher.options.addOption(optFile);
```

# 3. command line parser
```java
CommandLineParser cmdParser = new DefaultParser();
```

# 4. exec method
```java
private void help() {
 // This prints out some help
  HelpFormatter formater = new HelpFormatter();
  formater.printHelp(this.getClass().getSimpleName(), options);
  System.exit(0);
}
```

# 5. test
```bash
java -cp cliDemo-0.0.1-SNAPSHOT.jar;C:\Users\xiaobin\.m2\repository\commons-cli\commons-cli\1.4\commons-cli-1.4.jar my.csdn9.ConsoleLauncher --help
```

# source code
ConsoleLauncher.java
```java
package my.csdn9;

import org.apache.commons.cli.CommandLine;
import org.apache.commons.cli.CommandLineParser;
import org.apache.commons.cli.DefaultParser;
import org.apache.commons.cli.HelpFormatter;
import org.apache.commons.cli.Option;
import org.apache.commons.cli.Options;
import org.apache.commons.cli.ParseException;

public class ConsoleLauncher {
    // Step 1: otions
    private Options options = new Options();

    public static void main(String[] args) {
        // TODO Auto-generated method stub
        ConsoleLauncher launcher = new ConsoleLauncher();

        // Step 2: add option
        Option optFile = Option.builder( "file" )
                .longOpt( "logfile" )
                 .hasArg(false)
                 .desc(  "use given file for log" )
                 .build();

        // Way I: Direct definition
        launcher.options.addOption("h", "help", false, "show help.");
        // Way II: Use the option class to define
        launcher.options.addOption(optFile);
        // Step 3: cmd parser
        CommandLineParser cmdParser = new DefaultParser();
        try {
            CommandLine cmd = cmdParser.parse(launcher.options, args);
            if (cmd.hasOption("h")) {
                launcher.help();
            }
            if (cmd.hasOption("file")) {
                 System.out.println("----file----");
            }
        } catch (ParseException e) {
            // TODO Auto-generated catch block
             e.printStackTrace();
        }

    }

     // Step 4: exec method
     private void help() {
        // This prints out some help
         HelpFormatter formater = new HelpFormatter();
         formater.printHelp(this.getClass().getSimpleName(), options);
         System.exit(0);
     }

}
```

## pom
pom.xml
```xml
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>
  <groupId>com.tdtc</groupId>
  <artifactId>cliDemo</artifactId>
  <version>0.0.1-SNAPSHOT</version>
  <name>cliDemo</name>
  <dependencies>
      <dependency>
          <groupId>commons-cli</groupId>
          <artifactId>commons-cli</artifactId>
          <version>1.4</version>
      </dependency>
  </dependencies>
</project>
```

# Reference
- [java command line interfaces - part 1: apache commons](https://dzone.com/articles/java-command-line-interfaces-part-1-apache-commons)
- [Option.Builder](https://commons.apache.org/proper/commons-cli/javadocs/api-release/org/apache/commons/cli/Option.Builder.html)
