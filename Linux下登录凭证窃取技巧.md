# Linux下登录凭证窃取技巧

### 1. history记录敏感操作

Linux系统会自动把用户的操作命令记录到历史列表中，当用户在命令行中输入账号、密码进行登录时，将会导致敏感信息泄露。

```bash
history | gerp root
```

### 2. shadow文件破解

/etc/shadow文件只有root用户有读权限，保存了系统内所有用户的密码信息

每行代表一个用户，使用冒号作为分隔符，分隔成9个字段

> 用户名:**加密密码**:最后一次修改时间:最小修改时间间隔:密码有效期:密码需要变更前的警告天数:密码过期后的宽限时间:账号失效时间:保留字段

**加密密码：**目前基本采用SHA-512散列加密算法

| 开头 | 加密方式 |
| ---- | -------- |
| $6$  | SHA-512  |
| $1$  | MD5      |
| $2$  | Blowfish |
| $5$  | SHA-256  |

使用john暴力破解SHA-512密文

```bash
wget https://www.openwall.com/john/k/john-1.9.0.tar.gz
tar -zxvf john-1.9.0.tar.gz
make clean linux-x86-64
./john /etc/shadow
```

### 3. mimipenguin抓取密码

一款Linux下的密码抓取神器，需要root权限运行，通过转储进程并提取很可能包含明文密码的行来利用内存中的明文凭证，目前支持Kali、Ubnutu等操作系统。

```url
https://github.com/huntergregal/mimipenguin
```

### 4. tcpdump抓包分析

抓取数据包分析，获取明文账号密码

```bash
tcpdump -i ens33 port 23 -w test.cap
```

### 5. 全盘搜索敏感信息

全局搜索配置文件、脚本、数据库、日志文件是否有包含密码。

```bash
grep -rn "password=" /
```

### 6. swap_digger

用于自动进行Linux交换分析bash脚本，自动进行交换提取，并搜索Linux用户凭据，Web表单凭据，Web表单电子邮件，HTTP基本身份验证，WiFi SSID和密钥等。

```url
https://github.com/sevagas/swap_digger
```

### 7. Impost3r

窃取linux下各类密码(ssh,su,sudo)的工具。

```url
https://github.com/ph4ntonn/Impost3r
```

