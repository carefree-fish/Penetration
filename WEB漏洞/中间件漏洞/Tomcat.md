## Tomcat 任意文件写入（CVE-2017-12615）

#### 影响范围：

Apache Tomcat 7.0.0 - 7.0.81

#### 漏洞原理：

Tomcat配置文件/conf/web.xml 配置了可写（readonly=false），导致可以使用PUT方法上传任意文件，攻击者将精心构造的payload向服务器上传包含任意代码的 JSP 文件。之后，JSP 文件中的代码将能被服务器执行

![img](https://img-blog.csdnimg.cn/b49dbd92177f4a0caa8773ee7efa13e1.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA56CB5ZWK56CB,size_20,color_FFFFFF,t_70,g_se,x_16)

payload：

```
PUT /shell.jsp/ HTTP/1.1
PUT /shell.jsp%20 HTTP/1.1
PUT /x.jsp::$DATA HTTP/1.1
```



## Tomcat 远程代码执行（CVE-2019-0232）

#### 影响版本

```cobol
Apache Tomcat 9.0.0.M1 to 9.0.17
Apache Tomcat 8.5.0 to 8.5.39
Apache Tomcat 7.0.0 to 7.0.93
```

#### 漏洞利用条件

1.系统为Windows
\2. 启用了CGI Servlet（默认为关闭）
\3. 启用了enableCmdLineArguments（Tomcat 9.0.*及官方未来发布版本默认为关闭）

#### 漏洞原理

由于使用enableCmdLineArguments在Windows上运行时，远程执行代码漏洞（[CVE](https://so.csdn.net/so/search?q=CVE&spm=1001.2101.3001.7020)-2019-0232）驻留在公共网关接口（CGI）Servlet中，java运行时环境（JRE）将命令行参数传递给Windows的方式存在缺陷导致



## Tomcat session反序列化（CVE-2020-9484）

#### 影响版本：

```cobol
10.0.0-M1至10.0.0-M4
9.0.0.0.M1至9.0.34
8.5.0至8.5.54
7.0.0至7.0.103
```

#### 漏洞利用条件：

1.攻击者能够控制服务器上文件的内容和文件名称； 
2.服务器PersistenceManager配置中使用了FileStore； 
3.PersistenceManager中的sessionAttributeValueClassNameFilter被配置为“null”，或者过滤器不够严格，导致允许攻击者提供[反序列化](https://so.csdn.net/so/search?q=反序列化&spm=1001.2101.3001.7020)数据的对象； 
4.攻击者知道使用的FileStore存储位置到攻击者可控文件的相对路径

#### 漏洞原理：

当使用tomcat时，如果使用了tomcat提供的session持久化功能，就会在一次会话中尝试读取session文件中的内容，并进行反序列化。



## Tomcat 弱口令 && 后台getshell

#### 影响版本

所有版本

#### 漏洞条件

Tomcat7+权限分为：

- manager（后台管理）
  - manager-gui 拥有html页面权限
  - manager-status 拥有查看status的权限
  - manager-script 拥有text接口的权限，和status权限
  - manager-jmx 拥有jmx权限，和status权限
- host-manager（虚拟主机管理）
  - admin-gui 拥有html页面权限
  - admin-script 拥有text接口权限

这些权限的究竟有什么作用，详情阅读 [Apache Tomcat 8 (8.5.77) - Manager App How-To](http://tomcat.apache.org/tomcat-8.5-doc/manager-howto.html)

用户tomcat拥有上述所有权限，密码是tomcat；正常安装的情况下，tomcat中默认没有任何用户，且manager页面只允许本地IP访问。只有管理员手工修改了这些属性的情况下，才可以进行攻击。

#### 漏洞原理

通过弱口令进入后台后，在后台部署war文件，可以直接将webshell部署到web目录下