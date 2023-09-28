PHP框架

特征：

![image-20230706110617960](E:\AppData\Roaming\Typora\typora-user-images\image-20230706110617960.png)

所有东西都在public目录

?s=1使其报错





thinkphp是一个国内轻量级的开发框架，采用php+apache，在更新迭代中，thinkphp也经常爆出各种漏洞，thinkphp一般有thinkphp2、thinkphp3、thinkphp5、thinkphp6版本，前两个版本已经停止更新，主要介绍下thinkphp5的漏洞

- ThinkPHP 5.0 命令执行漏洞
- ThinkPHP 5.x 命令执行漏洞

![在这里插入图片描述](https://img-blog.csdnimg.cn/566c831aefc44c8d9ff36c3903d8b7e5.png)

### 攻击特征

```
1.访问URL：/index/think\app/invokefunction

2.关键PHP函数：call_user_func_array、system、exec、shell_exec、eval
```