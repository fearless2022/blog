## Eureka

### 概念

* 服务注册中心

* server服务端

* 访问地址 [http://localhost:8761](http://localhost:8761)

### eureka-server

#### pom.xml

* ```xml
  <dependency>
      <groupId>org.springframework.cloud</groupId>
      <artifactId>spring-cloud-starter-netflix-eureka-server</artifactId>
  </dependency>
  ```

#### application.yml

* ```yaml
  server:
     port: 8761 # 指定该Eureka实例的端口
  eureka:
     instance:
        hostname: localhost # 指定该Eureka实例的主机名
     # 配置关闭自我保护，并按需配置Eureka Server清理无效节点的时间间隔
     server:
        enable-self-preservation: false
        eviction-interval-timer-in-ms: 4000
     # 通过eureka.client.registerWithEureka:false和fetchRegistry:false，来表明自己是一个eureka server
     client:
        registerWithEureka: false
        fetchRegistry: false
        serviceUrl:
           defaultZone: http://${eureka.instance.hostname}:${server.port}/eureka/
  ```

#### 启动类

* ```java
  /**
   * 使用Eureka做服务发现.
   */
  @SpringBootApplication
  @EnableEurekaServer
  public class EurekaServerApplication {
   
  	public static void main(String[] args) {
  		SpringApplication.run(EurekaApplication.class, args);
  	}
   
  }
  ```

### eureka-client

#### pom.xml

* ```xml
  <dependency>
      <groupId>org.springframework.cloud</groupId>
      <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
  </dependency>
  ```

#### application.yml

* ```yaml
  server:
     port: 8000
  spring:
     application:
        name: eureka-client # 项目名称尽量用小写
  eureka:
     # 配置开启健康检查
     client:
        healthcheck:
           enabled: true
      serviceUrl:
           defaultZone: http://localhost:8761/eureka/ # 指定注册中心的地址
     instance:
        # IP地址优先
        preferIpAddress: true
        # 按需配置续约更新时间和到期时间
        lease-expiration-duration-in-seconds: 30
        lease-renewal-interval-in-seconds: 10
  ```

#### 启动类

* ```java
  @RestController
  @EnableEurekaClient
  @SpringBootApplication
  public class EurekaClientApplication {
  
      public static void main(String[] args) {
          SpringApplication.run(EurekaClientApplication.class, args);
      }
  
      @Value("${server.port}")
      String port;
  
      @RequestMapping("/hi")
      public String home(@RequestParam(value = "name", defaultValue = "forezp") String name) {
          return "hi " + name + " ,i am from port:" + port;
      }
  
  }
  ```
