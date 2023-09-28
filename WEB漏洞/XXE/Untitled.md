xml：可拓展标记语言。运行用户对自己的标记语言进行定义。



xml文档：xml声明、DTD文档类型定义（可选）、文档元素

![image-20230706140417478](E:\AppData\Roaming\Typora\typora-user-images\image-20230706140417478.png)

全部都是双标签



类型定义可以内部声明，或外部引用



引用外部DTD：

本地文件：SYSTEM来标识

网络文件：PUBLIC来标识



DTD实体（ENTIEY）：类似变量

声明方式：

1.内部声明实体

```
<!ENTIEY xxx 'xxx'>
<author>&xxx;</auther>
```

2.外部声明实体

```
<!ENTITY xxx SYSTEM 'URL'>
<author>&xxx;</auther>

如：<!ENTITY test SYSTEM "http://127.0.0.1/xx.DTD"
```

3.参数实体：只能在本标签内引用

```
<!DOCTYPE copyright [
<!ENTITY % xxx SYSTEM 'url'%xxx;>
<!ENTITY xxe '%body;'>
]>
```

4.公共实体声明



当引用了外部实体时，可导致XXE漏洞

外部引用支持伪协议，不同语言支持的协议不同：



## 漏洞发现

1.看Content-Type：application/xml

或发送的数据是xml格式

2.看是否有回显。有则直接构造外部实体，并调用

3.无回显：用DNSlog



## 防御

1.禁用外部实体

2.过滤entity、system等



## 攻击内网

先探测127.0.0.1:各端口，看是否开放