## Hystrix

### 概念

* 断路器

### 在ribbon中使用

#### pom.xml

* ```xml
  <dependency>
      <groupId>org.springframework.cloud</groupId>
      <artifactId>spring-cloud-starter-netflix-hystrix</artifactId>
  </dependency>
  ```

#### 启动类

* ```java
  @SpringBootApplication
  @EnableEurekaClient
  @EnableHystrix
  public class RibbonServiceApplication {
  
      public static void main(String[] args) {
          SpringApplication.run(RibbonServiceApplication.class, args);
      }
  
      @Bean
      @LoadBalanced
      public RestTemplate restTemplate(){
          return new RestTemplate();
      }
  
  }
  ```

#### 关键代码

* ```java
  @Service
  public class HiService {
  
      @Resource
      private RestTemplate restTemplate;
  
      @HystrixCommand(fallbackMethod = "hiError")
      public String hi(String name) {
          return restTemplate.getForObject("http://eureka-client/hi?name=" + name, String.class);
      }
  
      public String hiError(String name) {
          return "hi,"+name+",sorry,error!";
      }
  }
  ```

### 在feign中使用

#### application.yml

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

#### 启动类

* ```java
  @SpringBootApplication
  @EnableFeignClients
  public class FeignServiceApplication {
  
      public static void main(String[] args) {
          SpringApplication.run(FeignServiceApplication.class, args);
      }
  
  }
  ```

#### 关键代码

* ```java
  @FeignClient(name = "eureka-client", fallback = HiHystrix.class)
  public interface HiService {
  
      // 1.请求url必须与被调用服务请求url一致，否则不能跳转
      //2.必须添加@RequestParam注解，否则不能取到参数值
      @GetMapping("/hi")
      String hi(@RequestParam String name);
  }
  ```

* ```java
  @Component
  public class HiHystrix implements HiService {
  
      @Override
      public String hi(String name) {
          return "sorry " + name;
      }
  }
  ```