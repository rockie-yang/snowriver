---
ID: 584
post_title: MongoDB-casbah驱动
author: riv
post_date: 2011-08-24 09:39:27
post_excerpt: ""
layout: post
permalink: >
  http://snowriver.org/blog/2011/08/24/mongodb-casbah/
published: true
---
MongoDB是一个不错的文档型NoSQL。MongoDB是NoSQL当中最流行的实现之一。她的简洁强大是我的最爱。
Scala是一个很不错的语言。不象Java那样冗余繁琐，使用起来纯粹为了工作，没有乐趣可言。Scala虽然目前还没有那么流行，但是我相信她会流行起来。
Casbah是MongoDB的官方驱动。所以先拿它玩玩。<a href="http://api.mongodb.org/scala/casbah/current/index.html">Casbah文档</a>写的不错。

试着使用Casbah的时候，东搜西搜，让Casbah在Maven里使用起来。下面是我的pom文件。这当中casbah的type需要是pom，要不然无法编译通过。因为casbah本身没有jar，而只依赖一些子包。<!--more-->
<pre class="brush:xml">
<project xmlns="http://maven.apache.org/POM/4.0.0" 
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <groupId>scala</groupId>
    <artifactId>mongo</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <repositories>
        <repository>
            <id>scala-tools.org</id>
            <name>Scala-tools Maven2 Repository</name>
            <url>http://scala-tools.org/repo-releases</url>
        </repository>
    </repositories>
    <dependencies>
        <dependency>
            <groupId>org.scala-lang</groupId>
            <artifactId>scala-library</artifactId>
            <version>2.9.0-1</version>
        </dependency>
        <dependency>
            <groupId>com.mongodb.casbah</groupId>
            <artifactId>casbah_2.9.0-1</artifactId>
            <version>2.1.5-1</version>
            <scope>compile</scope>
            <type>pom</type>
        </dependency>
    </dependencies>
    <pluginRepositories>
        <pluginRepository>
            <id>scala-tools.org</id>
            <name>Scala-tools Maven2 Repository</name>
            <url>http://scala-tools.org/repo-releases</url>
        </pluginRepository>
    </pluginRepositories>
    <build>
        <sourceDirectory>src/main/scala</sourceDirectory>
        <testSourceDirectory>src/test/scala</testSourceDirectory>
        <plugins>
            <plugin>
                <groupId>org.scala-tools</groupId>
                <artifactId>maven-scala-plugin</artifactId>
            </plugin>
        </plugins>
    </build>
</project>
</pre>