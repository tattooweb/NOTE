# SQL Bypass

### and/or

1. 双写and、or
2. 使用运算符代替&&、||、^
3. 直接拼接`=`号，如：`?id=1=(condition)`
4. 其他，如：`?id=1^(condition)`

### 空格

1. 多层括号嵌套
2. 加号代替
3. 注释代替
4. `and/or`后面跟上偶数个`!、~`可以代替空格
5. `and/or`前面的空格可以省略
6. `%09, %0a, %0b, %0c, %0d, %a0`特殊字符代替空格

**e.g. ：**`select * from user where username='admin'union(select+title,content/**/from/*!article*/where/**/id='1'and!!!!~~1=1)`

### 括号

1. order by大小比较盲注

### 逗号

1. 改用盲注
2. 使用join语句代替
3. `substr(data from 1 for 1)`相当于`substr(data,1,1)`、`limit 9 offset 4`相当于`limit 9,4`

### 系统关键字

1. 双写绕过
2. 使用同义函数，如 if 函数可用`case when condition then 1 else 0 end`代替

### 单引号

1. 需要跳出单引号的情况：尝试是否存在编码问题而产生的SQL注入
2. 不需要跳出单引号的情况：字符串可用十六进制表示、也可通过进制转换函数表示成其他进制



