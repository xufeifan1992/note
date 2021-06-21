## Linux 切割日志脚本

  

```shell
log_path=/data/cbs/tomcat/tomcat-server/logs

d=`date -d'1 day ago' +%Y%m%d`
d30=`date -d'90 day ago' +%Y%m%d`
cd ${log_path} && cp catalina.out $log_path/catalina.$d.log
echo > catalina.out
rm -rf $log_path/catalina.out.${d30}.log                                  
```

