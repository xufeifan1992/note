# Spring Cloud 负载均衡



![客户端负载均衡](https://github.com/xufeifan1992/note/blob/master/images/2019610/1560156500.jpg)



![服务端负载均衡](https://github.com/xufeifan1992/note/blob/master/images/2019610/1560156636.jpg)





* 调度算法
  * 先来先服务(First Come First Served)
  * 最早截至时间优先(Earliest deadline first)
  * 最短保留时间优先(Shortest remaining time first)
  * 固定优先级(Fixed Priority)
  * 轮询(Round-Robin)
  * 多级别队列(Multilevel Queue)
* 特性
  * 非对称负载
  * 分布式拒绝服务攻击保护
  * 直接服务返回
  * 健康检查
  * 优先级队列
  * 其他