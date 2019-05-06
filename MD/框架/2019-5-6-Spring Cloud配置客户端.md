### Spring Cloud 配置客户端

<br>
<br>

* Spring Environment
	* Environment 是一种以配置(Profile)和属性(Properties)为模型的应用环境抽象整合
	* Spring Framework 提供了两种Environment的实现
		* 一般应用：StandardEnvironment
		* Web应用：StandardServletEnvironment
* Spring Profiles
	* 在Spring 容器，Profile是一种命名的Bean定一逻辑租，一个Spring应用可以同事激活多个Profile，常见的使用场景如：应用部署环境(test,stage,production)，单元测试等
	* 应用程序可以通过调用ConfigurationEnvironment接口控制Profile的激活，如：、
		* setActiveProfiles(String...)
		* addActiveProfiles(String)
		* setDefaultProfiles(String...)
* Spring Properties
* Spring 事件监听器
* ConfigFileApplicationListener

<br>
<br>


