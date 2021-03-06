### Activiti6.0

<br>
<br>

* 工作流是对工作流程机器各个操作步骤之间业务规则的抽象，概括描述
* 工作流建模即将工作流程中的工作如何前后组织在一起的逻辑和规则，在计算机中以恰达的模型表达并对其实施计算
* 为实现某个业务目标，利用计算机在多个参与者之间按照某种预定规则自动传递文档，信息或者任务

<br>
<br>

**ProcessEnigne**

<br>

![enter description here](https://www.github.com/xufeifan1992/note/raw/master/images/2019424/1556077585442.png)

<br>

* ProcessEnigne
	* RepositoryService:通过它对流程定一文件的管理，主要操作静态文件流程文件中的xml或者流程图片
		* 通过RepositoryService: 部署流程对象会设计到两个实体类：
			* 部署对象和资源对象，1对多的关系
			
	<br>
	
	* RuntimeService
		* 对流程进行控制的API，可以启动一个流程实例，针对流程实例进行暂停，挂起，继续，也可以查询正在运行得流程实例和执行对象，可以对流程中的上下文数据进行设置和获取
		
	 <br>
	 
	* TaskService
		* 管理运行的userTask，对该类型任务进行增删改成，对userTask设置指定得操作权限，指定用户和用户组，对userTask上下文变量进行设置和获取
	
	<br>
	
	* IdentityService
		* 对于用户和用户组的管理，创建用户和用户组，并维护用户和用户组的关系
	
	<br>
	
	* FormService
		* 解析流程定义中的设计表单，对表单的输入类型和格式进行数据渲染
	
	<br>
	
	* HistoryService
		* 提供了对运行结束的流程实例的查询功能也提供了流程维度和任务维度的删除操作，方便我们统计流程执行过程的边话
	
	<br>
	
	* ManagementService
		* 主要是对流程引擎基础的管理，提供了对定时执行任务job的管理
	
	<br>
	
	* DynamicBpmService
		* 侵入性比较高的功能，可以动态对流程模型进行修改
		
**表的介绍**
<br>
<br>

* 流程实例通过activiti流程引擎进行流转的过程都会持久化到数据库中，默认支持多种常见的关系型数据库，例如MySQL,Oracle,java内存数据库H2
	* ACT_GE_* : 通用数据存储(General)
	* ACT_RE_* : 流程定一存储表(Repository)
	* ACT_ID_* : 身份信息表(Identity)
	* ACT_RU_* : 运行时数据库表(Runtime)
	* ACT_HI_* : 历史数据库表(History)

<br>
<br>

**BPMN2.0规范**
***Business Process model and Notation***

<br>
<br>

* 是一套业务流程模型于符号的建模标准
* 精准的执行语义来描述元素的操作
* 以XML为载体，以符号可视化业务操作


<br>
主要分为五个部分
* 流对象(FlowObjects)
	* 活动，事件，网关
* 连接对象(ConnectingObject)
	* 流对象通过连接对象连接起来表示数据得流转
* 数据(Data)
	* 数据流转通过连接对象描述的
* 泳道(Swimlanes)
	* 用于对业务做一个范围维度的区分，比如角色和部门
* 描述对象(Artifacts)
	* 描述对象不影响流程得运行，只是进行补充的描述
 
<br>
<br>

***应用场景举例***
![enter description here](https://www.github.com/xufeifan1992/note/raw/master/images/2019423/1556028121273.png)

<br>
<br>

**Activiti6.0源码**

<br>
<br>

**cfg配置文件相关**

![enter description here](https://www.github.com/xufeifan1992/note/raw/master/images/2019424/1556098280469.png)

**兼容性相关**
![enter description here](https://www.github.com/xufeifan1992/note/raw/master/images/2019424/1556098345296.png)

**定义节点相关**
![enter description here](https://www.github.com/xufeifan1992/note/raw/master/images/2019424/1556099172941.png)

**事件和监听机制相关**
![enter description here](https://www.github.com/xufeifan1992/note/raw/master/images/2019424/1556099341770.png)

**表单相关**
![enter description here](https://www.github.com/xufeifan1992/note/raw/master/images/2019424/1556099409755.png)

**节点记录，上下文变量**
![enter description here](https://www.github.com/xufeifan1992/note/raw/master/images/2019424/1556099440648.png)

**身份验证相关**
![enter description here](https://www.github.com/xufeifan1992/note/raw/master/images/2019424/1556099461863.png)

**日志相关**
![enter description here](https://www.github.com/xufeifan1992/note/raw/master/images/2019424/1556099654735.png)

**解析流程图相关**
![enter description here](https://www.github.com/xufeifan1992/note/raw/master/images/2019424/1556099738378.png)

**定义了抽象的查询接口，依赖于Mybatis**
![enter description here](https://www.github.com/xufeifan1992/note/raw/master/images/2019424/1556099818943.png)

**流程部署到数据库过程包括二进制文件操作相关**
![enter description here](https://www.github.com/xufeifan1992/note/raw/master/images/2019424/1556100498971.png)

**流程执行相关**
![enter description here](https://www.github.com/xufeifan1992/note/raw/master/images/2019424/1556100527251.png)

**任务相关**
![enter description here](https://www.github.com/xufeifan1992/note/raw/master/images/2019424/1556100542808.png)

* PluggableActivitiTestCase
	* 启动时构造activiti引擎

![enter description here](https://www.github.com/xufeifan1992/note/raw/master/images/2019424/1556101775300.png)

<br>
<br>

 **源码介绍**
 
 <br>
 <br>
 
 **Activiti6.0模块**
 
 <br>
 <br>
 
 * 核心模块
	 * module/acitivit-engine  核心引擎
		 * bpmn-converter
		 * process-validation
		 * image-generator
		 * dmn-api
		 * form-api
		 * form-model
	 * module/activiti-spring Spring集成模块
	 * module/activiti-spring-boot SpringBoot 集成模块
	 * module/activiti-rest 对台提供rest api模块
	 * module/activiti-form-engine 表单引擎模块
	 * module/activiti-ldap 集成ldap用户模块

<br>

* activiti-ui
	* activiti-app 集成发布的war包
	* activiti-app-conf UI独立于业务外配置
	* activiti-app-logic UI的业务逻辑
	* activiti-app-rest 提供接口

<br>

![enter description here](https://www.github.com/xufeifan1992/note/raw/master/images/2019424/1556113942190.png)

<br>

* 初始化方法，构建容器
	* 当上下文容器为空时，重新构建，定义根容器rootContext，把spring的容器与servlet容器做一个双向绑定，先把servlet容器放进spring容器中，然后把spring容器放进servlet属性放进去
	* 初始化spring相关内容
	* 初始化spring安全相关

![enter description here](https://www.github.com/xufeifan1992/note/raw/master/images/2019424/1556115266758.png)
![enter description here](https://www.github.com/xufeifan1992/note/raw/master/images/2019424/1556115525347.png)
![enter description here](https://www.github.com/xufeifan1992/note/raw/master/images/2019424/1556115687824.png)
![enter description here](https://www.github.com/xufeifan1992/note/raw/master/images/2019424/1556115864221.png)
	 
<br>
<br>

* WebConfigurer 参考RootContextListener
	* 构造DispatcherServlet
	* RootContextListener   ApplicationConfiguration生成的
		* AppContextDispatcherServlet    AppDispatcherServletConfiguration    对应整个业务的url
		* ApiContextDispatcherServlet     ApiDispatcherServletConfiguration     activiti-rest对外提供的API

<br>
<br>

*初始化方法：构造一个基于注解的AnnotationConfigWebApplicationContext，注册的初始化类为ApplicationConfiguration,把servlet容器与spring容器做双向关联，初始化spring相关配置，初始化spring安全相关的配置，spring相关就是上文提到的 APP，API*

![enter description here](https://www.github.com/xufeifan1992/note/raw/master/images/2019425/1556182933909.png)

*销毁方法：当容器要关闭的时候，把对应的spring容器销毁*

![enter description here](https://www.github.com/xufeifan1992/note/raw/master/images/2019425/1556183225123.png)


```java
//基于spring xml解析出来， 
ProcessEngineConfiguration configuration = ProcessEngineConfiguration.createProcessEngineConfiguration.createProcessEngineConfigurationFromResourceDefault();

```
![enter description here](https://www.github.com/xufeifan1992/note/raw/master/images/2019429/1556527571869.png)

* resource : 加载xml位置
* processEngineConfiguration ：xml中配置引擎的名称
* BeansConfigurationHelper：spring的beanFactory对xml做解析
* XmlBeanDefinitionReader：xmlReader加载对象
* 继承结构
* ![enter description here](https://www.github.com/xufeifan1992/note/raw/master/images/2019429/1556529319623.png)


```java
//只是创建对象
ProcessEngineConfiguration configuration = ProcessEngineConfiguration.createStandaloneProcessEngineConfiguration();

```
![enter description here](https://www.github.com/xufeifan1992/note/raw/master/images/2019429/1556528154715.png)
![enter description here](https://www.github.com/xufeifan1992/note/raw/master/images/2019429/1556529765266.png)

<br>
<br>

**数据库配置**

<br>
<br>

* 缺省配置默认，使用H2内存数据库
* 配置JDBC属性，使用mybatis提供的连接池
	* jdbcUrl : jdbcMaxActiveConnections  最大活跃数
	* jdbcDriver : jdbcMaxldleConnections  最大允许空闲数
	* jdbcUsername : jdbcMaxCheckoutTime 最长的checkOut时间
	* jdbcPassword : jdbcMaxWaitTime 最长等待时间
* 配置DataSource,可自选第三方实现
* MySQL 5.6之前 timestamp不支持毫秒精度，5.6.0至5.6.3之间对毫秒精度有影响，官方建议使用5.6之前的版本或者5.6.4之后的版本。


<br>
<br>

**日志记录与MDC**

<br>
<br>

* 配置开启MDC(Mapped Diagnostic Contexts)
	* 默认没有开启，需要手动设置LogMDC.setMDCEnable(true)
	* 配置logback.xml 日志模板 %X{mdcProcessInstanceID}
	* 刘晨给只有在执行过程中出现异常时才会记录MDC信息
* 配置historyLevel
	* none:不记录历史流程，性能高，流程结束后不可读取
	* activiti:归档流程实例和活动实例，流程变量不同步
	* audit:默认值，在activiti基础上同步变量值，保存表单属性
	* full:性能较差，记录所有实例和变量细节边话
* 配置Event Logging
	* 实验性得事件记录机制，性能影响较大
	* 开启默认记录所有数据的变化过程，表记录快速增长
	* 日志内容json格式，建议存入mongoDB,Elastic Search


**未完待续**