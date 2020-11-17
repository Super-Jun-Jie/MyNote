#SpringBoot整合mybatis(动态sql的使用)
###一、准备
依赖
```pom
        <dependency>
            <groupId>org.mybatis.spring.boot</groupId>
            <artifactId>mybatis-spring-boot-starter</artifactId>
            <version>2.1.4</version>
        </dependency>
```
在启动类上加注解
```java
@SpringBootApplication
@MapperScan("org.springboot.data.mapper")//mybatis的注解，指定接口的位置
public class DataApplication {
    public static void main(String[] args) {
        SpringApplication.run(DataApplication.class,args);
    }
}
```
配置
```properties
mybatis.configuration.map-underscore-to-camel-case=true   驼峰标识转换，例如 数据库字段id_card映射到实体类上为idCard
```
###二、使用
感觉使用注解比较方便，直接在接口中的方法上加注解@Select @Update @Delete进行数据库操作
```java
public interface UserMapper {
    @Select("select * from user")
    List<User> queryAll();
}
```
###三、动态sql的使用
通过注解使用动态sql的时候,语句必须被<script></script>包住
1. 批量新增
insert into tableName (column1,column2...) values (),(),()...
```java
public interface UserMapper{
    @Insert("<script> " +
            "insert into user (name,age,id_card,address) values " +
            "<foreach collection='list' item='user' separator=','> " +
            "(#{user.name},#{user.age},#{user.idCard},#{user.address})" +
            "</foreach>" +
            "</script>")
    int userSave(User ... list);
}
```
2. 批量更新
