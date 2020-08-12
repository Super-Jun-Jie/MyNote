#linux系统中安装慢查询日志工具 pt-query-digest
###安装步骤
- 下载安装工具```wget percona.com/get/pt-query-digest```
- 授予用户执行权限```chmod u+x pt-query-digest``
- 安装与per相关的模块
```
yum install perl-DBI
yum install perl-Digest-MD5
```
- 查看是否安装成功`pt-query-digest -h`