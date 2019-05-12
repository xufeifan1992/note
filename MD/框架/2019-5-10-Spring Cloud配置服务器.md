### 分布式配置

<br>
<br>

![enter description here](https://markdown.xiaoshujiang.com/img/spinner.gif "[[[1557562736441]]]" )


![enter description here](https://markdown.xiaoshujiang.com/img/spinner.gif "[[[1557568443314]]]" )


#### Spring Cloud 配置服务器

<br>

* Spring Cloud Config Server
	* Spring Cloud 配置服务器提供分布式，动态化集中管理应用配置信息的能力

* 构建Spring Cloud配置服务器
	* @EnableConfigServer

* EnvironmentRepository
	* Spring Cloud 配置服务器管理多个客户端应用的配置信息，然而这些配置休尼希需要通过一定的规则获取，Spring Cloud Config Server 提供EnvironmentRepository接口供客户端应用获取，其中获取维度有三：
		* {application}:配置客户端应用名称，即配置项：spring.application.name
		* {profile}:配置客户端应用当前激活的Profile，即配置项:spring.profile.active
		* {label}:配置服务端标记的版本信息，如Git中的分支名

 ##### 服务端Environment仓储
 * 理解服务端配置映射
	 * /{applicaiton}/{profile}[/{label}]
	 * /{application}-{profile}.yml
	 * /{label}/{application}-{profile}.yml
	 * /{application}-{profile}.properties
	 * /{label}/{application}-{profile}.properties


#### 基于文件系统(File System)
##### 创建本地仓库

<br>
<br>

1.激活应用配置服务器
	在引导类上 @EnableConfigServer

```java
package com.xuff.springcloudxuff03;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class SpringCloudXuff03Application {

	public static void main(String[] args) {
		SpringApplication.run(SpringCloudXuff03Application.class, args);
		System.out.println(System.getProperty("user.dir"));
	}

}


```
	
2.创建本地目录
	`理解Java中的${user.dir},在IDE中是指的当前项目物理路径`
	在IDEA中src/main/resources目录下，创建一个名为"configs"，它的绝对路径：
    `${user.dir}/src/main/resources/config`
3.配置git本地仓库URI
    
	