## Sleuth 

### 概念

* Spring Cloud Sleuth集成了zipkin组件
* 服务追踪组件zipkin

### zipkin-server

* 不需要自己构建，下载jar包

* ```java
  java -jar zipkin-server-2.12.9-exec.jar
  ```

* 访问地址：http://localhost:9411

### zipkin-client

#### pom.xml

* ```xml
  <dependency>
      <groupId>org.springframework.cloud</groupId>
	    <artifactId>spring-cloud-starter-zipkin</artifactId>
  </dependency>
  ```

#### application.yml(bootstrap.yml)

* ```yaml
  spring:
    zipkin:
      base-url: http://localhost:9411
  ```