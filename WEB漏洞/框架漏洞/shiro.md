# Shiro-550

### 漏洞原理

Shiro默认使用了CookieRememberMeManager, 其处理cookie的流程是: 得到rememberMe的cookie值–>Base64解码–>AES解密–>反序列化.然而AES的密钥是硬编码的, 密钥泄漏的根本原因是开发人员在开发过程中部分代码直接使用了网上的一些开源的项目代码，就导致了攻击者可以构造恶意数据造成反序列化的RCE漏洞。
Apache Shiro框架提供了记住我的功能（RemeberMe），⽤户登录成功后会⽣成经过加密并编码的cookie。
cookie的key为RemeberMe，cookie的值是经过对相关信息进⾏序列化，然后使⽤aes加密，最后在使⽤base64编码处理形成的
在服务端接收cookie值时，按以下步骤解析：

1. 检索RemeberMe cookie的值
2. Base 64解码
3. 使⽤ACE解密（加密密钥硬编码）
4. 进⾏反序列化操作（未作过滤处理）
   在调⽤反序列化的时候未进⾏任何过滤，导致可以触发远程代码执⾏漏洞
   ⽤户登陆成功后会⽣成经过加密并编码的cookie，在服务端接收cookie值后，Base64解码–>AES解密–>反序列化。攻击者只要找到AES
   加密的密钥，就可以构造⼀个恶意对象，对其进⾏序列化–>AES加密–>Base64编码，然后将其作为cookie的rememberMe字段发送，Shiro将rememberMe进⾏解密并且反序列化，最终造成反序列化漏洞。只要rememberMe的AES加密密钥泄露，⽆论shiro是什么版本都会导致反序列化漏洞

### 攻击特征

```
1.cookie头的rememberMe字段超长
2.解密rememberMe可获取异常信息
在线解密网站：https://vulsee.com/tools/shiroDe/shiroDecrypt.html https://simolin.cn/tools/shiro/
3.若有回显，可以看到执行的相关命令和回显结果
1234
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/c3d31cafbe3d460e80b30341e2500afe.png)

## 影响版本

Apache Shiro <= 1.2.4



# Shiro-721

## 漏洞原理

由于Apache Shiro cookie中通过 AES-128-CBC 模式加密的rememberMe字段存在问题，用户可通过Padding Oracle 加密生成的攻击代码来构造恶意的rememberMe字段，并重新请求网站，进行反序列化攻击，最终导致任意代码执行。

## 漏洞特征

shiro反序列化的特征：在返回包的 Set-Cookie 中存在 `rememberMe=deleteMe` 字段

## 影响版本

Apache Shiro < 1.4.2