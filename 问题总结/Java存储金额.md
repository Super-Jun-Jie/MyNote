###Java存储金额
####Java存储金额为什么不用double？
虽然double是双精度类型，但是用做价格仍不合适。举例如下：
```java
public class TestDouble{
    public static void main(String[] args){
      double num1 = 0.03;
      double num2 = 0.02;
      System.out.println(num1-num2);
    }
}
```
正常来说，结果应该位0.01，但实际上不是。正确答案是0.009999999999999998。金额是绝对不允许有偏差的，所有double不能用作金额的存储类型。
####BigDecimal用做java存储金额的类型
```java
public class TestDouble {
    public static void main(String[] args){
        BigDecimal num1 = new BigDecimal("0.03");
        BigDecimal num2 = new BigDecimal("0.02");
        System.out.println(num1.subtract(num2));
    }
}
```
这样正确答案就是0.01了。
####BigDecimal用法
1. 构造方法  
   new BigDecimal("0.01")
2. 常用方法,加、减、乘、除
    ```java
    public class TestBigDecimal {
        public static void main(String[] args) {
            BigDecimal bigDecimal1 = new BigDecimal("0.01");
            BigDecimal bigDecimal2 = new BigDecimal("0.02");
            //加
            System.out.println(bigDecimal1+"+"+bigDecimal2+"="+bigDecimal1.add(bigDecimal2));
            //减
            System.out.println(bigDecimal1+"-"+bigDecimal2+"="+bigDecimal1.subtract(bigDecimal2));
            //乘
            System.out.println(bigDecimal1+"*"+bigDecimal2+"="+bigDecimal1.multiply(bigDecimal2));
            //除
            System.out.println(bigDecimal1+"/"+bigDecimal2+"="+bigDecimal1.divide(bigDecimal2));
        }
    }
    ```
3. BigDecimal还有一些枚举，可以表示不同的数字。
![](https://note-imge.oss-cn-beijing.aliyuncs.com/pasteimageintomarkdown/2020-08-31/339425222353900.png?Expires=4752437553&OSSAccessKeyId=LTAI4G1a9jwwXfvRfRgyzeZ3&Signature=pOi3PaVFxe1CAVF8R%2BhxQXSIKJY%3D)
4. 大小比较，不能通过大于号小于号表示，而是用compareTo()方法来比较，返回的-1，0，1分别代表小于，等于，大于  
5. 四舍五入保留小数位
![](https://note-imge.oss-cn-beijing.aliyuncs.com/pasteimageintomarkdown/2020-08-31/339899549779400.png?Expires=4752438027&OSSAccessKeyId=LTAI4G1a9jwwXfvRfRgyzeZ3&Signature=hMywYKNhuugs1AFyhYu473LPhqM%3D)
    ```java
    public class TestBigDecimal {
        public static void main(String[] args) {
            BigDecimal amount = new BigDecimal("50");
            BigDecimal taxPoint = amount.multiply(new BigDecimal(0.09/1.09));
            System.out.println("扣税"+taxPoint);
            //以下的2表示两位小数
            System.out.println("小数点两位之后的全部删除用BigDecimal.ROUND_DOWN="+taxPoint.setScale(2,BigDecimal.ROUND_DOWN));
            //5.134 进位保留2位小数 最后等于5.14
            System.out.println("进位用BigDecimal.ROUND_UP="+taxPoint.setScale(2,BigDecimal.ROUND_UP));
            // 四舍五入，碰到5进位
            BigDecimal bigDecimal = new BigDecimal("4.127");
            System.out.println("BigDecimal.ROUND_HALF_UP模式="+bigDecimal.setScale(2,BigDecimal.ROUND_HALF_UP));
            // 四舍五入，碰到5舍弃(个人感觉没啥用)
            System.out.println("BigDecimal.ROUND_HALF_DOWN模式="+bigDecimal.setScale(2,BigDecimal.ROUND_HALF_DOWN));
        }
    }
    ```
6. mysql数据库保存
BigDecimal在进行入库时, 数据库选择decimal类型, 长度可以自定义, 如18; 小数点我们项目中用的是2, 保留2位小数. 此外还要注意的就是默认值, 一定写成0.00, 不要用默认的NULL, 否则在进行加减排序等操作时, 会带来转换的麻烦!
decimal(10,2)中，10表示总长度，2表示保留的位数