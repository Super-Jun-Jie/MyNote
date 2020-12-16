# Docker命令
```text
拉取镜像：docker pull mysql
查看镜像：docker images
查看正在运行的容器：docker ps
查看所有的容器：docker ps
查看容器运行log：docker logs -f 容器id
```
### 制作java服务镜像
需要：jar文件、Dockerfile。  
```dockerfile
FROM java:8


VOLUME /tmp

ENV TZ=Asia/Shanghai
RUN ln -sf /usr/share/zoneinfo/{TZ} /etc/localtime && echo "{TZ}" > /etc/timezone

ADD javatx.jar /javatx.jar
RUN bash -c 'touch /javatx.jar'
ENTRYPOINT ["java","-jar","/javatx.jar"]
```
放在同目录下，然后cmd窗口，执行docker build -t 自定义镜像名 .   
举例：  
![](https://note-imge.oss-cn-beijing.aliyuncs.com/pasteimageintomarkdown/2020-12-16/165903906069800.png?Expires=4761682683&OSSAccessKeyId=LTAI4G1a9jwwXfvRfRgyzeZ3&Signature=Hj2pVoRih4nrTSZQ4aS25endM7c%3D)  
`docker build -t javatx .    //javatx是镜像名 后面空格 然后是个点 一个不能少`  




