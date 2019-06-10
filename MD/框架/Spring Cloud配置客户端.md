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


![enter description here](https://www.github.com/xufeifan1992/note/raw/master/images/201956/1557135040181.png)

![enter description here](https://www.github.com/xufeifan1992/note/raw/master/images/201956/1557134010262.png)

![enter description here](https://www.github.com/xufeifan1992/note/raw/master/images/201956/1557135644395.png)

![enter description here](https://www.github.com/xufeifan1992/note/raw/master/images/201956/1557149369581.png)


<br>
<br> 

 #### springBoot配置文件
* application.properties
	* 加载器:PropertiesPropertySourceLoader
* application.yml /application.yaml
	* 加载器:YamlPropertySourceLoader
####  Environment端点

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

 ![enter description here](https://www.github.com/xufeifan1992/note/raw/master/images/201958/1557308502622.png)
 
 
 <br>
 
 ##### Environment
 
 Environment:PropertySources  :  1:1
 PropertySources:PropertySource : 1:N
[0] PropertySource(Map)
 	spring.application.name = spring-cloud-config-client
	
[1] PropertySource(Map)
	spring.application.name = spring.cloud.config.client.demo
	
`取值时,如果key存在，后面的值会被忽略`


 
 
 #### Bootstrap配置
 
 <br>

 
  #### Bootstrap配置文件
  
  <br>
  
  `BootstrapApplicationListener` ：加载时机早于ConfigFileApplicaitonListener
  
  ConfigFileApplicaitonListener：
  原因 ： `DEFAULT_ORDER = Ordered.HIGHEST_PRECEDENCE + 10;` （第十一位 ）
  
  BootstrapApplicationListener：
  原因：DEFAULT_ORDER = Ordered.HIGHEST_PRECEDENCE + 5;（第六位）
  
  ` 如果需要调整 控制Bootstrap上下文行为配置，需要更高优先级，也就是说Order需要小于Ordered.HIGHEST_PRECEDENCE + 5 (越小越优先)，比如使用启动参数`
  
  <br>
  
  当spring.cloud.bootstrap.name:bootstrap存在时，使用该配置项
 
 ```java
 
 String configName = environment
				.resolvePlaceholders("${spring.cloud.bootstrap.name:bootstrap}");
 
 ```
 
 #### 调整Bootstrap配置
 
##### 调整Bootstrap配置文件名称
 
 <br>
 <br>
 
 调整启动参数
 
 <br>


```java
-- spring.cloud.bootstrap.name = spring-cloud

```
bootstrap配置文件名称发生了改变“spring-cloud”，意味着有三个文件

* applicaiton.properties
* bootstrap.properties
* spring-cloud.properties


<br>

##### 覆盖远程配置属性

<br>

默认情况，Spring Cloud 是允许覆盖的，spring.cloud.config.allowOverride = true

<br>

通过程序启动参数，调整这个值为false

```java
-- spring.cloud.config.allowOverride = false 
```

<br>

##### 自定义Bootstrap配置

1.创建 `META-INF/spring.factors文件(类似于Spring Boot Starter) `  
2.自定义Bootstrap配置Configuration  
3.
```java
package com.xuff.springcloudxuff02.bootstrap;

import org.springframework.context.ApplicationContextInitializer;
import org.springframework.context.ConfigurableApplicationContext;
import org.springframework.context.annotation.Configuration;
import org.springframework.core.env.ConfigurableEnvironment;
import org.springframework.core.env.MapPropertySource;
import org.springframework.core.env.MutablePropertySources;
import org.springframework.core.env.PropertySource;

import java.util.HashMap;
import java.util.Map;

/**
 * Bootstrap 配置Bean
 * Created by xufei
 * 2019/5/9
 */
@Configuration
public class MyConfiguration implements ApplicationContextInitializer {
    @Override
    public void initialize(ConfigurableApplicationContext applicationContext) {
        ConfigurableEnvironment environment =
                applicationContext.getEnvironment();
        //获取PropertySources
        MutablePropertySources propertySources =
                environment.getPropertySources();

        //定义一个新的propertySource
        propertySources.addFirst(createPropertySource());
    }

    private PropertySource createPropertySource() {
        Map<String, Object> source = new HashMap<>();
        source.put("name","xuff");
        PropertySource propertySource = new MapPropertySource("my-property-source", source);

        return propertySource;
    }
}

```

![enter description here](https://www.github.com/xufeifan1992/note/raw/master/images/201959/1557389610253.png)

3.配置META-INF/spring.factories文件，关联Key `org.springframework.cloud.BootstrapConfiguration=com.xuff.springcloudxuff02.bootstrap.MyConfiguration`

##### 自定义Bootstrap配置属性源

<br>
<br>

1.实现`PropertySourceLoader`  

```java
package com.xuff.springcloudxuff02.bootstrap;

import org.springframework.cloud.bootstrap.config.PropertySourceLocator;
import org.springframework.core.env.*;

import java.util.HashMap;
import java.util.Map;

/**
 * Created by xufei
 * 2019/5/10
 */
public class MyPropertySourceLocator implements PropertySourceLocator {
    @Override
    public PropertySource<?> locate(Environment environment) {
        if (environment instanceof ConfigurableEnvironment) {
            ConfigurableEnvironment configurableEnvironment = (ConfigurableEnvironment) environment;
            MutablePropertySources propertySources = configurableEnvironment.getPropertySources();
            propertySources.addFirst(createPropertySource());
        }
        return null;
    }

    private PropertySource createPropertySource() {
        Map<String, Object> source = new HashMap<>();
        source.put("spring.application.name", "xuff");
        PropertySource propertySource = new MapPropertySource("over-bootstrap-property-source", source);

        return propertySource;
    }
}

```

2.配置META-INF/spring.factories

	
```java
org.springframework.cloud.bootstrap.BootstrapConfiguration=com.xuff.springcloudxuff02.bootstrap.MyConfiguration,\
com.xuff.springcloudxuff02.bootstrap.MyPropertySourceLocator

```
