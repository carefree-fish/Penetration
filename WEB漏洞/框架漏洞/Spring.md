## Spring框架特征

1.看web应用程序的ico小图标，是一个小绿叶子

![在这里插入图片描述](https://img-blog.csdnimg.cn/4287e283224a4e79a941affe2ae355c8.png)

2.看报错页面，如果默认报错页面没有修复，那就是长这样
![在这里插入图片描述](https://img-blog.csdnimg.cn/ec16326e05fc4a7c861f37032a938338.png)

3.wappalyzer插件识别

![在这里插入图片描述](https://img-blog.csdnimg.cn/ab7c2c4659a54902b56300f9aa68edb9.png)

4.f12看X-Application-Context头
![在这里插入图片描述](https://img-blog.csdnimg.cn/8aa9140da35b402b8a01c315b684d8bf.png)



## **Spring Security OAuth2远程命令执行（CVE-2016-4977）**

### **漏洞原理：**

该漏洞是在有账号密码的前提下实现RCE。

Spring Security OAuth是为Spring框架提供安全认证支持的一个模块。在其使用 whitelabel views来处理错误时，由于使用 SpEL，攻击者在被授权的情况下可以通过构造恶意参数来远程执行命令。

### **影响版本：**

2.0.0-2.0.9

1.0.0-1.0.5



## **Spring Web Flow框架远程代码执行（CVE-2017-4971）**

### **漏洞简介：**

Spring Web Flow是Spring的一个子项目，主要目的是解决跨越多个请求的、用户与服务器之间的、有状态交互问题，提供了描述业务流程的抽象能力。

在其 2.4.x版本中，如果我们控制了数据绑定时的field，将导致一个SpEL表达式注入漏洞，从而造成任意命令执行。

### **利用条件：**

在订阅图书处，存在一个命令执行，直接调用了两个函数：addDefaultMappings 和 addModelBindings。其中，控制field这个值的函数是 addDefaultMappings，且未做过滤，而 addModelBindings 是直接获取的java的一个配置文件，由配置文件确定是否有 binder 节点。如果有，就无法触发代码执行。所以利用条件有两个：

```
1、binder节点为空。
2、useSpringBeanBinding 默认值（false）未修改。
```

### **影响版本：**

Spring WebFlow 2.4.0 - 2.4.4



## **Spring Data Rest远程命令执行（CVE-2017-8046）**

### **漏洞简介：**

Spring-Data-Rest 服务器在处理PATCH请求时存在一个远程代码执行漏洞。攻击者可以构造恶意的PATCH请求并发送给Spring-Data-Rest 服务器，通过构造好的 JSON数据执行任意 Java代码。

### **影响版本：**

Spring Data REST < 2.5.12, 2.6.7, 3.0 RC3

Spring Boot < 2.0.0M4

Spring Data release trains < Kay-RC3



## **Spring Messaging 远程命令执行突破（CVE-2018-1270）**

### **漏洞简介：**

Spring框架中的 spring-messaging 模块提供了一种基于 WebSocket的 STOMP 协议实现，STOMP消息代理在处理客户端消息时存在SpEL表达式注入漏洞，在spring messages中，其允许客户端订阅消息，并使用 selector过滤消息，selector用SpEL表达式编写，并使用 StandardEvaluationContext解析，攻击者可以通过构造恶意的消息来实现远程代码执行。

### **STOMP（简单文本定向消息协议）：**

STOMP是一种在客户端与中转服务端之间进行异步消息传输的简单通用协议。它定义了服务端与客户端之间的格式化文本传输方式。这个协议可以有多种载体，可以通过HTTP或WebSocket。在Spring中使用的是 STOMP Over WebSocket。

当Client和Server已经有了一个会话。现在需要规定一种格式，让双方都理解说的是什么，这种格式就是STOMP来统一的。

STOMP的每一个包由三个部分组成：COMMAND、Header、Body

结构简化为：

```
COMMAND
header1:value1
header2:value2

Body
```

### **STOMP如何进行订阅？**

客户端使用 SUBSCRIBE 订阅命令，向 Stomp服务代理订阅某一个虚拟路径上的监听。当其它客户端使用 SEND命令发送内容到路径上时，这个客户端就可以收到这个消息。在使用 SUBSCRIBE时，有一个重要的 ACK属性。这个 ACK属性说明了 STOMP服务代理端发送给这个客户端的消息是否需要收到一个 ACK命令，才认为这个消息处理成功。

### **影响版本：**

Spring Framework 5.0 to 5.0.4.

Spring Framework 4.3 to 4.3.14



## **Spring Data Commons 远程命令执行（CVE-2018-1273）**

### **漏洞简介：**

Spring Data 是一个简化数据库访问，并支持云服务的开源框架，Spring Data Commons是Spring Data 下所有子项目共享的基础框架。Spring Data Commons 在2.0.5及以前版本中，存在一处SpEL表达式注入漏洞。

### **影响版本：**

```Spring Data Commons 远程命令执行（CVE-2018-1273）
Spring Data Commons 1.13 - 1.13.10 (Ingalls SR10)
Spring Data REST 2.6 - 2.6.10 (Ingalls SR10)
Spring Data Commons 2.0 - 2.0.5 (Kay SR5)
Spring Data REST 3.0 - 3.0.5 (Kay SR5)
```