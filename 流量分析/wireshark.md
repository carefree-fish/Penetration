常用过滤：

- 过滤ip，如源IP或者目标x.x.x.x：

  ```
  ip.src eq xxxx or ip.dst eq xxxx 或者 ip.addr eq xxxx
  ```

- 过滤端口：

  ```
  tcp.port eq(等于) 80 or udp.port eq 80
  ```

- 过滤MAC:

  ```
  eth.dst == MAC地址
  ```

- 协议过滤:

  ```
  直接在Filter框中直接输入协议名即可，http udp dns
  ```



HTTP模式过滤

- 过滤全部POST数据包

  ```
  http.request.method="POST" 
  ```

- ```
  http.request.uri=="/img/logo-edu.gif"
  ```

- 模糊匹配 http头中有这个关键字

  ```
  http contains"GET"
  ```

- 版本号

  ```
  http contains"HTTP/1."
  ```

- ```
  http.request.method=="GET"&& http contains"User-Agent:"
  ```

  

