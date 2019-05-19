## 服务发现/注册

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

### Spring Cloud Netflix Eureka

<br>

* 服务发现：Eureka
	* Eureka是由Netflix公司发明的服务发现中间件，包括服务发现服务器和客户端
* 核心组件
	* Eureka Server
	* Eureka Clinet
* 服务端:Eureka Server
	* Eureka Server是Eureka Client的注册服务中心，管理所有注册服务，以及其实例信息和状态
* 运行Eureka Server
	* 依赖:org.springframework.cloud:spring-cloud-starter-eureka-server
	* 激活:@EnableEurekaServer