#SpringBoot整合SpringSecurity一（入门初体验）
### SpringSecurity简介
SpringSecurity是基于Spring应用提供的声明式的安全保护性的框架，它可以在web请求级别的和方法调用级别处理身份和授权。他是基于AspectJ的切面进行配置的。
### 创建SpringBoot项目
####创建模块（这里选用gradle做项目的构建工具）

![](https://note-imge.oss-cn-beijing.aliyuncs.com/pasteimageintomarkdown/2020-08-12/424387068894100.png?Expires=4750818545&OSSAccessKeyId=LTAI4G1a9jwwXfvRfRgyzeZ3&Signature=dokiULLyN3rZQQuno%2B8Zv9%2Brxqk%3D)
####选择项目依赖 

![](https://note-imge.oss-cn-beijing.aliyuncs.com/pasteimageintomarkdown/2020-08-12/424405046148300.png?Expires=4750818563&OSSAccessKeyId=LTAI4G1a9jwwXfvRfRgyzeZ3&Signature=%2FE3m6OWmsaEtL1j5HBeZ%2Bi9WolQ%3D)

####自定义一个端口，然后运行项目
```properties
server.port=8888
```
直接运行会报如下错误：
```log
Error starting ApplicationContext. To display the conditions report re-run your application with 'debug' enabled.
2020-08-04 11:49:11.685 ERROR 1268 --- [  restartedMain] o.s.b.d.LoggingFailureAnalysisReporter   : 

***************************
APPLICATION FAILED TO START
***************************

Description:

Failed to configure a DataSource: 'url' attribute is not specified and no embedded datasource could be configured.

Reason: Failed to determine a suitable driver class


Action:

Consider the following:
	If you want an embedded database (H2, HSQL or Derby), please put it on the classpath.
	If you have database settings to be loaded from a particular profile you may need to activate it (no profiles are currently active).
```
这是由于我们导入了jpa的starter包，springboot有自动装配，需要注入datasource，为了节省时间，我们把内个依赖屏蔽掉，只需在@SpringBootApplication(exclued = '')配置即可

```java
@SpringBootApplication(exclude = {DataSourceAutoConfiguration.class})//这里是重点,不写的话你还得配置完数据库的设置才能启动
public class DemoApplication {
    public static void main(String[] args) {
        SpringApplication.run(DemoApplication.class, args);
    }
}
```

运行成功后我们注意控制台，有一串加密字符串，这是security生成的安全密码，在不做任何配置的情况下，需要用这个密码去登录，用户名为user 
![](https://note-imge.oss-cn-beijing.aliyuncs.com/pasteimageintomarkdown/2020-08-12/424416862396600.png?Expires=4750818575&OSSAccessKeyId=LTAI4G1a9jwwXfvRfRgyzeZ3&Signature=hURrRV2OTqfKLaP0OdDLjQClRe8%3D)

然后访问项目，会出现如下登录框。这个登录页面是security自动配置的，后期我们也可以替换掉它。至此，基本配置已经完成了。用户名为user 密码为控制台的加密字符串。
![](https://note-imge.oss-cn-beijing.aliyuncs.com/pasteimageintomarkdown/2020-08-12/424426538004300.png?Expires=4750818585&OSSAccessKeyId=LTAI4G1a9jwwXfvRfRgyzeZ3&Signature=vboNJ12hpJ6VYHQkwLrekIpF6hw%3D)

也可以在配置文件自定义账号密码
```properties
spring.security.user.name=root
spring.security.user.password=root
```
写一个配置类，继承WebSecurityConfigurerAdapter，实现configure(AuthenticationManagerBuilder auth)方法，也可以配置登录账户，代码如下：
```java
@Configuration
public class SpringSecurityConfig extends WebSecurityConfigurerAdapter {
    @Override
    protected void configure(AuthenticationManagerBuilder auth) throws Exception {
        //可以配置多个   用户1
        auth.inMemoryAuthentication().passwordEncoder(new BCryptPasswordEncoder())//申明加密的方式（不加密访问会报错）
                                     .withUser("root")//用户名
                                     .password(new BCryptPasswordEncoder().encode("123456"))//密码
                                     .roles();//角色，一会儿会讲
        //用户二
        auth.inMemoryAuthentication().passwordEncoder(new BCryptPasswordEncoder())//申明加密的方式（不加密访问会报错）
                .withUser("admin")//用户名
                .password(new BCryptPasswordEncoder().encode("123456"))//密码
                .roles();//角色，一会儿会讲
    }
}
```
定义一个controller方法，设置请求的角色
```java
@RestController
public class PageController {
    @GetMapping("/index")
    @PreAuthorize("hasAnyRole('admin')")
    public String index(){
        return "index请求成功";
    }
}
```
给用户设置角色，@EnableGlobalMethodSecurity注解很关键，如果不配置，则不生效
```java
@Configuration
@EnableGlobalMethodSecurity(prePostEnabled = true)//开启方法 认证
public class SpringSecurityConfig extends WebSecurityConfigurerAdapter {
    @Override
    protected void configure(AuthenticationManagerBuilder auth) throws Exception {
        //可以配置多个   用户1
        auth.inMemoryAuthentication().passwordEncoder(new BCryptPasswordEncoder())//申明加密的方式（不加密访问会报错）
                                     .withUser("admin")//用户名
                                     .password(new BCryptPasswordEncoder().encode("123456"))//密码
                                     .roles("admin");
    }
}
```
如果没有admin的角色，就会显示如下页面：
![](https://note-imge.oss-cn-beijing.aliyuncs.com/pasteimageintomarkdown/2020-08-12/424438710192400.png?Expires=4750818597&OSSAccessKeyId=LTAI4G1a9jwwXfvRfRgyzeZ3&Signature=gDKfaMVmMdtA4aZ3z1EKREUFop8%3D)

接下来设置通过数据库账号密码登录
```java
@Component//配置到容器里面
public class MyUserDetails implements UserDetailsService {
    private final UserService userService;
    private final PasswordEncoder passwordEncoder;

    public MyUserDetails(UserService userService,PasswordEncoder passwordEncoder) {
        this.userService = userService;
        this.passwordEncoder = passwordEncoder;
    }

    @Override
    public UserDetails loadUserByUsername(String username) throws UsernameNotFoundException {
        System.out.println(username);
        ArrayList<GrantedAuthority> authorities = new ArrayList<>();
        authorities.add(new SimpleGrantedAuthority("ROLE_"+"admin"));//把角色添加进去******~~~~__````__~~~~******
        User user = userService.findByUsername(username);//去数据库里面查询
        if (StringUtils.isEmpty(user)){
            throw new UsernameNotFoundException("用户不存在");
        }
        org.springframework.security.core.userdetails.User userdetails = new org.springframework.security.core.userdetails.User(user.getUsername(),passwordEncoder.encode(user.getPassword()),authorities);
        return userdetails;
    }
}
```

