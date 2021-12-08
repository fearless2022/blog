## Ribbon

### 概念

* 服务调用
* 支持配置负载均衡策略

### pom.xml

* ```xml
  <dependency>
      <groupId>org.springframework.cloud</groupId>
      <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
  </dependency>
  
  <!-- 整合ribbon -->
  <dependency>
      <groupId>org.springframework.cloud</groupId>
      <artifactId>spring-cloud-starter-netflix-ribbon</artifactId>
  </dependency>
  ```

### application.yml

* ```yaml
  server:
     port: 8010
  spring:
     application:
        name: ribbon-service
  eureka:
     client:
        serviceUrl:
           defaultZone: http://localhost:8761/eureka/
     instance:
        preferIpAddress: true
  ```

### 启动类

* ```java
  /**
   * Ribbon是Netflix发布的开源项目，主要功能是提供客户端的软件负载均衡算法，将Netflix的中间层服务连接在一起。
   * Ribbon客户端组件提供一系列完善的配置项如连接超时，重试等。
   * 简单的说，就是在配置文件中列出Load Balancer后面所有的机器，Ribbon会自动的帮助你基于某种规则（如简单轮询，随机  *  连接等）去连接这些机器。
   * 我们也很容易使用Ribbon实现自定义的负载均衡算法。简单地说，Ribbon是一个客户端负载均衡器。
   * 
   * Ribbon工作时分为两步：
   * 第一步先选择 Eureka Server,它优先选择在同一个Zone且负载较少的Server；
   * 第二步再根据用户指定的策略，在从Server取到的服务注册列表中选择一个地址。
   * 
   * 其中Ribbon提供了多种策略，例如轮询、随机、根据响应时间加权等。
   */
  @SpringBootApplication
  @EnableEurekaClient
  @EnableHystrix
  public class RibbonServiceApplication {
  
      public static void main(String[] args) {
          SpringApplication.run(RibbonServiceApplication.class, args);
      }
  
      /**
  	 * 实例化RestTemplate，通过@LoadBalanced注解开启均衡负载能力.
  	 * @return restTemplate
  	 */
      @Bean
      @LoadBalanced
      public RestTemplate restTemplate(){
          return new RestTemplate();
      }
  
  }
  ```

### 关键代码

* ```java
  @Service
  public class HiService {
  
      @Resource
      private RestTemplate restTemplate;
  
      @HystrixCommand(fallbackMethod = "hiError")
      public String hi(String name) {
          return restTemplate.getForObject("http://eureka-client/hi?name=" + name, String.class);
      }
  
      /**
   	 * Hystric断路器回调方法
  	 */
      public String hiError(String name) {
          return "hi,"+name+",sorry,error!";
      }
  }
  ```

