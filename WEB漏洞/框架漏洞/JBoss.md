# JBoss反序列化漏洞（CVE-2015-7501）

## 漏洞原理

JBoss在`/invoker/JMXInvokerServlet`请求中读取了用户传入的对象，可以通过`Apache Commons Collections`中的Gadget执行任意代码

## 漏洞识别

访问`invoker/JMXInvokerServlet`出现下载即表示漏洞存在

## 影响版本

- JBoss Enterprise Application Platform 6.4.4,5.2.0,4.3.0_CP10
- JBoss AS (Wildly) 6 and earlier
- JBoss A-MQ 6.2.0
- JBoss Fuse 6.2.0
- JBoss SOA Platform (SOA-P) 5.3.1
- JBoss Data Grid (JDG) 6.5.0
- JBoss BRMS (BRMS) 6.1.0
- JBoss BPMS (BPMS) 6.1.0
- JBoss Data Virtualization (JDV) 6.1.0
- JBoss Fuse Service Works (FSW) 6.0.0
- JBoss Enterprise Web Server (EWS) 2.1,3.0



# JBoss反序列化漏洞2（CVE-2017-12149）

## 漏洞原理

> 原文来自知乎（https://zhuanlan.zhihu.com/p/33532884）

漏洞出现在 Jboss 的 HttpInvoker 组件中的 ReadOnlyAccessFilter 过滤器中，源码在`jboss\server\all\deploy\httpha-invoker.sar\invoker.war\WEB-INF\classes\org\jboss\invocation\http\servlet`目录下的`ReadOnlyAccessFilter.class`文件中
其中`doFilter()`函数代码如下:

```
JAVA
public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain)
    throws IOException, ServletException
  {
    HttpServletRequest httpRequest = (HttpServletRequest)request;
    Principal user = httpRequest.getUserPrincipal();
    if ((user == null) && (this.readOnlyContext != null))
    {
      ServletInputStream sis = request.getInputStream();
      ObjectInputStream ois = new ObjectInputStream(sis);
      MarshalledInvocation mi = null;
      try
      {
        mi = (MarshalledInvocation)ois.readObject();
      }
      catch (ClassNotFoundException e)
      {
        throw new ServletException("Failed to read MarshalledInvocation", e);
      }
      request.setAttribute("MarshalledInvocation", mi);

      mi.setMethodMap(this.namingMethodMap);
      Method m = mi.getMethod();
      if (m != null) {
        validateAccess(m, mi);
      }
    }
    chain.doFilter(request, response);
  }
```

可以看到其直接从http中获取数据，在没有进行检查或者过滤的情况下，尝试调用readobject()方法对数据流进行反序列操作，因此产生了Java反序列化漏洞。

## 影响版本

- JbossAS 5.X
- JbossAS 6.x