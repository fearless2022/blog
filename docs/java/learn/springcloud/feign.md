## Feign

### 概念

* Feign 采用的是基于接口的注解

* Feign 整合了ribbon，具有负载均衡的能力

* 整合了Hystrix，具有熔断的能力

### pom.xml

* ```xml
  <dependency>
      <groupId>org.springframework.cloud</groupId>
      <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
  </dependency>
  <dependency>
      <groupId>org.springframework.cloud</groupId>
      <artifactId>spring-cloud-starter-openfeign</artifactId>
  </dependency>
  ```

### application.yml

* ```yaml
  server:
    port: 8020
  spring:
    application:
      name: feign-service
  eureka:
    client:
      serviceUrl:
        defaultZone: http://localhost:8761/eureka/
    instance:
      preferIpAddress: true
  feign:
    hystrix:
      enabled: true
  ```

### 启动类

* ```java
  /**
   * Feign是一个声明式的web service客户端，它使得编写web service客户端更为容易。
   * 创建接口，为接口添加注解，即可使用Feign。
   * Feign可以使用Feign注解或者JAX-RS注解，还支持热插拔的编码器和解码器。
   * Spring Cloud为Feign添加了Spring MVC的注解支持，并整合了Ribbon和Eureka来为使用Feign时提供负载均衡。
   * 使用@EnableFeignClients开启Feign
   * 
   * 简而言之：
   * a. Feign 采用的是基于接口的注解
   * b. Feign 整合了ribbon，具有负载均衡的能力
   * c. 整合了Hystrix，具有熔断的能力
   */
  @SpringBootApplication
  @EnableFeignClients
  public class MovieFeignApplication {
  	
  	public static void main(String[] args) {
  		SpringApplication.run(MovieFeignApplication.class, args);
  	}
  	
  }
  ```

### 关键代码

* ```java
  /**
   * 使用@FeignClient("eureka-client")注解绑定eureka-client服务，还可以使用url参数指定一个URL。
   */
  @FeignClient(name = "eureka-client", fallback = HiHystrix.class)
  public interface HiService {
   
  	// 定义代码调用对方服务的"/hi"接口
  	// 请求url必须与被调用服务请求url一致，否则不能跳转
      //必须添加@RequestParam注解，否则不能取到参数值
      @GetMapping("/hi")
  	String hi(@RequestParam(value = "name") String name);
   
  }
  ```

* ```java
  /**
   * Hystric断路器回调方法
   */
  @Component
  public class HiHystrix implements HiService {
  
      @Override
      public String hi(String name) {
          return "sorry " + name;
      }
  }
  ```

