# Spring Cloud Eureka 高可用服务治理



* 高可用客户端(HA Client)

  多用于生产环境，客户端应用关联或配置注册中心服务集群，b避免注册中心单点故障

* 常见配置手段

  *  多注册中心主机
  * 注册中心DNS
  * 广播

* Spring Cloud Eureka Client

  如果Eureka客户端应用配置多个Eureka注册服务器，那么默认情况只有一台可用的服务器，存在注册信息。如果第一台可用的Eureka服务器Down掉了，那么Eureka客户端应用将会选择下一台可用的Eureka服务器

* 配置属性

  * eureka.client.serviceUrl.defaultZone=http://${eureka.server1.host}:${eureka.server1.port}/eureka,${eureka.server2.host}:${eureka.server2.port}/eureka
  * 效果演示

## Spring Cloud Eureka



### Eureka客户端



#### 配置多Eureka 注册中心

```properties
##应用名称
spring.application.name=spring-cloud-eureka-
client

##客户端端口随机
server.port=0

##配置连接 Eureka 服务器
##配置多个 Eureka 注册中心，以逗号分隔
eureka.client.service
url.defaultZone=http://localhost:9090/eureka,h
ttp://localhost:9091/eureka
```

#### 激活@EnableEurekaClient或者@EnableDiscoveryClient

```java
package com.xuff.springcloudxuff05eurekaclient;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.netflix.eureka.EnableEurekaClient;

@SpringBootApplication
@EnableEurekaClient
public class SpringCloudXuff05EurekaClientApplication {

    public static void main(String[] args) {
        SpringApplication.run(SpringCloudXuff05EurekaClientApplication.class, args);
    }

}

```

### Eureka 服务器



 





 



