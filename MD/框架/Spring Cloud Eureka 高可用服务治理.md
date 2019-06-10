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

#### 配置高可用Eureka服务器

##### 配置公用Eureka服务器配置

`application.properties`

```properties
## 定义应用名称
spring.application.name=spring-cloud-eureka-server

##配置服务端口
server.port=9090

##管理端安全失效
management.security.enabled=false

##注册中心注册
eureka.client.register-with-eureka=false
###向注册中心获取注册信息
eureka.client.fetch-registry=false


```





##### 配置Peer 1 Eureka 服务器

`application-peer1.properties`（单机情况相当于profile=“peer1”）

```properties

#peer 1 完整配置

##配置服务端口
##peer1 端口9090
server.port=9090

##peer2主机:localhost,端口9091
peer2.server.host=localhost
peer2.server.port=9092

# Eureka 注册信息
eureka.client.service-url.defaultZone=http://${peer2.server.host=localhost}:${peer2.server.port}/eureka

```

##### 启动Peer 1 Eureka服务器

通过启动参数 --spring.profiles.active=peer1,相当于读取了`application-peer1.properties`和`application.properties`



##### 配置Peer 2 Eureka 服务器

`application-peer2.properties`（单机情况相当于profile=“peer1”）

```properties

#peer 2 完整配置

##配置服务端口
##peer2 端口9091
server.port=9090

##peer1主机:localhost,端口9090
peer1.server.host=localhost
peer1.server.port=9091

# Eureka 注册信息
eureka.client.service-url.defaultZone=http://${peer1.server.host=localhost}:${peer1.server.port}/eureka

```

##### 启动Peer 2 Eureka服务器

通过启动参数 --spring.profiles.active=peer2,相当于读取了`application-peer2.properties`和`application.properties`

 ### Spring Cloud Consul

* Consul组件
  * 服务发现(Service Discovery)
  * 健康检查(Health Check)
  * 键值存储(KV Store) 
  * 多数据中心(Multi Datacenter)
* 理解Raft协议:http://thesecretlivesofdata.com/raft



#### 引入依赖

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-consul-discovery</artifactId>
</dependency>
```

#### 激活服务发现客户端

```java
package com.xuff.springcloudxuff05consulclient;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.client.discovery.EnableDiscoveryClient;

@SpringBootApplication
@EnableDiscoveryClient
public class SpringCloudXuff05ConsulClientApplication {

    public static void main(String[] args) {
        SpringApplication.run(SpringCloudXuff05ConsulClientApplication.class, args);
    }

}

```

#### 利用服务发现API操作

##### 配置应用信息

```properties
## 应用名称
spring.application.name=spring-cloud-consul

## 服务端口
server.port=8080

## 安全管理失效
management.security.enabled=false

## 配置连接Consul
### Consul 主机地址
spring.cloud.consul.host=localhost
### Consul 服务端口
spring.cloud.consul.port=8500
```



##### 编写`DiscoverClient`Controller

```java
package com.xuff.springcloudxuff05consulclient;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.cloud.client.ServiceInstance;
import org.springframework.cloud.client.discovery.DiscoveryClient;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;

import java.util.LinkedList;
import java.util.List;

/**
 * Created by xufei
 * 2019/6/5
 */
@RestController
public class DiscoverClientController {

    private final DiscoveryClient discoveryClient;

    private final String currentAoolicationName;

    @Autowired
    public DiscoverClientController(DiscoveryClient discoveryClient, @Value("spring.application.name") String currentApplicationName) {
        this.discoveryClient = discoveryClient;
        this.currentAoolicationName = currentApplicationName;
    }

    /**
     * 获取当前应用信息
     * @return
     */
    @GetMapping("/current/service-instances")
    public ServiceInstance getCurrentServiceInstance() {
        return discoveryClient.getInstances(currentAoolicationName).get(0);
    }

    /**
     * 获取所有服务名
     *
     * @return
     */
    @GetMapping("/list/services")
    public List<String> listServices() {
        return discoveryClient.getServices();
    }

    /**
     * 获取所有服务实例信息
     *
     * @return
     */
    @GetMapping("/list/service-instances")
    public List<ServiceInstance> listServiceInstances() {
        List<String> services = listServices();

        List<ServiceInstance> serviceInstances = new LinkedList<>();

        services.forEach(serviceName -> {
            serviceInstances.addAll(discoveryClient.getInstances(serviceName));
        });

        return serviceInstances;
    }

}

```



  

 



