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