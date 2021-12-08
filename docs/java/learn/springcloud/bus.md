## Bus

### 概念

* 消息总线，实现不重启服务更新配置
* 需整合rabbitmq
* **POST请求：**localhost:9090/actuator/bus-refresh

### config-client
#### pom.xml

* ```xml
  <dependency>
      <groupId>org.springframework.cloud</groupId>
      <artifactId>spring-cloud-starter-bus-amqp</artifactId>
  </dependency>
  <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-actuator</artifactId>
  </dependency>
  ```

* **注意：**必须添加actuator依赖，否则刷新配置post请求404

#### bootstrap.yml

* ```yaml
  server:
    port: 9090
  eureka:
    client:
      serviceUrl:
        defaultZone: http://localhost:8761/eureka/
  spring:
    application:
      name: config-client
    rabbitmq:
      host: localhost
      port: 5672
      username: guest
      password: guest
    cloud:
      bus:
        enabled: true
        trace:
          enabled: true
      config:
        profile: dev
        label: master # 远程分支
        discovery:
          enabled: true
          serviceId: config-server-git # 配置中心的服务名
          # serviceId: config-server-native
  management:
    endpoints:
      web:
        exposure:
          include:
            bus-refresh
  ```

#### 启动类

* ```java
  @SpringBootApplication
  @EnableEurekaClient
  @RestController
  @RefreshScope
  public class ConfigClientApplication {
  
      public static void main(String[] args) {
          SpringApplication.run(ConfigClientApplication.class, args);
      }
  
      /**
       * POST请求
       * http://localhost:9090/actuator/bus-refresh
       */
  
      /**
       * 本地路径
       */
      //@Value("${spring.datasource.url}")
      @Value("${user.name}")
      String foo;
  
      @Value("${server.port}")
      String port;
  
      @GetMapping("/hi")
      public String home(@RequestParam(value = "name", defaultValue = "forezp") String name) {
          return "hi " + name + ", i am from port: " + port + ", message: " + foo;
      }
  
  }
  ```