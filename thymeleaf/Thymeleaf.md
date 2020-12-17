# Spring Boot+Thymeleaf
虽然现在流行前后端分离，但是后端模版在一些关键地方还是非常有用的，例如邮件模版、代码模版等。当然也不排除一些古老的项目后端依然使用动态模版。
Thymeleaf 简洁漂亮、容易理解，并且完美支持 HTML5，可以直接打开静态页面，同时不新增标签，只需增强属性，这样也降低了学习成本。
### 1.Thymeleaf 简介
Thymeleaf 是新一代 Java 模板引擎，它类似于 Velocity、FreeMarker 等传统 Java 模板引擎，但是与传统 Java 模板引擎不同的是，Thymeleaf 支持 HTML 原型。

它既可以让前端工程师在浏览器中直接打开查看样式，也可以让后端工程师结合真实数据查看显示效果，同时，SpringBoot 提供了 Thymeleaf 自动化配置解决方案，因此在 SpringBoot 中使用 Thymeleaf 非常方便。

事实上， Thymeleaf 除了展示基本的 HTML ，进行页面渲染之外，也可以作为一个 HTML 片段进行渲染，例如我们在做邮件发送时，可以使用 Thymeleaf 作为邮件发送模板。

另外，由于 Thymeleaf 模板后缀为 .html，可以直接被浏览器打开，因此，预览时非常方便。

### 2. 整合 Spring Boot
#### 2.1基本用法
Spring Boot 中整合 Thymeleaf 非常容易，只需要创建项目时添加 Thymeleaf的依赖即可  
创建完成后的pom依赖  
```pom
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-thymeleaf</artifactId>
</dependency>
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
</dependency>
```
Thymeleaf 不仅仅能在 Spring Boot 中使用，也可以使用在其他地方，只不过 Spring Boot 针对 Thymeleaf 提供了一整套的自动化配置方案，这一套配置类的属性在 
`org.springframework.boot.autoconfigure.thymeleaf.ThymeleafProperties` 中，部分源码如下：  
```java
@ConfigurationProperties(prefix = "spring.thymeleaf")
public class ThymeleafProperties {
        private static final Charset DEFAULT_ENCODING = StandardCharsets.UTF_8;//默认编码
        public static final String DEFAULT_PREFIX = "classpath:/templates/";//视图解析器前缀
        public static final String DEFAULT_SUFFIX = ".html";//视图解析器后缀
        private boolean checkTemplate = true;
        private boolean checkTemplateLocation = true;
        private String prefix = DEFAULT_PREFIX;
        private String suffix = DEFAULT_SUFFIX;
        private String mode = "HTML";
        private Charset encoding = DEFAULT_ENCODING;
        private boolean cache = true;
        //...
}
```
1. 首先通过 **@ConfigurationProperties** 注解，将 **application.properties** 前缀为 **spring.thymeleaf** 的配置和这个类中的属性绑定。
2. 前三个 static 变量定义了默认的编码格式、视图解析器的前缀、后缀等。
3. 从前三行配置中，可以看出来，Thymeleaf 模板的默认位置在 **resources/templates** 目录下，默认的后缀是 html。
4. 这些配置，如果开发者不自己提供，则使用 默认的，如果自己提供，则在 **application.properties** 中以 **spring.thymeleaf** 开始相关的配置。  
而我们刚刚提到的，Spring Boot 为 Thymeleaf 提供的自动化配置类，则是 **org.springframework.boot.autoconfigure.thymeleaf.ThymeleafAutoConfiguration** ，部分源码如下：
```java
@Configuration
@EnableConfigurationProperties(ThymeleafProperties.class)
@ConditionalOnClass({ TemplateMode.class, SpringTemplateEngine.class })
@AutoConfigureAfter({ WebMvcAutoConfiguration.class, WebFluxAutoConfiguration.class })
public class ThymeleafAutoConfiguration {
}
```
可以看到，在这个自动化配置类中，首先导入 ThymeleafProperties ，然后 @ConditionalOnClass 注解表示当当前系统中存在 TemplateMode 和 SpringTemplateEngine 类时，当前的自动化配置类才会生效，即只要项目中引入了 Thymeleaf 相关的依赖，这个配置就会生效。  

