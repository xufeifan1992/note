# Nginx



* Nginx是一个高性能的HTTP和反向代理web服务器，同时也提供IMAP/POP3/SMTP服务
* 主要功能反向代理
* 通过配置文件可以实现集群和负载均衡
* 静态资源虚拟化





## 常见的服务器

* MS IIS                       *asp.net*
* Weblogic  ，Jboss  *传统行业  ERP/物流/金融/电信*
* Tomcat ，Jetty        *J2EE*
* Apache，Nginx      *静态服务*
* Netty                        *高性能服务器编程*







## 反向代理/正向代理

### 正向代理

* 客户端请求目标服务器之间的的一个代理服务器
* 请求会先经过代理服务器，然后再转发请求到目标服务器，获得内容后最后响应给客户端

### 反向代理

* 用户请求目标服务器，由代理服务器决定访问哪个IP



### 安装Nginx



1.(1).安装gcc环境

```java
 yum install gcc-c++
```

(2)安装PCRE库，用于解析正则表达式

```java
 yum install -y pcre pcre-devel
```

(3)zlib压缩和解压缩依赖，

```
 yum install -y zlib zlib-devel
```

(4)SSL 安全的加密的套接字协议层，用于HTTP安全传输，也就是https

```
 yum install -y openssl openssl-devel
```



2.解压，需要注意，解压后得到的是源码，源码需要编译后才能安装

```
tar -zxvf nginx-1.16.1.tar.gz
```



3.编译之前，先创建nginx临时目录，如果不创建，在启动nginx的过程中会报错

```
mkdir /var/temp/nginx -p
```



4.在nginx目录，输入如下命令进行配置，目的是为了创建makefile文件

```xml
./configure 
--prefix=/usr/local/nginx 
--pid-path=/var/run/nginx/nginx.pid 
--lock-path=/var/lock/nginx.lock 
--error-log-path=/var/log/nginx/error.log 
--http-log-path=/var/log/nginx/access.log 
--with-http_gzip_static_module 
--http-client-body-temp-path=/var/temp/nginx/client 
--http-proxy-temp-path=/var/temp/nginx/proxy 
--http-fastcgi-temp-path=/var/temp/nginx/fastcgi 
--http-uwsgi-temp-path=/var/temp/nginx/uwsgi 
--http-scgi-temp-path=/var/temp/nginx/scgi
```

5.make编译

```
make
```

6.安装

```
make install
```

7.进入sbin目录启动nginx

```
./nginx
```

- 停止：./nginx -s stop
- 重新加载：./nginx -s reload





### Nginx配置命令





| 命令                          | 解释                                 |
| :---------------------------- | :----------------------------------- |
| –prefix                       | 指定nginx安装目录                    |
| –pid-path                     | 指向nginx的pid                       |
| –lock-path                    | 锁定安装文件，防止被恶意篡改或误操作 |
| –error-log                    | 错误日志                             |
| –http-log-path                | http日志                             |
| –with-http_gzip_static_module | 启用gzip模块，在线实时压缩输出数据流 |
| –http-client-body-temp-path   | 设定客户端请求的临时目录             |
| –http-proxy-temp-path         | 设定http代理临时目录                 |
| –http-fastcgi-temp-path       | 设定fastcgi临时目录                  |
| –http-uwsgi-temp-path         | 设定uwsgi临时目录                    |
| –http-scgi-temp-path          | 设定scgi临时目录                     |



## Nginx的进程

* master进程:主进程

* worker进程:工作进程

  ```java
  #user  nobody;
  worker_processes  1;//工作进程，可以调整
  
  #error_log  logs/error.log;
  #error_log  logs/error.log  notice;
  #error_log  logs/error.log  info;
  ```

  

* 信号
  * ./nginx -s stop
  * ./nginx -s quit
  * ./nginx -s reload
  * ./nginx -t



## Worker抢占机制以及事件处理

*master主进程会fork多个worker进程，客户端请求进入nginx时，有一个互斥锁头，抢占accept_mutex锁，抢到就为客户端处理请求，捕获，解析，处理，响应，处理完毕后响应客户端,nginx使用epoll机制，linux的epoll模型，进行异步非阻塞处理* 



```java
nginx.conf文件

events {
    # 默认使用epoll
    user epoll;
    # 每个worker允许连接的客户端最大连接数
    worker_connections  1024;
}
```



### nginx.conf配置结构

* main 全局配置

* event 配置工作模式一级连接数
* http http模块相关配置
  * server 虚拟主机配置，可以有多个
  * location 路由规则，表达式
  * upstream 集群，内网服务器，负载均衡规则





##  nginx配置文件详情

1. 设置worker进程的用户，指的linux中的用户，会涉及到nginx操作目录或文件的一些权限，默认为`nobody`

   ```
   user root;
   ```

2. worker进程工作数设置，一般来说CPU有几个，就设置几个，或者设置为N-1也行

   ```
   worker_processes 1;
   ```

3. nginx 日志级别`debug | info | notice | warn | error | crit | alert | emerg`，错误级别从左到右越来越大

4. 设置nginx进程 pid

   ```
   pid        logs/nginx.pid;
   ```

