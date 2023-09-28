# Weblogic反序列化漏洞（CVE-2019-2725）

## 漏洞原理

CVE-2019-2725是一个Oracle weblogic反序列化远程命令执行漏洞，这个漏洞依旧是根据weblogic的xmldecoder反序列化漏洞，通过针对Oracle官网历年来的补丁构造payload来绕过。

## 漏洞特征

访问/_async/AsyncResponseService成功说明存在该漏洞
[![20220323001831](https://s2.loli.net/2022/03/23/sp4xa3HFe6cbh9N.png)](https://s2.loli.net/2022/03/23/sp4xa3HFe6cbh9N.png)

## 影响版本

- weblogic 10.x
- weblogic 12.1.3