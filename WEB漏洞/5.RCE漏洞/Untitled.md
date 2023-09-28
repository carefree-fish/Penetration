RCE：代码执行漏洞，命令执行漏洞

![image-20230430171908268](E:\AppData\Roaming\Typora\typora-user-images\image-20230430171908268.png)

![image-20230430171945477](E:\AppData\Roaming\Typora\typora-user-images\image-20230430171945477.png)

## 漏洞产生条件

1. 可控变量：可以客户端可以通过传参来改变的变量
2. 漏洞函数：应用程序直接/间接使用了动态执行命令/代码的危险函数

不止在网站上有CVE漏洞，中间件等也有

## 出现场景

网站有需要用系统命令才能使用的功能，如图片上传网站等。也可能是反序列化造成的

## 代码执行

应用程序在调用一些能够**将字符串转换为代码**的函数（如PHP中的eval）时，没有考虑用户是否控制这个字符串，将造成代码执行漏洞。

很难通过黑盒查找漏洞，大部分都是根据源代码判断代码执行漏洞。

常用函数：

```
PHP: eval、assert、preg_replace()、+/e模式（PHP版本<5.5.0）
```



## 命令执行

操作系统命令

命令执行相当于直接在cmd下敲命令

在操作系统中，“&、|、||”都可以作为命令连接符使用，用户通过浏览器提交执行命令，由于服务器端没有针对执行函数做过滤，导致在没有指定绝对路径的情况下就执行命令(如添加一个名为admin 密码为admin的用户 net user admin admin/add net localgroup administrators admin /add 加入管理员组（赋予管理员权限）)

system执行系统命令，分Windows和Linux，如ipconfig和pwd

```
command1&command2 两个命令同时执行
command1&&command2 只有前面命令执行成功，后面命令才继续执行
command1;command2 不管前面命令执行成功没有，后面的命令继续执行(linux独有)
command1|command2 前面命令的输出，为后门命令的输入
command1||command2 顺序执行多条命令，当碰到执行正确的命令后将不执行后面的命令
```

系统命令执行函数：

```
 1. System（Windows&Linux）：system函数可以用来执行一个外部的应用程序并将相应的执行结果输出，函数原型如下：
 string system(string command, int&return_var)
其中，command是要执行的命令，return_var存放执行命令的执行后的状态值。
 2. Exec：exec函数可以用来执行一个外部的应用程序
string exec (string command, array&output, int &return_var)
其中，command是要执行的命令，output是获得执行命令输出的每一行字符串，return_var存放执行命令后的状态值。只返回之后一行输出
 3.Passthru（Linux）：passthru函数可以用来执行一个UNIX系统命令并显示原始的输出，当UNIX系统命令的输出是二进制的数据，并且需要直接返回值给浏览器时，需要使用passthru函数来替代system与exec函数。Passthru函数原型如下：
void passthru (string command, int&return_var)
其中，command是要执行的命令，return_var存放执行命令后的状态值。
 4. Shell_exec：执行shell命令并返回输出的字符串，返回全部输出，函数原型如下：
string shell_exec (string command)
其中，command是要执行的命令。
 5. `$command` ：反引号(波浪号+shift)。PHP 支持将反引号中的内容作为 shell 命令来执行，并将其输出信息返回（即，可以赋给一个变量而不是简单地丢弃到标准输出）
 6. popen()：popen ( string command, string mode )
打开一个指向进程的管道，该进程由派生给定的 command 命令执行而产生。返回一个和 fopen() 所返回的相同的文件指针，只不过它是单向的（只能用于读或写）并且必须用 pclose() 来关闭。此指针可以用于 fgets()，fgetss() 和 fwrite()
 7.proc_open()：执行一个命令，并且打开用来输入/输出的文件指针。类似 popen() 函数， 但是 proc_open() 提供了更加强大的控制程序执行的能力。
 8.pcntl_exec()
```



## 代码执行

代码执行函数：

```
1.eval()：传入参数必须为PHP参数，分号结尾
2.assert()：直接将传入的参数当场PHP代码执行，不需要分号结尾
3.preg_replace()：将目标字符中符合正规规则的字符替换为替换字符。正则规则中使用/e修饰符，则存在代码执行漏洞。preg_replace('/test/e',$_POST['cmd'],'just test');
4.create_function()：创建匿名函数
5.call_user_func()：用回调函数处理字符串
6.array_filter()：用回调函数过滤数组
7.array_map()
```



## 其他造成漏洞的原因

#### 反序列化

#### NC

nc6.5可导致远程命令执行漏洞

nc可能可以未授权访问

#### 三星 WLAN AP 路由器RCE



## 漏洞发现

白盒：看源码（√）

黑盒：漏扫工具，网上爆出，手动看应用功能点来判断

## 漏洞利用

如果出现“阻止此页面创建更多对话框”，说明是前端验证，可以通过禁用js来绕过

poc：验证漏洞是否存在

exp：利用漏洞



## 防御

#### 变量过滤或固定

函数太多，所以限制变量

#### 铭感函数禁用

PHP： eval，assert等

python： exec

java：

#### 符号禁用

#### WAF产品



https://www.cnblogs.com/ermei/p/6689005.html

http://blog.leanote.com/post/snowming/9da184ef24bd
