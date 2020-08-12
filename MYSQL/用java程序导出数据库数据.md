#Java程序中实现 MySQL数据库的备份
####原理
是基于该命令实现的（前提是配置了mysql的环境变量）
```mysqldump -h(127.0.0.1) -p(3306) -u(root) -p(root) 库名 >保存的路径（尽量别c盘，有可能没有权限）```
例：
```cmd /c mysqldump -h127.0.0.1 -p3306 -uroot -proot hl> d://my2.sql```
####代码实现
```java
/**
 * @Author: 胡俊杰
 * @Date: 2020/8/12 17:52
 */
public class MYSQLDB {
    public static void main(String[] args) {
        dataBaseDump(   "127.0.0.1",
                        "3306",
                        "root",
                        "root",
                        "hl",
                        "e:\\",
                        "mysql");
    }
    /**
     *
     * @param host 主机名
     * @param port 端口
     * @param username 用户名
     * @param password 密码
     * @param databasename 数据库名
     * @param path 保存路径
     * @param sqlname sql文件名
     */
    public static void dataBaseDump(String host,String port,String username,String password,String databasename,String path,String sqlname){
        //执行备份sql的命令
        try {
            Process exec = Runtime.getRuntime().exec("cmd /c mysqldump -h"+host+" -p"+port+" -u"+username+" -p"+password+" hl> "+path+sqlname);
            if( exec.waitFor() == 0){
                System.out.println("数据库备份成功,备份路径为："+path+sqlname);
            }
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
```