这些默认的配置我们几乎不需要做任何更改就可以直接使用了。如果开发者有特殊需求，则可以在 application.properties 中配置以 spring.thymeleaf 开头的属性即可。
自动渲染不在叙述
#### 2.2手动渲染
前面我们说的是返回一个 Thymeleaf 模板，我们也可以手动渲染 Thymeleaf 模板，这个一般在邮件发送时候有用，例如我在 resources/templates 目录下新建一个邮件模板，如下：  
```html
<!DOCTYPE html>
<html lang="en" xmlns:th="http://www.thymeleaf.org">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
<p>hello 欢迎 <span th:text="${username}"></span>加入 XXX 集团，您的入职信息如下：</p>
<table border="1">
    <tr>
        <td>职位</td>
        <td th:text="${position}"></td>
    </tr>
    <tr>
        <td>薪水</td>
        <td th:text="${salary}"></td>
    </tr>
</table>
<img src="http://www.javaboy.org/images/sb/javaboy.jpg" alt="">
</body>
</html>
```  
这一个 HTML 模板中，有几个变量，我们要将这个 HTML 模板渲染成一个 String 字符串，再把这个字符串通过邮件发送出去，那么如何手动渲染呢？  
```java
@Autowired
public class Test{
   TemplateEngine templateEngine;
   @Test
   public void test1() throws MessagingException {
      Context context = new Context();
      context.setVariable("username", "javaboy");
      context.setVariable("position", "Java工程师");
      context.setVariable("salary", 99999);
      String mail = templateEngine.process("mail", context);
      //省略邮件发送
   } 
}

```
1. 渲染时，我们需要首先注入一个 TemplateEngine 对象，这个对象就是在 Thymeleaf 的自动化配置类中配置的（即当我们引入 Thymeleaf 的依赖之后，这个实例就有了）。
2. 然后构造一个 Context 对象用来存放变量。
3. 调用 process 方法进行渲染，该方法的返回值就是渲染后的 HTML 字符串，然后我们将这个字符串发送出去。
### 3. Thymeleaf 细节
#### 3.1 标准表达式语法
3. 1.1 简单表达式  
   `${...}`  `*{...}`  `#{...}`  #{...}用于获取国际化语言翻译值    
   

在 resources 目录下新建两个文件：messages.properties 和 messages_zh_CN.properties，内容如下：  
messages.properties：  
`message = smile-hu`  
messages_zh_CN.properties：  
`message = 胡俊杰`  
然后在 thymeleaf 中引用 message，系统会根据浏览器的语言环境显示不同的值`<div th:text="#{message}"></div>`  
```text
@{...} 
引用绝对URl：<script type="text/javascript" th:src="@{http://localhost:8080/hello.js}"></script> 
等价于：<script type="text/javascript" src="http://localhost:8080/hello.js"></script>
server.servlet.context-path=/myapp  上下文配置
url：<script type="text/javascript" th:src="@{/hello.js}"></script>等价于<script type="text/javascript" src="/myapp/hello.js"></script>
相对URL：<script type="text/javascript" th:src="@{~/hello.js}"></script>等价于<script type="text/javascript" src="/hello.js"></script>
应用程序的上下文/myapp将被忽略
带参数的url
<script type="text/javascript" th:src="@{//localhost:8080/hello.js(name='javaboy',age=99)}"></script>
等价于
<script type="text/javascript" th:src="//localhost:8080/hello.js?name=javaboy&age=99"></script>
```
`~{...}`  
片段表达式是 Thymeleaf 的特色之一，细粒度可以达到标签级别，这是 JSP 无法做到的。片段表达式拥有三种语法：  
- `~{ viewName }`：表示引入完整页面
- `~{ viewName ::selector}`：表示在指定页面寻找片段，其中 selector 可为片段名、jquery选择器等
- `~{ ::selector}`：表示在当前页寻找  
通过`th:fragment`标签和`th:replace`标签    

