# Springboot 中那些没填<version>的一些探究

以provider的pom.xml为例:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.1.3.RELEASE</version>
        <relativePath/> <!-- lookup parent from repository -->
    </parent>
    <groupId>com.ytj</groupId>
    <artifactId>microservice-provider</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <name>microservice-provider</name>
    <description>Demo project for Spring Boot</description>

    <properties>
        <java.version>1.8</java.version>
        <spring-cloud.version>Greenwich.SR1</spring-cloud.version>
    </properties>

    <dependencies>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
        </dependency>

        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <optional>true</optional>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
    </dependencies>

    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-dependencies</artifactId>
                <version>${spring-cloud.version}</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>

    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
        </plugins>
    </build>

</project>
```

parent是

<parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.1.3.RELEASE</version>
        <relativePath/> <!-- lookup parent from repository -->
    </parent>

spring-boot-starter-parent的parent是spring-boot-dependencies，这个就是最主要的原因啦

注意到：

```xml
<dependency>
    <groupId>org.projectlombok</groupId>
    <artifactId>lombok</artifactId>
    <version>${lombok.version}</version>
</dependency>
```

这个dependency是dependencyManagement下面的。

> ```
> Default dependency information for projects that inherit from
> this one. The dependencies in this section are not immediately resolved.
> Instead, when a POM derived from this one declares a dependency
> described by a matching groupId and artifactId, the version and other values from this
> section are used for that dependency if they were not already specified.
> ```

这个标签下的不会立即加入依赖，而是通过<groupId>和<artifactId>匹配，如果匹配上了，没写version，就会采用这里的<version>.pom文件里的spring-boot-starter-test和lombok都是在这里配置的，spring-cloud-starter-netflix-eureka-client则是通过spring-cloud-dependencies的dependencyManagement 中的spring-cloud-netflix-dependencies里有这个：

​		       <dependency>
​				<groupId>org.springframework.cloud</groupId>
​				<artifactId>spring-cloud-netflix-eureka-client</artifactId>
​				<version>${project.version}</version>
​			</dependency>

按照这种找法，因为父pom有，所以不需要配置。			

