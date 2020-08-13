#thymeleaf用法
###页面公共资源复用
####所用标签
- th:fragment(定义的资源片段)
- th:include(类似于 th:insert，⽽不是插⼊⽚段，它只插⼊此⽚段的内容)
- th:replace(将被引用的模板片段替换掉自己)
- th:insert(将被引用的模板片段插⼊到自己的标签体中)
####代码实现
fragment片段
```html
<!DOCTYPE html>
<html lang="en" xmlns:th="http://www.thymeleaf.org">
<head th:fragment="head">
    <title>smile-hu个人博客 - 一个倔强的专科少年</title>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <link rel="stylesheet" th:href=" @{/css/base.css}"/>
    <link rel="stylesheet" th:href=" @{/css/index.css}"/>
    <link rel="stylesheet" th:href=" @{/css/m.css}"/>
    <!--[if lt IE 9]>
    <script th:src="@{/js/modernizr.js}"></script>
    <![endif]-->
    <script th:src="@{/js/nav.js}"></script>
</head>
</html>
```
调用片段
```html
<head th:include="view/resource :: head"></head>
```
