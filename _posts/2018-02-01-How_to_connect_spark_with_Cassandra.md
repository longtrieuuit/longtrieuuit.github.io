---
title:  "How to connect spark with Cassandra in Java with Maven Project"
categories: python
permalink: How_to_connect_spark_with_Cassandra.html
tags: [opencv]
---
## Develop environment
- NetBeans IDE 8.2
- Java with Maven Project

pom.xml
```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <groupId>longtrieuuit</groupId>
    <artifactId>sparkexample</artifactId>
    <version>tutorial</version>
    <packaging>jar</packaging>
    
    <dependencies>
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>3.8.1</version>
            <scope>provided</scope>
        </dependency>
     
        <!-- https://mvnrepository.com/artifact/org.apache.spark/spark-sql_2.10 -->
      
      
        <!-- https://mvnrepository.com/artifact/com.datastax.spark/spark-cassandra-connector_2.10 -->
        <!-- https://mvnrepository.com/artifact/com.datastax.spark/spark-cassandra-connector -->
        <dependency>
            <groupId>com.datastax.spark</groupId>
            <artifactId>spark-cassandra-connector_2.11</artifactId>
            <version>2.0.6</version>
        </dependency>


        <dependency>
            <groupId>org.apache.spark</groupId>
            <artifactId>spark-core_2.11</artifactId>
            <version>2.0.0</version>
            <scope>provided</scope>
        </dependency>

        <dependency>
            <groupId>org.apache.spark</groupId>
            <artifactId>spark-sql_2.11</artifactId>
            <version>2.0.0</version>
            <scope>provided</scope>
        </dependency>
       
      
      
        <dependency>
            <groupId>com.google.code.gson</groupId>
            <artifactId>gson</artifactId>
            <version>2.6.2</version>
            <type>jar</type>
        </dependency>
        
        <!-- https://mvnrepository.com/artifact/log4j/log4j -->
        <dependency>
            <groupId>log4j</groupId>
            <artifactId>log4j</artifactId>
            <version>1.2.17</version>
        </dependency>

    </dependencies>
    <properties>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <maven.compiler.source>1.8</maven.compiler.source>
        <maven.compiler.target>1.8</maven.compiler.target>
    </properties>
        <build>
        <plugins>
            <plugin>
                <artifactId>maven-assembly-plugin</artifactId>
                <configuration>
                    <descriptorRefs>
                        <descriptorRef>jar-with-dependencies</descriptorRef>
                    </descriptorRefs>
                </configuration>
                <executions>
                    <execution>
                        <id>make-assembly</id>
                        <phase>package</phase>
                        <goals>
                            <goal>single</goal>
                        </goals>
                    </execution>
                </executions>
            </plugin>
        </plugins>
    </build>
</project>

```


##  Interacting with Cassandra from Spark sample source

```xml
/*
 * To change this license header, choose License Headers in Project Properties.
 * To change this template file, choose Tools | Templates
 * and open the template in the editor.
 */
package longtrieuuit.sparkexample;

import com.datastax.spark.connector.japi.CassandraJavaUtil;
import java.sql.SQLException;
import java.text.ParseException;
import org.apache.spark.SparkConf;
import org.apache.spark.api.java.JavaRDD;
import org.apache.spark.api.java.JavaSparkContext;
import org.apache.spark.storage.StorageLevel;

/**
 *
 * @author trieu
 */
public class Connect_SparkWithCassandra {

    private static final String CASSANDRA_DB_USER = "remoteuser";
    private static final String CASSANDRA_USER_PASS = "FWguest345!";
    private static final String TABLE_NAME = "d_registered_com_1000000005";
    private static final String KEY_SPACES = "testdb";
    private static final String CASSANDRA_HOSTS = "192.168.130.63,192.168.130.64,192.168.130.65,192.168.130.66,192.168.130.67,192.168.130.68,192.168.130.69,192.168.130.70,192.168.130.71,192.168.130.72,192.168.130.73,192.168.130.74";

    public static void main(String[] args) throws SQLException, ClassNotFoundException, ParseException {
        SparkConf sparkConf = new SparkConf(true);
        // sparkConf.setMaster("spark://192.168.1.212:7077");
        sparkConf.setAppName("How to connect Spark with Cassandra App");
        sparkConf.set("spark.cassandra.connection.host", CASSANDRA_HOSTS);
        sparkConf.set("spark.serializer", "org.apache.spark.serializer.KryoSerializer");
        sparkConf.set("spark.cassandra.input.split.size_in_mb", "50");
        sparkConf.set("spark.broadcast.blockSize", "500M");
        sparkConf.set("spark.executor.cores", "1");
        sparkConf.set("spark.rpc.message.maxSize", "400");
        sparkConf.set("spark.driver.maxResultSize", "55g");
        sparkConf.set("spark.kryoserializer.buffer.max.mb", "500");
        sparkConf.set("spark.cassandra.auth.username", CASSANDRA_DB_USER);
        sparkConf.set("spark.cassandra.auth.password", CASSANDRA_USER_PASS);
        sparkConf.set("spark.cassandra.input.consistency.level", "TWO");
        sparkConf.set("spark.shuffle.io.maxRetries", "20");
        sparkConf.set("spark.rpc.numRetries", "30");
        sparkConf.set("spark.task.maxFailures", "30");
   //     sparkConf.set("spark.cassandra.output.consistency.level", "EACH_QUORUM");
        JavaSparkContext javaSparkContext = new JavaSparkContext(sparkConf);
        JavaRDD<com.datastax.spark.connector.japi.CassandraRow> cassandraRDD = CassandraJavaUtil.javaFunctions(javaSparkContext).cassandraTable(KEY_SPACES, TABLE_NAME).select("yyyymmdd,id".split(","));
        
    // Count CassandraRow
        System.out.println("CassandraRow count: " + cassandraRDD.count());
    }

}



```


## start the shell with this command
```shell
/home/ca/spark-2.1.0-bin-hadoop2.7/bin/spark-submit  --class longtrieuuit.sparkexample.Connect_SparkWithCassandra  --master spark://192.168.128.93:7077,192.168.128.94:7077 --driver-memory 1g --total-executor-cores 5  --executor-memory 1G --executor-cores 1 --driver-java-options "-Dspark.akka.frameSize=15" /home/ca/javafile/sparkexample-tutorial-jar-with-dependencies.jar
```



{% include links.html %}
