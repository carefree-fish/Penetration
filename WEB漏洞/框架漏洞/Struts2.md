### 漏洞原理

Struts2是一款基于Java开发的框架,web路径下会出现两种特殊的文件格式,即*.action文件与*.jsp文件;

现阶段已知的S2poc大多数都是**远程命令执行漏洞**,主要出现位置有:**url中,报文内容中,content-type中;**

- S2-052:REST插件使用到XStreamHandler处理xml数据，由于未对xml数据做任何过滤，在进行发序列xml数据转换为Object时导致RCE
- S2-053:Struts2在使用Freemarker模板引擎的时候，同时允许解析OGNL表达式,导致用户输入的数据本身不会被OGNL解析，但由于被Freemarker解析一次后变成离开一个表达式，被OGNL解析第二次，导致任意命令执行漏洞。
- S2-057:网站配置XML时如果没有设置namespace的值，并且上层动作配置中并没有设置或使用通配符namespace时，可能会导致远程代码执行漏洞的发生
- S2-059:攻击者可以通过构造恶意的OGNL表达式，并将其设置到可被外部输入进行修改，且会执行OGNL表达式的Struts2标签的属性值，引发OGNL表达式解析，最终造成远程代码执行的影响。
- S2-061:对S2-059的沙盒绕过。

### 攻击特征

```
url中会出现的攻击特征主要是:
...*.action?method | ?redirect:${...}
conten-type中出现的攻击特征主要有:
%{#context
报文体中出现的攻击特征主要有:
#_memberAccess 

1.content-type出现异常字段，非MIME类型标识
2.字段格式为典型的注入代码格式%{ognl}
3.存在命令执行方法java.lang.ProcessBuilder().start()
4.执行命令“whoami”，返回包中返回执行成功信息“root”
5.出现类似于“3345*23565”的日志，通常都是扫描器扫描的日志
123456789101112
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/284c01b4760d4bcf97780a1960867be6.png)

![在这里插入图片描述](https://img-blog.csdnimg.cn/e0f31145c6744b01aae1cc30c5582b50.png)



java框架

特征：.action

有漏洞