## PHP反序列化

防止字段被序列化：类声明中表示为transient

序列化函数：serialize：对象->字符串

反序列化：unserialize：字符串->对象



类型:长度:值:成员个数

O:object

i:int

s:string

eg. O:4:"info":2:{s:4:"name";i:2:"19";}



### 漏洞原理

序列化之后，将其内容修改，再发给服务器，反序列化之后就会覆盖原来的内容

条件：

1.接收反序列化的数据

2.调用了魔术函数

对象的魔术函数：某些时候会自动调用

__construct()：对象创建时调用

__destruct()：对象销毁时调用

__toString()：对象被当成字符串来调用时

__sleep()：序列化时调用

__wakeup()：反序列化时调用

__get()：访问私有属性或不存在的属性

__invoke()：以调用函数的方式，调用对象时

### POP链

写exp



## java反序列化

需要编码

工具ysoserial.jar



## 危害

RCE、越权



## 漏洞发现

审计