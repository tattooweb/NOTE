# 1. 针对错误提示进行防御

**1.1 关闭错误提示**

PHP配置文件php.ini中设置

```php.ini
display_errors=Off
```

**1.2 统一错误提示**

不管有什么错误，都统一错误提示，可有效避免注入以及其它敏感信息泄露。

```php
print_r("非法字符，重新输入！");
```

# 2. 魔术引号和addslashes函数

**2.1 魔术引号**

**说明**：魔术引号（Magic Quote）是一个自动将进入 PHP 脚本的数据进行转义的过程。（对所有的 GET、POST 和 COOKIE 数据自动运行转义

**使用及效果：**

当php.ini里的**magic_quotes_gpc=On**时。提交的变量中所有的单引号（’）、双引号（”）、反斜线（\）与 NUL（NULL 字符）会自动转为含有反斜线的转义字符
**特性：**

PHP 5.4 之前 PHP 指令 magic_quotes_gpc 默认是 on。

注：本特性将自 PHP 5.4.0 起移除在PHP 5.4.O 起将始终返回 FALSE。

**2.2 Addslashes函数**

说明：对指定的字符进行转义

```php
<?php
    $str = "AA'AA";
    echo addslashes($str);//对单引号和双引号进行转义
?>
```

# 3. 检查数据类型

比如输入邮箱时，必须为邮箱格式，手机号规定长度等等

# 4. 控制连接数据的用户权限

应该使用最小权限原则，避免Web应用直接使用root等高权限账户直接连接数据库。如果多个不同的应用在使用同一个数据库，则该为每个应用分配不同的账户。Web应用使用的数据库账户，不应该创建自定义函数、操作本地文件的权限。

# 5. PDO预处理+参数绑定

预处语法：

```php
　　$pdo->prepare('select * from biao1 where id=:id');

　　$pdo->execute([':id'=>4]);
```

防注入原理：

语句一，服务器发送一条sql给mysql服务器，mysql服务器**会**解析这条sql。

语句二，服务器发送一条sql给mysql服务器，mysql服务器**不会**解析这条sql，只会把execute的参数当做纯参数赋值给语句一。哪怕参数中有sql命令也不会被执行，从而实现防治sql注入。

# 6. 硬件防护措施（WAF）

不管是WAF还是其它的安全设备，可以很大程度上过滤或监控来自网络的非法访问以及数据流量攻击，如SQL注入攻击；一套完善的安全模型应该包括以下一些必要的组件：防火墙、入侵检测系统、路由系统等。

# 7. 云端防御

阿里云盾 Web应用防火墙（WAF）通过防御常见OWASP攻击、提供热补丁漏洞修复，网站业务的定制规则防护，从而成功保障网站Web应用的安全性与可用性。