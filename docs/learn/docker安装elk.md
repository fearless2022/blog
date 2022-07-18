## 安装elasticsearch

* ```bash
  elasticsearch:6.8.11
  ```

## 安装kibana

* ```bash
  kibana:6.8.11
  ```

## 拉取logstash

* ```bash
  # 版本与elasticsearch、kibana保持一致
  docker pull logstash:6.8.11

## 创建配置文件

* ```tex
  创建文件夹保存配置文件
  D:\Work\WorkProject\shop\configuration\logstash
  D:\Work\WorkProject\shop\configuration\logstash\config
  D:\Work\WorkProject\shop\configuration\logstash\pipeline
  在config文件夹下创建logstash.yml
  在pipeline文件夹下创建logstash.conf
  ```

* logstash.yml

* ```yaml
  ## Default Logstash configuration from Logstash base image.
  ### https://github.com/elastic/logstash/blob/master/docker/data/logstash/config/logstash-full.yml
  http.host: "0.0.0.0" #任意主机
  xpack.monitoring.elasticsearch.hosts: [ "http://192.168.3.135:9200" ] 
  #es局域网地址，ip不能写成localhost或127.0.0.1
  #elasticsearch 这里写的是你的ip
  ## X-Pack security credentials
  #xpack.monitoring.enabled: true
  #xpack.monitoring.elasticsearch.username: elastic
  #xpack.monitoring.elasticsearch.password: changeme
  ```

* logstash.conf

* ```json
  input {
    beats {
      host => "0.0.0.0" #任意主机
      port => "4560" #日志收集端口
    }
    file {
      path => "/usr/share/logstash/logs/*.log" #日志收集目录，宿主机产生日志的目录映射到该目录，且必须是绝对路径
      type => "elasticsearch" #日志类型
      start_position => "beginning" #从文件开始处读写
    }
  }
  
  output {
    stdout { codec => rubydebug } #输出到控制台
    elasticsearch {
      hosts => ["192.168.3.135:9200"] #es局域网地址，ip不能写成localhost或127.0.0.1
      index => "logstash-%{+YYYY.MM.dd}" #es索引
    }
  }
  ```

## 启动logstash

* ```bash
  docker run -it -d --name logstash --link docker_elasticsearch_1 --net docker_default -v D:\File\ProjectFile\Resource\elk\config\logstash\pipeline:/usr/share/logstash/pipeline -v D:\File\ProjectFile\Resource\elk\config\logstash\config\logstash.yml:/usr/share/logstash/config/logstash.yml -v D:\Work\IdeaWorkSpace\learn\fearless-admin\log:/usr/share/logstash/logs -p 4560:4560 logstash:6.8.11
  
  # --link + es容器名
  # --net es网络
  # -v 将docker容器目录挂载到宿主机目录（用宿主机目录替代docker容器的相应目录）
  ```

## springboot配置

### 引入依赖

* ```xml
  <dependency>
      <groupId>net.logstash.logback</groupId>
      <artifactId>logstash-logback-encoder</artifactId>
      <version>5.3</version>
  </dependency>
  ```

### logback-spring.xml配置appender

* ```xml
  <!--输出到elk的LOGSTASH-->
  <appender name="LOGSTASH" class="net.logstash.logback.appender.LogstashTcpSocketAppender">
      <!--LOGSTASH：ip:port-->
      <destination>192.168.3.135:4560</destination>
      <encoder charset="UTF-8" class="net.logstash.logback.encoder.LogstashEncoder">
          <providers>
              <timestamp>
                  <timeZone>UTC</timeZone>
              </timestamp>
          </providers>
      </encoder>
  </appender>
  
  <!--默认所有的包以info-->
  <root level="info">
      <appender-ref ref="LOGSTASH"/>
  </root>
  ```

## 查看索引

* ```tex
  安装elasticsearch-head插件
  访问 http://localhost:9100/
  查看索引 logstash-2021.07.16
  ```

## 配置kibana

* ![image-20210716151934190](../../resource/image/docker-es%E5%AE%89%E8%A3%85logstash/image-20210716151934190.png)
* ![image-20210716152025025](../../resource/image/docker-es%E5%AE%89%E8%A3%85logstash/image-20210716152025025.png)

* ![image-20210716152119351](../../resource/image/docker-es%E5%AE%89%E8%A3%85logstash/image-20210716152119351.png)

* ![image-20210716152323912](../../resource/image/docker-es%E5%AE%89%E8%A3%85logstash/image-20210716152323912.png)
