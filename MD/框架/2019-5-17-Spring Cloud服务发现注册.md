# 服务发现/注册

<br>
<br>

* 服务发现(Service Discovery)
	* 在计算机网络中，一种自动发现设备或者服务得技术，通过服务发现协议(Service Discovery Protcol)实现。
* 常见协议
	* Java:Jini(Apache River)
	* RES:HATEOAS
	* Web Services:UDDI(Universal Description Discovery and Integration)          
* 服务注册(Service Registration)
	* 在计算机网络中，为了更好地治理多个设备或者服务，这些设备或者服务主动或者被动注册管理中心，以便服务被发现和消费。
	* 常见注册中心
		* Apache Zookeeper(性能差，高一致性)
		* Netflix Eureka(高可用，不是高一致性)
		* Consul(既是高可用，也是高一致，相对折中)

<br>

![enter description here](https://www.github.com/xufeifan1992/note/raw/master/images/2019517/1558084886988.png)


<br>

* 高可用(High Availability)
	* 一种系统特性，致力于确保可接受程度的操作执行，通常采用上线时间为基准，其中以一年内上线时间与自然时间的比率来描述可用性
* 基本原则
	* 消灭单点故障
	* 可靠性交迭
	* 故障探测

## Spring Cloud Netflix Eureka

<br>

* 服务发现：Eureka
	* Eureka是由Netflix公司发明的服务发现中间件，包括服务发现服务器和客户端
* 核心组件
	* Eureka Server
	* Eureka Clinet
* 服务端:Eureka Server
	* Eureka Server是Eureka Client的注册服务中心，管理所有注册服务，以及其实例信息和状态等能力 
* 运行Eureka Server
	* 依赖:org.springframework.cloud:spring-cloud-starter-eureka-server
	* 激活:@EnableEurekaServer

<br>

### Eureka服务器

<br>
<br>


#### 引入maven依赖  
```java
<dependency>
	<groupId>org.springframework.cloud</groupId>
	<artifactId>spring-cloud-starter-netflix-eureka-server</artifactId>
</dependency>
```

<br>

#### 激活Eureka服务器  


```java
package com.xuff.springcloudxuff04eurekaserver;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.netflix.eureka.server.EnableEurekaServer;

@SpringBootApplication
@EnableEurekaServer
public class SpringCloudXuff04EurekaServerApplication {

	public static void main(String[] args) {
		SpringApplication.run(SpringCloudXuff04EurekaServerApplication.class, args);
	}

}
```
#### 调整Eureka服务器配置

`application.properties`

<br>

```java
##Spring Cloud Eureka 服务器应用名称
spring.application.name=spring-cloud-eureka-server

##Spring Cloud Eureka 服务器服务端口
server.port=9090

##管理端口安全关闭
management.security.enabled=false
```
#### 检测Eureka Server

<br>

异常堆栈信息

![enter description here](https://www.github.com/xufeifan1992/note/raw/master/images/2019521/1558422720590.png)

>com.sun.jersey.api.client.ClientHandlerException: java.net.ConnectException: Connection refused: connect

`Eureka Server 既是注册服务器，也是客户端，默认情况需要配置注册中心地址`

Application |AMIs|Availability Zones|Status
-|-|-|-
No instances available|-|-|-

#### 解决问题的方法

<br>

```java
##Spring Cloud Eureka 服务器作为注册中心
##通常情况下，不需要再注册到其他注册中心去
##同时，它也不需要获取客户端信息
##取消向注册中心注册
eureka.client.register-with-eureka=false

##取消向注册中心获取注册信息(应用,实例信息)
eureka.client.fetch-registry=false
```



<br>

### Eureka 客户端

<br>

#### 引入依赖

```java
<dependency>
	<groupId>org.springframework.cloud</groupId>
	<artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
</dependency>
```

<br>

#### 激活Eureka客户端

```java
package com.xuff.springcloudxuff04eurekaclient;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.client.discovery.EnableDiscoveryClient;
import org.springframework.cloud.netflix.eureka.EnableEurekaClient;

@SpringBootApplication
@EnableDiscoveryClient
//@EnableEurekaClient
public class SpringCloudXuff04EurekaClientApplication {

    public static void main(String[] args) {
        SpringApplication.run(SpringCloudXuff04EurekaClientApplication.class, args);
    }

}

```

#### 配置Eureka客户端

<br>

运行效果
发现于Eureka服务器控制台相同异常

>com.sun.jersey.api.client.ClientHandlerException: java.net.ConnectException: Connection refused: connect

#### 需要再次调整Eureka客户端配置

<br>

`eureka.client.serviceUrl.defaultZone`

```java
##Spring Cloud Eureka客户端应用名称
spring.application.name=spring-cloud-eureka-client

##Spring Cloud Eureka 客户端服务端口
server.port=9191

##管理端口安全失效
management.security.enabled=false

##Spring Cloud Eureka 客户端 注册到Eureka服务器
eureka.client.serviceUrl.defaultZone=http://localhost:9090/eureka
```

 <br>
 
 