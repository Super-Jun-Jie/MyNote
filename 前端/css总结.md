#CSS（层叠样式表）总结

###选择器
- 标签选择器 例如 p img div等等
- class选择器 例如.mybox
- id选择器 例如#myid
###添加css的方法
- 外部样式表   
保存在.css文件中
- 内部样式表  
放在<style》里面 
- 内联合样式（不推荐使用，维护比较繁琐）
只影响一个元素，在style属性中添加
### 技巧
- div居中可以设置margin : auto，div里面设置color 可以修改字体的颜色
- font-family设置字体
- line-height 行高 
- 外边距塌陷 重叠 （表示不会有两个外边距同时生效）
- margin:5 10 5 10 分别设置了上右下左
- margin：5 10 分别设置了上下 左右的数据
- margin：5 10 5 设置上5 左右都为10 下5
- margin:5 设置上下左右都为5
- border:border-left 边框粗细、颜色 线
- border-radius 30px 设置圆角
- li标签的样式  可以用小图片代理 给li设置list-style-image属性 
- 按钮的状态 设置鼠标放上去的状态 `button:hover{/*里面是样式*/}`
- 移除a标签的下划线  设置 text-decoration:none,a标签也可以设置鼠标放上去的颜色a:hover  a:visited 访问后的样式
- 解决div的宽度因为border的宽度而影响 box-sizing: border-box 
- 一般float用完之后就清除
```html
<div></div>
<div style="clear: both">清除浮动</div>
<div></div>
```
![](https://note-imge.oss-cn-beijing.aliyuncs.com/pasteimageintomarkdown/2020-08-18/58408373514800.png?Expires=4751347550&OSSAccessKeyId=LTAI4G1a9jwwXfvRfRgyzeZ3&Signature=LYn1Tm7%2FOgWw9qS3VfrUbk4602c%3D)

####position 定位
- static 静态定位 默认
- relative 相对定位 根据现在位置进行偏移
- absolute 绝对定位 根据窗口偏移（如果上级有relative ,那么就按照上级的relative进行绝对定位）
- fixed 固定定位 可以把一个按钮固定在屏幕的右下角 不随屏幕滚动而变化
- sticky 


