#SpringBoot+SpringDataJpa配置多数据源
####问题场景
当一个大型的电商项目，既要保存订单的信息，又要保存日志，数据量请求过大，所有采用两个数据库存储数据，这就要求一个项目配置两个数据库去进行独写操作。
####技术点
SpringBoot、SpringDataJpa、多数据源
####创建过程
#####创建SpringBoot项目，勾选依赖,依赖如下：
```groovy
plugins {
    id 'org.springframework.boot' version '2.3.2.RELEASE'
    id 'io.spring.dependency-management' version '1.0.9.RELEASE'
    id 'java'
}

group = 'org.moredatabase'
version = '0.0.1-SNAPSHOT'
sourceCompatibility = '1.8'

configurations {
    compileOnly {
        extendsFrom annotationProcessor
    }
}

repositories {
    mavenCentral()
}

dependencies {
    implementation 'org.springframework.boot:spring-boot-starter-data-jpa'
    compileOnly 'org.projectlombok:lombok'
    developmentOnly 'org.springframework.boot:spring-boot-devtools'
    annotationProcessor 'org.projectlombok:lombok'
    testImplementation('org.springframework.boot:spring-boot-starter-test') {
        exclude group: 'org.junit.vintage', module: 'junit-vintage-engine'
    }
}

test {
    useJUnitPlatform()
}
```
#####配置文件,配置了两个数据库
```yaml
server:
  port: 8083
spring:
  datasource:
    db1:
      driver-class-name: com.mysql.cj.jdbc.Driver
      jdbc-url: jdbc:mysql://127.0.0.1:3306/test1?serverTimezone=UTC&allowMultiQueries=true&autoReconnect=true&useSSL=false
      username: root
      password: root
    db2:
      driver-class-name: com.mysql.cj.jdbc.Driver
      jdbc-url: jdbc:mysql://127.0.0.1:3306/test2?serverTimezone=UTC&allowMultiQueries=true&autoReconnect=true&useSSL=false
      username: root
      password: root
  jpa:
    hibernate:
      ddl-auto: update
    database-platform: org.hibernate.dialect.MySQL5InnoDBDialect
    show-sql: true
```
#####手动配置数据源，作为database注入容器中，@ConfigurationProperties把配置文件的内容转换成java实体类
```java
@Configuration
public class DatabaseConfig {
    @Bean//注入容器
    @ConfigurationProperties(prefix = "spring.datasource.db1")//把配置文件的内容转换成java实体类
    @Primary//主 数据源 只能由一个 其他的数据源不需要加此注解
    public DataSource dataSource1(){
        return DataSourceBuilder.create().build();
    }
    @Bean//注入容器
    @ConfigurationProperties(prefix = "spring.datasource.db2")//把配置文件的内容转换成java实体类
    public DataSource dataSource2(){
        return DataSourceBuilder.create().build();
    }
}
```
#####创建需要的实体类和repository，如下图所示
![](https://note-imge.oss-cn-beijing.aliyuncs.com/pasteimageintomarkdown/2020-08-11/333753205451900.png?Expires=4750727911&OSSAccessKeyId=LTAI4G1a9jwwXfvRfRgyzeZ3&Signature=ROSddLTkVtmOL6lKf8hox8YmKSs%3D)


代码详情
![](https://note-imge.oss-cn-beijing.aliyuncs.com/pasteimageintomarkdown/2020-08-11/333763274459000.png?Expires=4750727921&OSSAccessKeyId=LTAI4G1a9jwwXfvRfRgyzeZ3&Signature=%2B3T1AnaO9GffYP1n9KxFO8H3CBc%3D)

#####配置springdatajpa和数据源关联，例如orderRepository使用db1，logRepository使用db2，主要配置事务管理器和entry管理
```java
@Configuration
@EnableTransactionManagement
@EnableJpaRepositories(
        entityManagerFactoryRef="entityManagerFactoryDB1",
        transactionManagerRef="transactionManagerDB1",
        basePackages= { "org.moredatabase.order" }) //设置Repository所在位置
public class DB1Config {

    @Autowired
    @Qualifier("dataSource1")
    private DataSource dataSource1;

    @Autowired
    private JpaProperties jpaProperties;
    @Autowired
    private HibernateProperties hibernateProperties;

    private Map<String, Object> getVendorProperties() {
        return hibernateProperties.determineHibernateProperties(jpaProperties.getProperties(), new HibernateSettings());
    }
    @Primary
    @Bean(name = "entityManagerFactoryDB1")
    public LocalContainerEntityManagerFactoryBean entityManagerFactoryDB1 (EntityManagerFactoryBuilder builder) {
        return builder
                .dataSource(dataSource1)
                .packages("org.moredatabase.order") //设置实体类所在位置
                .properties(getVendorProperties())
                .build();
    }

    @Primary
    @Bean(name = "transactionManagerDB1")
    public PlatformTransactionManager transactionManagerPrimary(EntityManagerFactoryBuilder builder) {
        return new JpaTransactionManager(Objects.requireNonNull(entityManagerFactoryDB1(builder).getObject()));
    }
}
```

```java
@Configuration
@EnableTransactionManagement
@EnableJpaRepositories(
        entityManagerFactoryRef="entityManagerFactoryDB2",
        transactionManagerRef="transactionManagerDB2",
        basePackages= { "org.moredatabase.log" }) //设置Repository所在位置
public class DB2Config {

    @Autowired
    @Qualifier("dataSource2")
    private DataSource dataSource2;

    @Autowired
    private JpaProperties jpaProperties;
    @Autowired
    private HibernateProperties hibernateProperties;

    private Map<String, Object> getVendorProperties() {
        return hibernateProperties.determineHibernateProperties(jpaProperties.getProperties(), new HibernateSettings());
    }
    @Bean(name = "entityManagerFactoryDB2")
    public LocalContainerEntityManagerFactoryBean entityManagerFactoryDB2 (EntityManagerFactoryBuilder builder) {
        return builder
                //数据源
                .dataSource(dataSource2)
                //实体类位置
                .packages("org.moredatabase.log")
                .properties(getVendorProperties())
                .build();
    }

    @Bean(name = "transactionManagerDB2")
    public PlatformTransactionManager transactionManagerPrimary(EntityManagerFactoryBuilder builder) {
        return new JpaTransactionManager(Objects.requireNonNull(entityManagerFactoryDB2(builder).getObject()));
    }

}
```
温馨提示：order是保留字，不能用作表名，所以上面order的实体类表名改一下
#####测试
![](https://note-imge.oss-cn-beijing.aliyuncs.com/pasteimageintomarkdown/2020-08-11/333676692386600.png?Expires=4750727835&OSSAccessKeyId=LTAI4G1a9jwwXfvRfRgyzeZ3&Signature=c7lcQXc1wBHwe1nwyeiLifj%2BTKg%3D)

数据库数据如下：

![](https://note-imge.oss-cn-beijing.aliyuncs.com/pasteimageintomarkdown/2020-08-11/333688577753100.png?Expires=4750727847&OSSAccessKeyId=LTAI4G1a9jwwXfvRfRgyzeZ3&Signature=84vDz2bKOy%2FfG7ARGqlGOBh6TgY%3D)

