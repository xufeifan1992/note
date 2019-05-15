## 分布式配置

<br>
<br>


### Spring Cloud 配置服务器

![enter description here](https://www.github.com/xufeifan1992/note/raw/master/images/2019514/1557806742470.png)

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


### 基于文件系统(File System)
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
3.给应用创建三个环境的配置项	
 
 ```java
 -rw-r--r-- 1 xufei 197609 57 5月  12 13:37 segmentfault.properties
 -rw-r--r-- 1 xufei 197609 61 5月  12 13:40 segmentfault-prod.properties
 -rw-r--r-- 1 xufei 197609 62 5月  12 13:38 segmentfault-test.properties
 ```

4.配置git本地仓库URI
	`##配置服务器文件系统git仓库
	  ##${user.dir}减少平台文件系统的不一致
	  spring.cloud.config.server.git.uri = ${user.dir}/src/main/resources/configs`
	  
 5.初始化本地git仓库
 
```java
   git init   
   git add .  
   git commit -m "Frirst commit   "
   3 files changed, 9 insertions(+)  
   create mode 100644 segmentfault-prod.properties  
   create mode 100644 segmentfault-test.properties  
   create mode 100644 segmentfault.properties  
```
<br>

#### 测试配置服务器

` 通过浏览器访问： http://localhost:9090/segmentfault/test`

```json
{
	name: "segmentfault",
	profiles: [
			"test"
	],
	label: null,
	version: "3cef0f526c3ffa34712dba223ff2ee30faeb251d",
	state: null,
	propertySources: [
			{
				name: "C:\person\idea_code\spring-cloud-xuff-03/src/main/resources/configs/segmentfault-							test.properties",
				source: {
				name: "segmentfault-test"
						}
			},
{
	name: "C:\person\idea_code\spring-cloud-xuff-								03/src/main/resources/configs/segmentfault.properties",
	source: {
	name: "segmentfault"
			}
	}
	]
}
```
`注意：当制定了profile时，默认的profile(不指定)配置也会输出`

<br>
<br>

### 基于远程git仓库


<br>
<br>

1.激活应用配置服务器  
	在引导类上标注@EnableConfigServer
2.配置远程Git仓库地址  
	```java
	###配置远程服务器Git仓库(GitHub)
	spring.cloud.config.server.git.uri = https://github.com/xufeifan1992/tmp
	```
