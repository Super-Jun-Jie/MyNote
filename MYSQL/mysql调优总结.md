#MYSQL调优总结
###定位查询时间过长的sql语句
####一、查看慢查询记录
- 首先看是否开启的慢查询日志记录（mysql5.7之后默认是开启了）
![](https://note-imge.oss-cn-beijing.aliyuncs.com/pasteimageintomarkdown/2020-08-08/84575161715600.png?Expires=4750478732&OSSAccessKeyId=LTAI4G1a9jwwXfvRfRgyzeZ3&Signature=ujPMpuM4ypW4lMea2jUlPY9Em9U%3D)
- 使用命令开启 `set global slow_qeury_log = on;`
![](https://note-imge.oss-cn-beijing.aliyuncs.com/pasteimageintomarkdown/2020-08-08/84717162418900.png?Expires=4750478874&OSSAccessKeyId=LTAI4G1a9jwwXfvRfRgyzeZ3&Signature=hkBIWaOXLRXbbKTH3Y5IjMIsp9k%3D)
- 查看记录慢查询的超时时间，超过多少秒的查询才会被记录,我这里默认是10秒，我改的小一点，例如1秒(查询时间超过一秒才会记录，注意是大于一秒，等于不算),改完之后没有变化需要重新打开一下回话即可。（基于命令修改的只对当前生效，mysql重启之后就会失效，如果想永久生效，需要手动修改配置文件my.in）
![](https://note-imge.oss-cn-beijing.aliyuncs.com/pasteimageintomarkdown/2020-08-08/85225854809300.png?Expires=4750479382&OSSAccessKeyId=LTAI4G1a9jwwXfvRfRgyzeZ3&Signature=BzNtXcgJmb9lzh%2BxukW93ZfIcC8%3D)
- 查看慢查询日记存放的位置
![](https://note-imge.oss-cn-beijing.aliyuncs.com/pasteimageintomarkdown/2020-08-08/85328556903300.png?Expires=4750479485&OSSAccessKeyId=LTAI4G1a9jwwXfvRfRgyzeZ3&Signature=olIrKR8ohLBwBbgblKxOENfuInk%3D)
- 慢查询日志内容解析
![](https://note-imge.oss-cn-beijing.aliyuncs.com/pasteimageintomarkdown/2020-08-08/87024186711300.png?Expires=4750481181&OSSAccessKeyId=LTAI4G1a9jwwXfvRfRgyzeZ3&Signature=RMT1sctkuacHevh2uSR774rzhkw%3D)
-- 通过查看慢查询的记录次数，也可以判断程序的好坏'show variables like 'slow_queries%''
![](https://note-imge.oss-cn-beijing.aliyuncs.com/pasteimageintomarkdown/2020-08-08/88065933283700.png?Expires=4750482223&OSSAccessKeyId=LTAI4G1a9jwwXfvRfRgyzeZ3&Signature=Vkb6cPAzxIyIgb4X8LGjdbVu0Bg%3D)
- 调优的原理就是让查询的时间变小，所有接下来筛选出查询最慢的sql语句(使用mysqldumpslow工具查询)
使用mysqldumpslow之前要先安装perl环境




