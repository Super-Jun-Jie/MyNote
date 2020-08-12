#linux（centos） 系统安装jdk
###方法一 yum安装
- 查看java的所有版本：yum list java*
- 选择一个版本 yum install java-1.8.0-openjdk.x86_64
- 配置环境变量：修改/etc/profile文件，配置java的环境变量
```log
JAVA_HOME=/usr/lib/jvm/java-1.8.0-openjdk-1.8.0.191.b12-1.el7_6.x86_64 //修改为实际的路径，其他地方不用动
JRE_HOME=$JAVA_HOME/jre
CLASS_PATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar:$JRE_HOME/lib
PATH=$PATH:$JAVA_HOME/bin:$JRE_HOME/bin
export JAVA_HOME JRE_HOME CLASS_PATH PATH
```
- 修改完，使用命令让修改的资源配置生效 ```source /etc/profile```

###方法二 手动下载解压安装
- https://www.oracle.com/java/technologies/javase-jdk11-downloads.html#license-lightbox 下载之后上传到linux中，zxvf解压，然后配置环境变量即可
 