```html
<div th:with="age=(99*99/99+99-1)">
    <div th:text="${age}"></div>
</div>
```  
th:with 定义了一个局部变量 age，在其所在的 div 中可以使用该局部变量
##### 3.1.5 布尔运算
- 二元运算符：and, or
- 布尔非（一元运算符）：!, not  
```html
<div th:with="age=(99*99/99+99-1)">
    <div th:text="9 eq 9 or 8 ne 8"></div>
    <div th:text="!(9 eq 9 or 8 ne 8)"></div>
    <div th:text="not(9 eq 9 or 8 ne 8)"></div>
</div>
```  
##### 3.1.6 比较和相等
表达式里的值可以使用 >, <, >= 和 <= 符号比较。== 和 != 运算符用于检查相等（或者不相等）。注意 XML规定 < 和 > 标签不能用于属性值，所以应当把它们转义为 &lt; 和 &gt;。

如果不想转义，也可以使用别名：gt (>)；lt (<)；ge (>=)；le (<=)；not (!)。还有 eq (==), neq/ne (!=)。  
```html
<div th:with="age=(99*99/99+99-1)">
    <div th:text="${age} eq 197"></div>
    <div th:text="${age} ne 197"></div>
    <div th:text="${age} ge 197"></div>
    <div th:text="${age} gt 197"></div>
    <div th:text="${age} le 197"></div>
    <div th:text="${age} lt 197"></div>
</div>
```
##### 3.1.7 条件运算符
```html
<div th:with="age=(99*99/99+99-1)">
    <div th:text="(${age} ne 197)?'yes':'no'"></div>
</div>
```
其中，: 后面的部分可以省略，如果省略了，又同时计算结果为 false 时，将返回 null。
##### 3.1.8 内置对象
基本内置对象
- #ctx：上下文对象。
- #vars: 上下文变量。
- #locale：上下文区域设置。
- #request：（仅在 Web 上下文中）HttpServletRequest 对象。
- #response：（仅在 Web 上下文中）HttpServletResponse 对象。
- #session：（仅在 Web 上下文中）HttpSession 对象。
- #servletContext：（仅在 Web 上下文中）ServletContext 对象。
举例：`<div th:text='${#session.getAttribute("name")}'></div>`  

实用内置对象：  
- #execInfo：有关正在处理的模板的信息。
- #messages：在变量表达式中获取外部化消息的方法，与使用＃{...}语法获得的方式相同。
- #uris：转义URL / URI部分的方法
- #conversions：执行配置的转换服务（如果有）的方法。
- #dates：java.util.Date对象的方法：格式化，组件提取等
- #calendars：类似于#dates但是java.util.Calendar对象。
- #numbers：用于格式化数字对象的方法。
- #strings：String对象的方法：contains，startsWith，prepending / appending等
- #objects：一般对象的方法。
- #bools：布尔评估的方法。
- #arrays：数组方法。
- #lists：列表的方法。
- #sets：集合的方法。
- #maps：地图方法。
- #aggregates：在数组或集合上创建聚合的方法。
- #ids：处理可能重复的id属性的方法（例如，作为迭代的结果）。
举例：
```html
<div th:text="${#execInfo.getProcessedTemplateName()}"></div>
<div th:text="${#arrays.length(#request.getAttribute('names'))}"></div>
```
#### 3.2 设置属性值
这个是给 HTML 元素设置属性值。可以一次设置多个，多个之间用 , 分隔开  
例如： 
`<img th:attr="src=@{/1.png},title=${user.username},alt=${user.username}">`  
会被渲染成  

`<img src="/myapp/1.png" title="javaboy" alt="javaboy">`  

但是可读性不太好，Thymeleaf 还支持在每一个原生的 HTML 属性前加上 th: 前缀的方式来使用动态值  
`<img th:src="@{/1.png}" th:alt="${user.username}" th:title="${user.username}">`
#### 3.2 遍历
数组/集合/Map/Enumeration/Iterator 等的遍历也算是一个非常常见的需求，Thymeleaf 中通过 th:each 来实现遍历，像下面这样：  
```html
<table border="1">
    <tr th:each="u : ${users}">
        <td th:text="${u.username}"></td>
        <td th:text="${u.address}"></td>
    </tr>
</table>
```  
users 是要遍历的集合/数组，u 则是集合中的单个元素。  

