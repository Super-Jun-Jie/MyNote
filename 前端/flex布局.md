####flex布局记录
- 开启flex布局`display: flex;`
- 水平居中`justify-content: center;`
- 垂直居中`align-items: center;` 不生效的原因是height没有定义，定义一个高度即可
- 定义高度 100%和100vh的区别：
100%继承的是父标签的高度，或者是自身撑开的高度，并不包括滚动条的高度  
100vh是窗口可视高度包括滚动条
- 父布局`flex-container`

```css
            /*
            flex-direction 决定排列方向 默认时row 从左到右
                row 默认 从左到右
                row-reverse 反转（排列顺序反转） 从右向左
                column 从下往上
                column-reverse 反转 从下往上
             */
```
![](https://note-imge.oss-cn-beijing.aliyuncs.com/pasteimageintomarkdown/2020-08-23/21599837141900.png?Expires=4751774997&OSSAccessKeyId=LTAI4G1a9jwwXfvRfRgyzeZ3&Signature=dModkIbkLnDntr2sQ1VjcXVKan4%3D)
```css
/*
            justify-content 决定flex item主轴的对齐方式
                flex-start 默认 从左开始
                flex-end 从右开始（和row-reverse的区别是 flex-end不会改变排列顺序，row-reverse会改变div的排列顺序）
                center 居中对齐
                space-between div 都靠边 然后间隔相同
                space-evenly 平均间隔 对齐
                space-around 环绕对齐
             */
```
![](https://note-imge.oss-cn-beijing.aliyuncs.com/pasteimageintomarkdown/2020-08-23/22062023132800.png?Expires=4751775459&OSSAccessKeyId=LTAI4G1a9jwwXfvRfRgyzeZ3&Signature=j5DM1L7sSIe%2BJ2vdvtuyjY%2FzbnI%3D)
```css
            /**
            align-items 决定flex item 在交叉轴的对齐方式
                normal  在没有设置高度的情况下的默认值，交叉轴方向size为auto，自动拉伸填充至flex container
                stretch 同上
                flex-start 在设置高度的情况下的默认值，item顶部对齐
                flex-end 设置高度的情况下 item底部对齐
                align-items 设置高度的情况下 item中心对齐
                baseline 基线对齐，基于第一个div的文本进行对齐 和flex-start相似
             */
```
![](https://note-imge.oss-cn-beijing.aliyuncs.com/pasteimageintomarkdown/2020-08-23/22843408293900.png?Expires=4751776240&OSSAccessKeyId=LTAI4G1a9jwwXfvRfRgyzeZ3&Signature=Y5uGFOnxxNw0kAQar5Rfl2FGCv0%3D)
```css
            /*在默认情况下，container里面的item会在一行显示，如果显示不下，那么就压缩宽度*/
            /*
            flex-wrap 柔性布局
                nowrap 不换行
                wrap 自动换行  左上角开始
                wrap-reverse 左下角开始
            */
```
![](https://note-imge.oss-cn-beijing.aliyuncs.com/pasteimageintomarkdown/2020-08-23/23248839432300.png?Expires=4751776645&OSSAccessKeyId=LTAI4G1a9jwwXfvRfRgyzeZ3&Signature=kN2b3ro0YuJUXD9Oa%2Fr1DVqmpJY%3D)
```css
/*flex-flow 缩写属性 相当于 flex-direction + flex-wrap*/
            /*
            align-content 多行对齐方式
                stretch 没有高度的情况下 会拉伸
                space-around 边缘具距离是中间距离的两倍
                flex-start 从上往下
                flex-end 从下往上
                space-between 占据两下
            */
```
![](https://note-imge.oss-cn-beijing.aliyuncs.com/pasteimageintomarkdown/2020-08-23/24011583405700.png?Expires=4751777408&OSSAccessKeyId=LTAI4G1a9jwwXfvRfRgyzeZ3&Signature=uO6ZzB6743322qURM2nOlYQoxB0%3D)

####flex-item作用于container里的item
```css
/*order 顺序 值越小越靠前*/

/*align-self flex-start 覆盖父元素的设置(例如我就布局中)*/

/*flex-grow item的比重 值的和小于1时 不会占满container 大于1按照比重分*/
/*flex-shrink 决定item如何收缩 默认1  */
/*flex-shrink 决定item如何收缩 默认1  */
/*flex-basis 决定item最终的宽度 默认1  */
/*flex 一个包括三个 flex-grow flex-shrink flex-basis*/
```
![](https://note-imge.oss-cn-beijing.aliyuncs.com/pasteimageintomarkdown/2020-08-23/24977441187600.png?Expires=4751778374&OSSAccessKeyId=LTAI4G1a9jwwXfvRfRgyzeZ3&Signature=6kSYdNM8ae%2F%2FVVuFyxiyL6ABBzc%3D)
![](https://note-imge.oss-cn-beijing.aliyuncs.com/pasteimageintomarkdown/2020-08-23/25210531397800.png?Expires=4751778607&OSSAccessKeyId=LTAI4G1a9jwwXfvRfRgyzeZ3&Signature=ty0DgWSiVVg8Qgs%2BgHwuDgzhfHw%3D)
![](https://note-imge.oss-cn-beijing.aliyuncs.com/pasteimageintomarkdown/2020-08-23/25323165405100.png?Expires=4751778720&OSSAccessKeyId=LTAI4G1a9jwwXfvRfRgyzeZ3&Signature=gesWXYvlCXqgJ%2BE%2Fow7yXo8mhnU%3D)
![](https://note-imge.oss-cn-beijing.aliyuncs.com/pasteimageintomarkdown/2020-08-23/25530349477800.png?Expires=4751778927&OSSAccessKeyId=LTAI4G1a9jwwXfvRfRgyzeZ3&Signature=QsmghXZawBlFlbxnzPt9gQdLpKI%3D)

