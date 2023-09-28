#### 常用模块

- dashboard：仪表盘，用于显示任务、日志信息
- Target: 提供显示目标目录结构的功能
- Proxy:拦截HTTP/s请求的代理服务器，作为web浏览器与服务器的中间人，允许拦截、修改数据流
- intruder：入侵模块，提供高精度的可配置工具，可进行爆破攻击、获取信息以及使用fuzzing技术探测漏洞等
- repeater：中继器，通过手动来触发单词HTTP请求，并分析应用程序的响应包
- sequence：会话模块，用于分析那些不可预知的应用程序会话令牌和重要数据的随机性的工具。
- decoder：解码器
- compare：对比模块，对数据进行差异化分析
- extender:拓展模块，可以加载BP拓展模块和第三方代码
- option：设置模块，可以设置项目、用户等信息。

















Hackvector：将请求的某部分进行编码等操作

Authz：同时对比查看多个请求



![image-20230901114029609](E:\AppData\Roaming\Typora\typora-user-images\image-20230901114029609.png)

截取返回的数据包



spider模块可以进行爬虫，爬取过程会显示在history中，可以在history中搜索特定内容。如id，uid，login，callback等功能点或数据包

![image-20230910000204555](E:\AppData\Roaming\Typora\typora-user-images\image-20230910000204555.png)