遍历的时候，我们可能需要获取遍历的状态，Thymeleaf 也对此提供了支持：  
- index：当前的遍历索引，从0开始。
- count：当前的遍历索引，从1开始。
- size：被遍历变量里的元素数量。
- current：每次遍历的遍历变量。
- even/odd：当前的遍历是偶数次还是奇数次。
- first：当前是否为首次遍历。
- last：当前是否为最后一次遍历。  

u 后面的 state 表示遍历状态，通过遍历状态可以引用上面的属性。  
```html
<table border="1">
    <tr th:each="u,state : ${users}">
        <td th:text="${u.username}"></td>
        <td th:text="${u.address}"></td>
        <td th:text="${state.index}"></td>
        <td th:text="${state.count}"></td>
        <td th:text="${state.size}"></td>
        <td th:text="${state.current}"></td>
        <td th:text="${state.even}"></td>
        <td th:text="${state.odd}"></td>
        <td th:text="${state.first}"></td>
        <td th:text="${state.last}"></td>
    </tr>
</table>
```
#### 3.4 分支语句  
```html
<table border="1">
    <tr th:each="u,state : ${users}" th:if="${state.odd}">
        <td th:text="${u.username}"></td>
        <td th:text="${u.address}"></td>
        <td th:text="${state.index}"></td>
        <td th:text="${state.count}"></td>
        <td th:text="${state.size}"></td>
        <td th:text="${state.current}"></td>
        <td th:text="${state.even}"></td>
        <td th:text="${state.odd}"></td>
        <td th:text="${state.first}"></td>
        <td th:text="${state.last}"></td>
    </tr>
</table>
``` 
th:if 不仅仅只接受布尔值，也接受其他类型的值，例如如下值都会判定为 true：  
- 如果值是布尔值，并且为 true。
- 如果值是数字，并且不为 0。
- 如果值是字符，并且不为 0。
- 如果值是字符串，并且不为 “false”， “off” 或者 “no”。
- 如果值不是布尔值，数字，字符或者字符串。 
  
但是如果值为 null，th:if 会求值为 false。  

th:unless 的判定条件则与 th:if 完全相反。
```html

<table border="1">
    <tr th:each="u,state : ${users}" th:unless="${state.odd}">
        <td th:text="${u.username}"></td>
        <td th:text="${u.address}"></td>
        <td th:text="${state.index}"></td>
        <td th:text="${state.count}"></td>
        <td th:text="${state.size}"></td>
        <td th:text="${state.current}"></td>
        <td th:text="${state.even}"></td>
        <td th:text="${state.odd}"></td>
        <td th:text="${state.first}"></td>
        <td th:text="${state.last}"></td>
    </tr>
</table>
```
这个显示效果则与上面的完全相反。  

当可能性比较多的时候，也可以使用 switch：  
```html
<table border="1">
    <tr th:each="u,state : ${users}">
        <td th:text="${u.username}"></td>
        <td th:text="${u.address}"></td>
        <td th:text="${state.index}"></td>
        <td th:text="${state.count}"></td>
        <td th:text="${state.size}"></td>
        <td th:text="${state.current}"></td>
        <td th:text="${state.even}"></td>
        <td th:text="${state.odd}"></td>
        <td th:text="${state.first}"></td>
        <td th:text="${state.last}"></td>
        <td th:switch="${state.odd}">
            <span th:case="true">odd</span>
            <span th:case="*">even</span>
        </td>
    </tr>
</table>
```  
**th:case="*"** 则表示默认选项。

#### 3.5 本地变量
这个我们前面已经涉及到了，使用 th:with 可以定义一个本地变量。

#### 3.6 内联
```html
<div>hello [[${user.username}]]</div>
```
[[...]] 对应于 th:text （结果会是转义的 HTML），[(...)]对应于 th:utext，它不会执行任何的 HTML 转义。

也可以在 js 或者 css 中使用内联，以 js 为例，使用方式如下
```html
<script th:inline="javascript">
    var username=[[${user.username}]]
    console.log(username)
</script>
```
