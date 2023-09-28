

基础知识：

数据包中参数

![image-20230426205356299](E:\AppData\Roaming\Typora\typora-user-images\image-20230426205356299.png)

| 参数                           | 含义                         | 能否更改   |
| ------------------------------ | ---------------------------- | ---------- |
| Content-Disposition: form-data | 接收类型：表单数据           | 一般可更改 |
| name                           | 表单传递的名字               | 不可更改   |
| filename                       | 文件名，带后缀（最关键）     | 可更改     |
| Content-Type                   | 文件自带类型（根据MIME信息） | 视情况更改 |

各参数之间分号间隔

参数赋值，如果用冒号，说明值为系统规定；如果用等号，说明可以为任意值。同时等号后的值，用单引号和双引号应该无区别



## 安全狗

上传php成功后，bp中不能Render显示页面，只能看源码

上传时，拦截特定后缀名

### 常见绕过方法：

关键：让其匹配不到php

#### 数据溢出-防匹配filename

用垃圾数据来溢出

垃圾数据填充点：Content-Disposition后、filename后

注意：垃圾数据单独做一项，也用分号与其他部分间隔

#### 符号变异（' " ;）

猜测安全狗用单/双引号来匹配参数值

如果去掉后引号，如filename="qq.php

则，可能匹配①qq.php，②"qq.php，③什么都匹配不到

如果是③，则可以上传成功，因为③不是php后缀

另：最终可判断出其匹配方式，如匹配最后一个引号前面的值，"x.php"=>"x.php，则可以"x"x.php=>"x，匹配不到，但是会上传出x.php文件

#### 数据截断（%00 ; 换行 /）

filename="x.jpg%00.php"(%00要手动进行编码)或filename="x.jpg;.php"(分号代表参数结束)

.php后面被截断，不会识别

换行：

filename="x.p

h

p"之类

#### 重复数据（参数多次）

将filename重复多次

先判断文件上传后，文件名取的是第一个filename还是最后一个

在将filename重复足够多次

如果参数匹配没有递归足够次数，则会绕过

或：

将filename前面的数据复制一份放进filename的x.php前

waf可能会根据x.php前的数据，认为x.php不是赋值给filename，从而绕过



总之，利用系统处理上传文件的filename的规则 和 waf用来拦截的规则的不一致



## FUZZ测试

fuzz字典，可以在bp中替换filename值

乌云fuzz库

https://github.com/fuzzdb-project/fuzzdb

https://github.com/TheKingOfDuck/fuzzDicts

https://github.com/TuuuNya/fuzz_dict

等等



## 安全修复

### 后端验证

采用服务端验证模式

#### 后缀检测

基于黑名单、白名单过滤

#### MIME检测

基于上传自带类型检测

#### 内容检测

文件头、完整性检测

### 自带函数过滤

php的，参考uploadlabs靶场中的函数

### 自定义函数过滤

function check_file()[]

### WAF防护产品

宝塔，云盾，安全公司产品等