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
	* 属性又被称之为配置项，key-value的形式。在Spring应用中常用作占位符(Placeholder),而在API层面，Spring Framework如下抽象来表述
		* 组合属性：PropertySources
		* 单一属性：PropertySource
* Spring 事件监听器
	* ApplicationEvent
	* ApplicationListener
* ConfigFileApplicationListener
	* SpringBoot中，用于读取默认以及Profile关联的配置文件(application,properties)

![enter description here](https://www.github.com/xufeifan1992/note/raw/master/images/201956/1557133553453.png)

![enter description here](https://www.github.com/xufeifan1992/note/raw/master/images/201956/1557135040181.png)

![enter description here](https://www.github.com/xufeifan1992/note/raw/master/images/201956/1557134010262.png)

![enter description here](https://www.github.com/xufeifan1992/note/raw/master/images/201956/1557135644395.png)

![enter description here](https://www.github.com/xufeifan1992/note/raw/master/images/201956/1557149369581.png)


<br>
<br> 

* SpringBoot配置文件
	* application.properties
		* 加载器:PropertiesPropertySourceLoader
	* application.yml /application.yaml
		* 加载器:YamlPropertySourceLoader
* Environment端点
	* 请求URI:/env
		* 数据来源:EnvironmentEndPoint
		* Controller来源:EnvironmentMVCEndpoint

<br>

![enter description here](https://www.github.com/xufeifan1992/note/raw/master/images/201956/1557149677648.png)

![enter description here](https://www.github.com/xufeifan1992/note/raw/master/images/201956/1557149899196.png)


<br>

* HTTP请求
	* 400：bad request 请求不合法
	* 401：认证失败
	* 403：forbbidden 鉴权不够
	* 404：服务器找不到请求
	* 405：方法禁用
	* 500：服务器内部错误
	* 503：服务不可用

<br>
<br>

#### Bootstrap 配置属性


<br>
<br>

* Bootstrap配置文件
* 调整Bootstrap配置文件路径
* 覆盖源成配配置文件属性
* 自定义Bootstrap配置
* 自定义Bootstrap配置属性源