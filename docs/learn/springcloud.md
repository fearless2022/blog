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

## Zuul

### 概念

* zuul的主要功能是路由转发和过滤
* zuul默认和Ribbon结合实现了负载均衡的功能

### pom.xml

* ```xml
  <dependency>
      <groupId>org.springframework.cloud</groupId>
      <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
  </dependency>
  <dependency>
      <groupId>org.springframework.cloud</groupId>
      <artifactId>spring-cloud-starter-netflix-zuul</artifactId>
  </dependency>
  ```

### application.yml

* ```yml
  server:
    port: 8769
  eureka:
    client:
      service-url:
        defaultZone: http://localhost:8761/eureka/
  spring:
    application:
      name: zuul-service
  zuul:
    routes:
      api-a:
        path: /api-a/**
        serviceId: ribbon-service
      api-b:
        path: /api-b/**
        serviceId: feign-service
  ```

### 启动类

* ```java
  @SpringBootApplication
  @EnableEurekaClient
  @EnableZuulProxy
  public class ZuulServiceApplication {
  
      public static void main(String[] args) {
          SpringApplication.run(ZuulServiceApplication.class, args);
      }
  
  }
  ```

### 服务过滤

* ```java
  @Slf4j
  @Component
  public class MyFilter extends ZuulFilter {
      @Override
      public String filterType() {
          return "pre";
      }
  
      @Override
      public int filterOrder() {
          return 0;
      }
  
      @Override
      public boolean shouldFilter() {
          return true;
      }
  
      @Override
      public Object run() throws ZuulException {
          RequestContext ctx = RequestContext.getCurrentContext();
          HttpServletRequest request = ctx.getRequest();
          log.info(String.format("%s >>> %s", request.getMethod(), request.getRequestURL().toString()));
          Object accessToken = request.getParameter("token");
          if(accessToken == null) {
              log.warn("token is empty");
              ctx.setSendZuulResponse(false);
              ctx.setResponseStatusCode(401);
              try {
                  ctx.getResponse().getWriter().write("token is empty");
              }catch (Exception e){}
  
              return null;
          }
          log.info("ok");
          return null;
      }
  }
  ```

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
      
      /**
       * 配置负载均衡规则：随机
       */
      @Bean
      public IRule initIRule() {
          return new RandomRule();
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
      // 必须添加@RequestParam注解，否则不能取到参数值
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

### **Feign 如何结合 Ribbon 切换均衡算法**

* 默认轮询负载均衡算法

* 在 application.yml 配置文件中指定

* ```yaml
  # feign和ribbon结合，指定策略。feign默认的是轮询的策略，这里的配置可以自定义
  MICROSERVICE-ORDER:
   ribbon:
     NFLoadBalancerRuleClassName: com.netflix.loadbalancer.RandomRule
  ```

## Bus

### 概念

* 消息总线，实现不重启服务更新配置
* 需整合rabbitmq
* **POST请求：**[http://localhost:9090/actuator/bus-refresh](http://localhost:9090/actuator/bus-refresh)

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

## Config

### 概念

* 分布式配置中心组件
* 服务配置文件统一管理，实时更新

### config-server

#### pom.xml

* ```xml
  <dependency>
      <groupId>org.springframework.cloud</groupId>
      <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
  </dependency>
  <dependency>
      <groupId>org.springframework.cloud</groupId>
      <artifactId>spring-cloud-config-server</artifactId>
  </dependency>
  ```

#### application.yml

##### 远程配置中心（git）

* ```yaml
  server:
    port: 8888
  eureka:
    client:
      serviceUrl:
        defaultZone: http://localhost:8761/eureka/
  spring:
    application:
      name: config-server-git
    cloud:
      config:
        server:
          git:
            uri: https://github.com/forezp/SpringcloudConfig/ # 配置git仓库地址
            search-paths: respo # 配置仓库路径
            username: # 访问git仓库的用户名
            password: # 访问git仓库的用户密码
        label: master # 配置仓库的分支
  ```

##### 本地配置中心

* ```yaml
  server:
    port: 8889
  eureka:
    client:
      serviceUrl:
        defaultZone: http://localhost:8761/eureka/
  spring:
    application:
      name: config-server-native
    profiles:
      active: native  # 指明配置文件在本地，会默认从应用的 src/main/resource 目录下检索配置文件
    cloud:
      config:
        server:
          native:
            search-locations: D:/Work/IdeaWorkSpace/EEM_SYS/src/main/resources/ # 配置文件的目录
  ```

#### 启动类

* ```java
  @SpringBootApplication
  @EnableConfigServer
  @EnableEurekaClient
  public class ConfigServerNativeApplication {
  
      public static void main(String[] args) {
          SpringApplication.run(ConfigServerNativeApplication.class, args);
      }
  
  }
  ```

### config-client

#### pom.xml

* ```xml
  <dependency>
      <groupId>org.springframework.cloud</groupId>
      <artifactId>spring-cloud-starter-config</artifactId>
  </dependency>
  <dependency>
      <groupId>org.springframework.cloud</groupId>
      <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
  </dependency>
  ```

#### bootstrap.yml

* ```yaml
  server:
    port: 9000
  eureka:
    client:
      serviceUrl:
        defaultZone: http://localhost:8761/eureka/
  spring:
    application:
      name: config-client
    cloud:
      config:
        profile: dev
        label: master # 远程分支
        discovery:
          enabled: true
          serviceId: config-server-git # 配置中心的服务名
          # serviceId: config-server-native
  ```

#### 启动类

* ```java
  @SpringBootApplication
  @EnableEurekaClient
  @RestController
  public class ConfigClientApplication {
  
      public static void main(String[] args) {
          SpringApplication.run(ConfigClientApplication.class, args);
      }
  
      /**
       * 本地路劲
       */
      //@Value("${spring.datasource.url}")
      @Value("${foo}")
      String foo;
  
      @GetMapping(value = "/hi")
      public String hi(){
          return foo;
      }
  
  }
  ```

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

## Sleuth 

### 概念

* Spring Cloud Sleuth集成了zipkin组件
* 服务追踪组件zipkin

### zipkin-server

* 不需要自己构建，下载jar包

* ```java
  java -jar zipkin-server-2.12.9-exec.jar
  ```

* 访问地址：[http://localhost:9411](http://localhost:9411)

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

## sentinel

### 启动命令

* ```bash
  # -Dserver.port=8718 用于指定 Sentinel 控制台端口为 8718
  # 把dashboard自己也当成一个资源加入到了dashboard中来进行监控
  java -Dserver.port=8718 -Dcsp.sentinel.dashboard.server=localhost:8718 -Dproject.name=sentinel-dashboard -jar sentinel-dashboard-1.8.4.jar
  
  # 如果不想把dashboard自己加入控制台监控
  java -Dserver.port=8718 -Dproject.name=sentinel-dashboard -jar sentinel-dashboard-1.8.4.jar
  ```

### yml配置

* ```yaml
  spring:
    application:
      name: cloudalibaba-sentinel-service
      sentinel:
        transport: #dashboard地址
          dashboard: localhost:8718
          port: 8719  #默认端口，如果被占用则从8719依次+1扫描
  ```


## 服务调用

### eureka

* Eureka Client：负责将这个服务的信息注册到Eureka Server中
* Eureka Server：注册中心，里面有一个注册表，保存了各个服务所在的机器和端口号
* **各个服务启动时，Eureka Client都会将服务注册到Eureka Server，并且Eureka Client还可以反过来从Eureka Server拉取注册表，从而知道其他服务在哪里**

### feign

* **Feign的一个关键机制就是使用了动态代理**
* 首先，如果你对某个接口定义了@FeignClient注解，Feign就会针对这个接口创建一个动态代理
* 接着你要是调用那个接口，本质就是会调用 Feign创建的动态代理，这是核心中的核心
* Feign的动态代理会根据你在接口上的@RequestMapping等注解，来动态构造出你要请求的服务的地址
* 最后针对这个地址，发起请求、解析响应
* **基于Feign的动态代理机制，根据注解和选择的机器，拼接请求URL地址，发起请求**

### ribbon

* 首先Ribbon会从 Eureka Client里获取到对应的服务注册表，也就知道了所有的服务都部署在了哪些机器上，在监听哪些端口号
* 然后Ribbon就可以使用默认的Round Robin算法，从中选择一台机器
* Feign就会针对这台机器，构造并发起请求
* **服务间发起请求的时候，基于Ribbon做负载均衡，从一个服务的多台机器中选择一台**

### hystrix

* 发起请求是通过Hystrix的线程池来走的，不同的服务走不同的线程池，实现了不同服务调用的隔离，避免了服务雪崩的问题

### zuul

* 如果前端、移动端要调用后端系统，统一从Zuul网关进入，由Zuul网关转发请求给对应的服务
