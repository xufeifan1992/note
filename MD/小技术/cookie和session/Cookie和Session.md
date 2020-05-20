### Cookie和Session



#### Cookie

* 以键值对的形式存储信息在浏览器端
* cookie不能跨域，当前以及父级域名可以取值
* cookie可以设置有效期
* cookie可以设置path，path是一个路由，一般设置为/

#### Session

* 基于服务器内存的缓存(非持久化),可以保存请求会话
* 每个session通过sessionid来区分不同请求
* session可设置过期时间
* session也是以键值对形式存在的