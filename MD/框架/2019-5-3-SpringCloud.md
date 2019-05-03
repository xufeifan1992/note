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
<br>

1.Codebase   代码管理  svn git
2.Dependencies  依赖  maven gradle jvm等
3.Config  配置 外部化配置(启动参数,文件等) 内部化配置(代码修改)
4.Backing Services  后端服务
5.Build,release,run  构建，发布，运行
6.Processes  分布式进程
7.Port binding  端口绑定
8.Concurrency 并发 
9.Disposability 易处理
10.Dev/prod party 环境等价
11.Logs 日志
12.Admin processes  管理进程

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

 