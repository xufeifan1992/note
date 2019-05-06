### SpringCloud

<br>
<br>

 *Spring Cloud 为开发人员提供快速构建分布式系统的一些通用模式，其中包括，配置管理，服务发现，服务短路，只能路由，微型网管，控制总线，一次性令牌，全局锁，领导选举，分布式绘画和集群状态。分布式系统间的协调导向样板模式，并且使用Spring Cloud的开发人员能够快速构建实现这些模式的服务和应用，这些服务和应用也将在任何环境下工作良好，无论是开发者的笔记本，还是数据中心裸机或者管控平台。*

 <br>
 <br>
 
 参考：https://spring.io/projects/spring-cloud
 
 <br>
 <br>
 
 ![enter description here](https://www.github.com/xufeifan1992/note/raw/master/images/201953/1556844938700.png)
 
  <br>
  <br>
  
  #### 12-Factor应用

<br>

下载地址：https://github.com/xufeifan1992/TechnicalSummary/blob/master/Spring%20Cloud%20%E6%8A%80%E6%9C%AF%E4%BD%93%E7%B3%BB.xmind

<br>
<br>

* Codebase   代码管理  svn git
* Dependencies  依赖  maven gradle jvm等
* Config  配置 外部化配置(启动参数,文件等) 内部化配置(代码修改)
* Backing Services  后端服务
* Build,release,run  构建，发布，运行
* Processes  分布式进程
* Port binding  端口绑定
* Concurrency 并发 
* Disposability 易处理
* Dev/prod party 环境等价
* Logs 日志
* Admin processes  管理进程

<br>

![enter description here](https://www.github.com/xufeifan1992/note/raw/master/images/201953/1556848229317.png)


<br>
<br>

#### Bootstrap上下文

<br>
<br>

* 理解Bootstrap上下文
	* Bootstrap上下文是Spring Cloud新引入的，与传统Spring上下文相同，系ConfigurableApplicationContext实例，由BootStrapApplicationListener在监听ApplicationEnviromentPreparedEvent(springBoot中事件)时创建
* Spring事件/监听器模式
	* ApplicationEvent/ApplicationListenter

![enter description here](https://www.github.com/xufeifan1992/note/raw/master/images/201953/1556850814858.png)

<br>

```java

package com.xuff.springcloudxuff01.event;

import org.springframework.context.ApplicationEvent;
import org.springframework.context.ApplicationListener;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;

/**
 * Spring 事件/监听器 Demo
 * Created by xufei
 * 2019/5/3
 */
public class SpringEventListenerDemo {
    public static void main(String[] args) {

        AnnotationConfigApplicationContext context = new AnnotationConfigApplicationContext();

        //增加监听器
        context.addApplicationListener(new MyApplicationListener());

        //上下文启动
        context.refresh();

        //发布事件
        context.publishEvent(new MyAppLicationEvent("Hello World"));
    }

    private static class MyApplicationListener implements ApplicationListener<MyAppLicationEvent> {

        @Override
        public void onApplicationEvent(MyAppLicationEvent event) {
            System.out.println("MyApplicationLintener receives event source  \n" + event.getSource());
        }
    }

    private static class MyAppLicationEvent extends ApplicationEvent {
        /**
         * Create a new ApplicationEvent.
         *
         * @param source the object on which the event initially occurred (never {@code null})
         */
        public MyAppLicationEvent(Object source) {
            super(source);
        }
    }
}


```


<br>
<br>

* 理解SpringApplication
	* SpringApplication是Spring Boot引导启动类，与Spring上下文，事件，监听器以及环境等组件关系紧密，其中提供了控制Spring Boot应用特征的行为方法
* Spring Boot 应用运行监听器
	* SpringApplicationRunListener 


```java

package com.xuff.springcloudxuff01.event;

import org.springframework.context.ApplicationEvent;
import org.springframework.context.ApplicationListener;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;
import org.springframework.stereotype.Component;

/**
 * Spring 事件/监听器 Demo
 * Created by xufei
 * 2019/5/3
 */
public class SpringEventListenerDemo {
    public static void main(String[] args) {

        //AnnotationConfigApplicationContext context = new AnnotationConfigApplicationContext();
        AnnotationConfigApplicationContext context = new AnnotationConfigApplicationContext(MyApplicationListener.class);

        //增加监听器
        //context.addApplicationListener(new MyApplicationListener());

        //注册Configuration 的方式
        //context.register(MyApplicationListener.class);

        //上下文启动
        //context.refresh();

        //发布事件
        context.publishEvent(new MyAppLicationEvent("Hello World"));
    }

    @Component
    private static class MyApplicationListener implements ApplicationListener<MyAppLicationEvent> {

        @Override
        public void onApplicationEvent(MyAppLicationEvent event) {
            System.out.println("MyApplicationLintener receives event source  \n" + event.getSource());
        }
    }

    private static class MyAppLicationEvent extends ApplicationEvent {
        /**
         * Create a new ApplicationEvent.
         *
         * @param source the object on which the event initially occurred (never {@code null})
         */
        public MyAppLicationEvent(Object source) {
            super(source);
        }
    }
}

```

 ![enter description here](https://www.github.com/xufeifan1992/note/raw/master/images/201953/1556865734044.png)
 
 <br>
 <br>
 
 * 理解Spring Boot事件
	 * 事件触发器:EventPublishingRunListener
		 * ApplicaitonStartedEvent
		 * ApplicationEnvironmentPreparedEvent
		 * ApplicationPreparedEvent
		 * ApplicationReadyEvent/ApplicationFailedEvent
		 
<br>

![enter description here](https://www.github.com/xufeifan1992/note/raw/master/images/201953/1556866520872.png)

<br>
<br>

* 理解Spring Boot/Spring Cloud上下文层次关系
	* Spring Boot 上下文
		* 非web应用:AnnotationConfigApplicationContext
		* web应用：AnnotationConfigEmbeddedWebApplicationContext
	* Spring Cloud 上下文:Bootstrap(父）

<br>

访问beans端点

![enter description here](https://www.github.com/xufeifan1992/note/raw/master/images/201953/1556871919792.png)
![enter description here](https://www.github.com/xufeifan1992/note/raw/master/images/201953/1556871938625.png)

<br>

![enter description here](https://www.github.com/xufeifan1992/note/raw/master/images/201954/1556935980944.png)


<br>
<br>

#### Actuator Endpoints

<br>
<br>

* 理解Actuator Endpoints
	* Actuator 中文直译“传动装置”，在Spring Boot使用场景中表示为"生产而准备的特性"(Production-ready features),这些特性通过HTTP端口的形式，帮助相关人员管理和监控应用，大致上可以归类为：
		* 监控类：“断电信息”，“应用信息”，“外部话配置信息”，“指标信息”，“健康检查”，“Bean管理”，“Web URL映射管理”，“Web URL跟踪”
		* 管理类：“外部化配置”，“日志配置”，“线程dump”，“堆dump”，“关闭应用”

<br>

`注意：Spring Boot1.5开始Actuator增强了安全能力`

