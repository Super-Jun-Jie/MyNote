###springmvc如何接收请求体为xml格式的数据
设置produces=application/xml,参数上加注解@Request
```java
@Controller
@RequestMapping("/test")
public class PostController {
    @PostMapping(value = "/post",produces = "application/xml")
    @ResponseBody
    public String post(@RequestBody User user){
        System.out.println(user.toString());
        return "";
    }
}
```
默认xml的标签名对应字段的属性名，如果不匹配，可以通过@JacksonXmlProperty注解手动指定一下，例如
![](https://note-imge.oss-cn-beijing.aliyuncs.com/pasteimageintomarkdown/2020-08-21/302464487196500.png?Expires=4751591607&OSSAccessKeyId=LTAI4G1a9jwwXfvRfRgyzeZ3&Signature=iBctHyy06GOpRKpycxXLfh9oHAE%3D)
设置一下实体类的映射
![](https://note-imge.oss-cn-beijing.aliyuncs.com/pasteimageintomarkdown/2020-08-21/302532180605800.png?Expires=4751591675&OSSAccessKeyId=LTAI4G1a9jwwXfvRfRgyzeZ3&Signature=2jEFWtKMMKy%2F2eG8xk8GGnvss9Y%3D)
再次请求发现 username接收到值了
![](https://note-imge.oss-cn-beijing.aliyuncs.com/pasteimageintomarkdown/2020-08-21/302597998366600.png?Expires=4751591741&OSSAccessKeyId=LTAI4G1a9jwwXfvRfRgyzeZ3&Signature=Lkg7Qs9%2B7Z5aQ8KzqrgI3dWlsx8%3D)
