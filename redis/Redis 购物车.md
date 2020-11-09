###购物车的实现主要通过redis中的hash类型来实现
####Hash
应对的存储需求:对一系列存储的数据进行编组，方便管理  
需要的存储结构:一个存储空间保存多个键值对数据
Hash类型：底层使用哈希表结构实现数据存储

####Hash类型的基本操作
![](https://note-imge.oss-cn-beijing.aliyuncs.com/pasteimageintomarkdown/2020-08-24/87755865950500.png?Expires=4751841152&OSSAccessKeyId=LTAI4G1a9jwwXfvRfRgyzeZ3&Signature=GHj6hDGUPc%2Fd84WrtHJe6M4Bow0%3D)
![](https://note-imge.oss-cn-beijing.aliyuncs.com/pasteimageintomarkdown/2020-08-24/87785627222600.png?Expires=4751841182&OSSAccessKeyId=LTAI4G1a9jwwXfvRfRgyzeZ3&Signature=A43yKt9x0bDb7L%2Fsqvj%2FvhlnJmE%3D)
####Hash 购物车的实现
![](https://note-imge.oss-cn-beijing.aliyuncs.com/pasteimageintomarkdown/2020-08-24/87926056419800.png?Expires=4751841323&OSSAccessKeyId=LTAI4G1a9jwwXfvRfRgyzeZ3&Signature=H3OJJ6MoV77RBVaKeGv3fPE2i9o%3D)
![](https://note-imge.oss-cn-beijing.aliyuncs.com/pasteimageintomarkdown/2020-08-24/87949128575900.png?Expires=4751841346&OSSAccessKeyId=LTAI4G1a9jwwXfvRfRgyzeZ3&Signature=azsmUFUISOUy4uK5urwIB%2FCfDrI%3D)
