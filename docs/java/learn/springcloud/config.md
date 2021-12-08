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