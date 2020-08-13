#SpringBoot+SpringTask定时器
###一般工作中会用到一些定时任务，比如定时去批量的刷新数据，发送邮件，发端短信等，这就用到了定时器
很简单的两部
- 在启动类上添加@EnableScheduling注解
- 定义一个类，注入到容器中，然后在方法上加注解@Scheduled(cron时间表达式即可)，可以同时开启多个定时任务，项目之间不会有影响
```java
@Component
public class WordTask {
    @Scheduled(cron = "0/20 * * * * *")
    public void scheduled() {
        System.out.println("定时任务正在执行");
    }
}
```
- cron表达式详解