5. 设置工作模式

   ```
   events {
       # 默认使用epoll
       use epoll;
       # 每个worker允许连接的客户端最大连接数
       worker_connections  10240;
   }
   ```

6. http 是指令块，针对http网络传输的一些指令配置

   ```
   http {
   }
   ```

7. include 引入外部配置，提高可读性，避免单个配置文件过大

   ```
   include       mime.types;
   ```

8. 设定日志格式，`main`为定义的格式名称，如此 access_log 就可以直接使用这个变量了
   ![图片描述](https://climg.mukewang.com/5e4265f508e8724d15360230.jpg)

   | 参数名                | 参数意义                             |
   | :-------------------- | :----------------------------------- |
   | $remote_addr          | 客户端ip                             |
   | $remote_user          | 远程客户端用户名，一般为：’-’        |
   | $time_local           | 时间和时区                           |
   | $request              | 请求的url以及method                  |
   | $status               | 响应状态码                           |
   | $body_bytes_send      | 响应客户端内容字节数                 |
   | $http_referer         | 记录用户从哪个链接跳转过来的         |
   | $http_user_agent      | 用户所使用的代理，一般来时都是浏览器 |
   | $http_x_forwarded_for | 通过代理服务器来记录客户端的ip       |

9. `sendfile`使用高效文件传输，提升传输性能。启用后才能使用`tcp_nopush`，是指当数据表累积一定大小后才发送，提高了效率。

   ```
   sendfile        on;
   tcp_nopush      on;
   ```

10. `keepalive_timeout`设置客户端与服务端请求的超时时间，保证客户端多次请求的时候不会重复建立新的连接，节约资源损耗。

```
#keepalive_timeout  0;
keepalive_timeout  65;
```

1. `gzip`启用压缩，html/js/css压缩后传输会更快

   ```
   gzip on;
   ```

2. `server`可以在`http`指令块中设置多个虚拟主机

   - listen 监听端口
   - server_name localhost、ip、域名
   - location 请求路由映射，匹配拦截
   - root 请求位置
   - index 首页设置

   ```xml
       server {
               listen       88;
               server_name  localhost;
       
               location / {
                   root   html;
                   index  index.html index.htm;
               }
       }
   ```





### Nginx常用命令

* ./nginx -s stop *强制关闭nginx(不太好的方式)*

* ./nginx -s quit *比较有好的退出nginx*

* ./nginx -t *检测配置文件*

* ./nginx -v *查看nginx版本号*

* ./nginx -V *详细查看nginx具体信息*

* ./nginx -? *提供帮助*

  * 

  ```java
  [root@localhost sbin]# ./nginx -?
  nginx version: nginx/1.16.1
  Usage: nginx [-?hvVtTq] [-s signal] [-c filename] [-p prefix] [-g directives]
  
  Options:
    -?,-h         : this help
    -v            : show version and exit
    -V            : show version and configure options then exit
    -t            : test configuration and exit
    -T            : test configuration, dump it and exit
    -q            : suppress non-error messages during configuration testing
    -s signal     : send signal to a master process: stop, quit, reopen, reload
    -p prefix     : set prefix path (default: /usr/local/nginx/)
    -c filename   : set configuration file (default: conf/nginx.conf)
    -g directives : set global directives out of configuration file
      
  ```



## Nginx日志切割-定时

### 使用定时任务

1. 安装定时任务：

   ```
   yum install crontabs
   ```

2. `crontab -e` 编辑并且添加一行新的任务：

   ```
   */1 * * * * /usr/local/nginx/sbin/cut_my_log.sh
   ```

3. 重启定时任务：

   ```
   service crond restart
   ```

- 附：常用定时任务命令：

  ```
  service crond start         //启动服务
  service crond stop          //关闭服务
  service crond restart       //重启服务
  service crond reload        //重新载入配置
  crontab -e                  // 编辑任务
  crontab -l                  // 查看任务列表
  ```

### 定时任务表达式：

Cron表达式是，分为5或6个域，每个域代表一个含义，如下所示：

|          | 分   | 时   | 日   | 月   | 星期几 | 年（可选）       |
| :------- | :--- | :--- | :--- | :--- | :----- | :--------------- |
| 取值范围 | 0-59 | 0-23 | 1-31 | 1-12 | 1-7    | 2019/2020/2021/… |

### 常用表达式：

- 每分钟执行：

  ```
  */1 * * * *
  ```

- 每日凌晨（每天晚上23:59）执行：

  ```
  59 23 * * *
  ```

- 每日凌晨1点执行：

  ```
  0 1 * * *
  ```

# root 与 alias

假如服务器路径为：/home/imooc/files/img/face.png

- root 路径完全匹配访问
  配置的时候为：

  ```xml
  location /imooc {
      root /home
  }
  ```

用户访问的时候请求为：`url:port/imooc/files/img/face.png`

- alias 可以为你的路径做一个别名，对用户透明
  配置的时候为：

  ```xml
  location /hello {
      alias /home/imooc
  }
  ```

  用户访问的时候请求为：`url:port/hello/files/img/face.png`，如此相当于为目录`imooc`做一个自定义的别名。