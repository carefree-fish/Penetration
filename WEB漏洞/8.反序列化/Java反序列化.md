![image-20230919142847722](https://cdn.unrun.top/zhangwei/image-20230919142847722.png)

![image-20230919143027069](https://cdn.unrun.top/zhangwei/image-20230919143027069.png)



PHP是触发魔术方法，Java没有魔术方法，只是一些代码上的问题



由于让受害者执行命令要考虑是否有回显限制的问题，所以大部分反序列化会采用反弹shell



序列化数据特征：

以rO0AB开头：Java序列化+base64加密

以aced开头：Java序列化+16进制

导入包里有swagger包，可以在/swagger-ui.html进行账号密码测试