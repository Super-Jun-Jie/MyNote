###linux常用命令总结
- 查看正在运行的java程序的pid `jps`
- 杀死进程 `kill -9 pid`
- 重命名 `mv oldName newName`
- 查询进程pid `pgrep xxx`
- 查看端口 netstat -ntpl
- 移动文件`mv 目录1+文件  目录2+文件`
- 重命名`mv A文件 B文件`
- 删除文件`rm -rf 文件夹/目录`
- 查询所有开启的端口号`netstat -aptn`
- 使用tcp协议的端口`netstat -ntpl`
- 查询放行的端口`firewall-cmd --zone=public --list-ports`
- 新增放行端口`firewall-cmd --zone=public --add-port=3306/tcp --permanent`，之后刷新一下`firewall-cmd --reload`
- 查询文件：rpm -qa | grep -i mysql
- 删除文件:  rpm -e 文件名 --nodeps
- 创建文件夹：mkdir 文件夹名
- 解压：tar -xvf 压缩包名 -C 文件夹路径
####查看进程
- ps aux
- ps -le 
- ps -ef | grep redis

