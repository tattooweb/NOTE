# python note

### 1. print()

```python
print()	#自带换行
print('*',end = '') #取消自动换行
```

### 2. 语法报错
Non-ASCII character '\xe5' in file...

```python
#编码有问题，在脚本头加入以下代码

#!usr/bin/python
# -*- coding: utf-8 -*-
```

### 3. 循环遍历字典

```python
fruit = {1:'apple',2:'banana',3:'grapes',4:'pear'}	#字典
fruit = {1,2,3,4}	#集合，没有映射关系

#遍历字典的键（默认）
for i in fruit.keys():
	print(i)

#遍历字典的值
for i in fruit.values():
	print(i)

#遍历字典的键值
for i,j in fruit.items():
	print(i,j)
```

### 4. 传递参数默认值

```python
#!usr/bin/python
# -*- coding: utf-8 -*-
def buyPC(cpu = 'i5',memory = '8G',videocard = '970'):
	print("CPU是:" + cpu)
	print("内存是:" + memory)
	print("显卡是:" + videocard)

#不传入实参，使用形参默认值
buyPC()
print('----------')
#传入实参
buyPC('i7','16G','1080')
```

### 5. 关键字参数（实参）

指定形参的实参的传递参数格式

可防止参数传递顺序错误

```python
buyPC(memory = '16G',cpu = 'i5',videocard = '4K')
```

### 6. 收集参数

一次性收集多个，不限个数的形参

```python
#!usr/bin/python
# -*- coding: utf-8 -*-
# *allargs表示接收所有形参传递过来的参数
def getsum(*allargs):
	#得到的是一个元组类型，使用for..in..遍历
	res = 0
	for i in allargs:
		res += i
	print(res)

getsum(2,5,10,8)

'''
注意事项:
	1.收集参数仅接收没有形参接受的普通实参，如函数getarg1(a,b,c,*d)
	2.收集参数只可以接收普通实参，不可以接收关键字参数，如函数getarg2(a,b,c,*d)会报错
'''
def getarg1(a,b,c,*d):
	print(d)
	print(a,b,c)

getarg1(1,2,3,4,5,6,7,8)

def getarg2(a,b,c,*d):
	print(d)
	print(a,b,c)

getarg2(1,2,3,4,5,6,7,8,z='10')
```

### 7. 关键字收集参数（关键字参数+收集参数）

在普通的形参之前添加两个**，就可以使得当前形参可接受多个传入的关键字实参

```python
#!usr/bin/python
# -*- coding: utf-8 -*-
# 双星号**表示接收关键字收集参数，是一个字典类型
def personInfo(**person):
	print('属性：' , person.keys())
	print('值：' , person.values())

personInfo(height = '173cm',weight = '65kg')

'''
注意事项:
	1.关键字收集参数仅接收没有形参接收的关键字实参，如函数personInfo1(weight,**person),关键字收集参数person不会接收到weight，已被前面的形参接收
	2.关键字收集参数只可以接收关键字实参，不可以接收普通实参，如函数personInfo2(**person)，调用时传入了name，但是函数定义了形参为关键字收集参数，所以name不会被接收
'''
def personInfo1(weight,**person):
	print('属性：' , person.keys())
	print('值：' , person.values())

personInfo1(height = '173cm',weight = '65kg')

def personInfo2(**person):
	print('属性：' , person.keys())
	print('值：' , person.values())

personInfo2(name,height = '173cm',weight = '65kg')
```

### 8. 多种参数混合存在

形参：普通形参 `var`，收集参数 `*var`，关键字收集参数 `**var`

实参：普通实参 `99`，关键字实参 `var = 99`

**实参顺序：普通实参在前，关键字参数在后**

```python
#!usr/bin/python
# -*- coding: utf-8 -*-

#定义一个测试使用的函数
def testargs(name,sex,age,*size,**goodfriends):
	#使用普通参数
	print(name,sex,age)
	#使用收集参数
	print(size)
	#使用关键字收集参数
	print(goodfriends)

testargs('张三','男',18,178,125,35,gf1 = '小刚',gf2 = '小红',gf3 = '小李')

'''
//output
张三 男 18
(178, 125, 35)
{'gf1': '小刚', 'gf2': '小红', 'gf3': '小李'}
'''
```

**形参顺序：关键字收集参数(\**)一定在最后出现，收集参数(\*)推荐在普通参数后出现**

- 普通形参→收集参数→关键字收集参数

```python
testargs('张三','男',18,178,125,35,gf1 = '小刚',gf2 = '小红',gf3 = '小李')
```

- 普通参数：'张三','男',18
- 收集参数：178,125,35
- 关键字收集参数：gf1 = '小刚',gf2 = '小红',gf3 = '小李'

### 9. 查看函数文档

```python
#1. help函数
help(print)
'''
Help on built-in function print in module builtins:

print(...)
    print(value, ..., sep=' ', end='\n', file=sys.stdout, flush=False)
    
    Prints the values to a stream, or to sys.stdout by default.
    Optional keyword arguments:
    file:  a file-like object (stream); defaults to the current sys.stdout.
    sep:   string inserted between values, default a space.
    end:   string appended after the last value, default a newline.
    flush: whether to forcibly flush the stream.
'''

#2. 函数名.__doc__
print(print.__doc__)
'''
print(value, ..., sep=' ', end='\n', file=sys.stdout, flush=False)

Prints the values to a stream, or to sys.stdout by default.
Optional keyword arguments:
file:  a file-like object (stream); defaults to the current sys.stdout.
sep:   string inserted between values, default a space.
end:   string appended after the last value, default a newline.
flush: whether to forcibly flush the stream.
'''
```

**自定义函数文档**

定义在冒号后一行，用三引号注释

```python
#!usr/bin/python
# -*- coding: utf-8 -*-
def getsum(num1,num2):
	'''
	:param no1:num1
	:param no2:num2
	:return:result
	'''
	result = num1 + num2
	return result

help(getsum)
```

### 10. 全局变量

真全局：使用global关键字声明**函数外**的变量，在函数内部可访问修改

假全局：未使用global关键字声明**函数外**的变量，在函数内部仅可访问，不可操作

```python
#!usr/bin/python
# -*- coding: utf-8 -*-
real = 99

def realGlobal():
	global real #关键字声明可访问修改
	real = real + 1
	print(real)

realGlobal()
```

### 11. 变量作用域链

变量的有效范围可以向函数内部层层传递

### 12. nonlocal关键字

nonlocal关键字声明**函数内**的变量，在嵌套的函数中可以修改访问

**nonlocal：不是全局变量也不是局部变量，叫外部变量**（python3）

```python 
#!usr/bin/python
# -*- coding: utf-8 -*-
def outer():
	var = 55
	def inner():
		nonlocal var
		var = var + 1
		print(var)
	inner()

outer()
```

### 13. lambda表达式

函数的另一种表示方式，本质还是函数

```python
#!usr/bin/python
# -*- coding: utf-8 -*-
def getsum1(no1,no2):
	result = no1 + no2
	return result

var = getsum1(5,10)
print(var)

'''
lambda格式:
函数名 = lambda 参数1,参数2...:实现函数功能的代码

lambda表达式自带return
主要用于简单运算后者判断，无法使用循环或者遍历
'''

getsum2 = lambda nb1,nb2:nb1 + nb2
var = getsum2(3,3)
print(var)
```

### 14. 字符串格式化

**位置参数**

0和1分别代表位置

```python
"{0} love {1}".format("I","you")
```

**关键字参数**

a和b为关键字

```python
"{a} love {b}".format(a="I",b="you")
```

**字符串格式化符号含义**

| 符号   | 说明                               |
| ------ | :--------------------------------- |
| %c     | 格式化字符串及其ASCII码            |
| %s     | 格式化字符串                       |
| %d     | 格式化整数                         |
| %o、%x | 格式化八进制、十六进制             |
| %f     | 格式化定点数，可指定小数点后的精度 |

```python
#ASCII转字符串
"ASCII2STR:%c" % (97)

#字符串
"%s" % "formatStr"

#整数
"%d + %d = %d" % (10,20,10+20)

#十进制转八进制
"Decimal2Octal:%o" % (10)

#十进制转十六进制
"Decimal2Hex:%x" % (10)

#定点数（默认六位小数）
"%f" % （27.658）
```

**格式化操作符辅助指令**

| 符号 | 说明                                              |
| :--- | ------------------------------------------------- |
| m.n  | m表示最小总宽度，n表示小数点后的位数              |
| -    | 左对齐                                            |
| +    | 在整数前显示加号                                  |
| #    | 八进制数前显示零（‘0’），十六进制数前显示（‘0x’） |
| 0    | 显示的数字前面填充‘0’取代空格                     |

````python
"%5.2f" % (52.666)
"%.2f" % (52.666)
"%6d" % 5

"%-6d" % 5

"%+d" % 5

"%#o" % (10)

"%#x" % (10)

"%06d" % (10)
````

**操作函数**

| 函数        |
| :---------- |
| list()      |
| len()       |
| max()       |
| min()       |
| sum()       |
| sorted()    |
| reversed()  |
| enumerate() |
| zip()       |

### 15. 集合

字典的表亲：集合，不像字典中有映射关系，集合中元素都是唯一的，无序

集合可以用于去除元组、列表中的重复值

```python
#创建集合
jihe1 = {1,2,3,4,5}
jihe2 = set([1,2,3,4,5])

#去除重复
list1 = [3,4,2,5,5,3,1,9]
print(list(set(list1)))
```

### **16**. 文件操作

**打开文件模式**

| 打开模式 | 执行操作                                               |
| :------- | ------------------------------------------------------ |
| r        | 只读。以只读方式打开文件（默认）                       |
| w        | 覆盖写。以写入方式打开文件，会覆盖已存在的文件         |
| x        | 如果文件已经存在，使用此模式打开将引出异常             |
| a        | 追加写。以写入模式打开，如果文件存在，则在末尾追加写入 |
| b        | 以二进制模式打开文件                                   |
| t        | 以文本模式打开（默认）                                 |
| +        | 可读写模式（可添加到其他模式中使用）                   |
| U        | 通用换行符                                             |

**文件对象方法的使用**

| 文件对象方法        | 执行操作                                                     |
| ------------------- | ------------------------------------------------------------ |
| f.close()           | 关闭文件对象                                                 |
| f.read(size=-1)     | 从文件读取size个**字符**，当未给定size或给定负值的时候，读取剩余的所有字符，然后作为字符串返回 |
| f.readline()        | 用于从文件读取整行，包括 "\n" 字符                           |
| f.write(str)        | 将字符串str写入文件                                          |
| f.writelines(seq)   | 向文件写入字符串序列seq，seq应该是一个返回字符串的可迭代对象 |
| f.seek(offset,from) | 在文件中移动文件指针，从from（0文件开头，1当前位置，2文件末尾）偏移offset个字节 |
| f.tell()            | 返回当前在文件中的位置                                       |

```python
#打开并读取文件
f1 = open('file.txt','r',encoding='utf-8')
print(f1.read(20))
f1.close()

#for循环遍历读取文件
f = open('file.txt','r',encoding='utf-8')
for each_line in f:
	print(each_line)
f.close()
```

```python
#写入内容到文件，前提是可写模式w、a
f = open('file1.txt','a',encoding='utf-8')
f.write("贾小爽是sb2" + '\n')
f.close()
```

**os模块的使用**

| 函数名            | 使用方法                                                 |
| :---------------- | -------------------------------------------------------- |
| getcwd()          | 获取当前工作目录                                         |
| chdir()           | 改变工作目录                                             |
| listdir(path='.') | 列举指定目录中的文件名                                   |
| mkdir(path)       | 创建单层目录                                             |
| makedirs(path)    | 递归创建多层目录                                         |
| remove(path)      | 删除文件                                                 |
| rmdir(path)       | 删除单层目录                                             |
| removedirs(path)  | 递归删除多层目录                                         |
| rename(old,new)   | 重命名文件                                               |
| system(command)   | 执行shell命令                                            |
| os.curdir         | 指代当前目录                                             |
| os.pardir         | 指代上一级目录                                           |
| os.sep            | 输出操作系统特定的路径分隔符（win: '\\\\\'  linux: '/'） |
| os.linesep        | 当前平台使用的行终止符（win: '\r\n'  linux: '\n'）       |
| os.name           | 指代当前使用的操作系统                                   |

**os.path模块的使用**

| 函数名                     | 使用方法                                                     |
| -------------------------- | ------------------------------------------------------------ |
| basename()                 | 去掉目录路径，单独返回文件名                                 |
| dirname()                  | 去掉文件名，单独返回目录路径                                 |
| join(path1[1,path2[,...]]) | 将path1,path2各部分组合成一个路径名                          |
| split(path)                | 分割文件名与路径，返回（f_path,f_name）元组。如果完全使用目录，它也会将最后一个目录作为文件名分类，且不会判断文件或者目录是否存在 |
| splitext(path)             | 分离文件名与扩展名，返回（f_name,f_extension）元组           |
| getsize(file)              | 返回指定文件的尺寸，单位是字节                               |
| getatime(file)             | 返回指定文件最近的访问时间                                   |
| getctime(file)             | 返回指定文件的创建时间                                       |
| getmtime(file)             | 返回指定文件最新的修改时间                                   |
|                            |                                                              |
| exists(path)               | 判断指定路径（目录或文件）是否存在                           |
| isabs(path)                | 判断指定路径是否为绝对路径                                   |
| isdir(path)                | 判断指定路径是否存在且是一个目录                             |
| isfile(path)               | 判断指定路径是否存在且是一个文件                             |
| islink(path)               | 判断指定路径是否存在且是一个符号链接                         |
| ismount(path)              | 判断指定路径是否存在且是一个挂载点                           |
| samefile(path1,path2)      | 判断path1和path2两个路径是否指向同一个文件                   |

**永久存储**

```python
#!usr/bin/python
# -*- coding: utf-8 -*-
#写入文件wb
import pickle
list1 = [156,3.1415,'asdas',['second']]
file = open('list1.txt','wb')
pickle.dump(list1,file)
file.close()

#打开文件rb
pickle_file = open('list1.txt','rb')
list2 = pickle.load(pickle_file)
print(list2)
pickle_file.close()
```

### 17. 异常处理

常见异常总结

| 异常名            | 解释                               |
| ----------------- | :--------------------------------- |
| AttributeError    | 尝试访问未知的对象属性、方法       |
| EOFError          | input()读取到EOF却没有接收任何数据 |
| ImportError       | 导入模块失败                       |
| IndexError        | 索引超出范围                       |
| KeyError          | 字典中查找一个不存在的关键字       |
| NameError         | 访问一个不存在的遍历变量           |
| OSError           | 操作系统产生的异常                 |
| SyntaxError       | 语法错误                           |
| TypeError         | 不同类型间的无效操作               |
| IndentationError  | 缩进错误                           |
| ValueError        | 传入无效的参数                     |
| ZeroDivisionError | 除数为零                           |
| UnicodeError      | unicode相关错误                    |
| RuntimeError      | 一般的运行时错误                   |

```python
#try-except语法
try:
    #检测范围
except [Exception1,Exception2...][as reason]:
    #处理异常,reason需要强制转换为string类型
[else:]
    #无异常会执行的代码
[finally:]
    #无论如何都会执行的代码
```

```python
try:	
	file = open('new.txt','r')
	print(file.read())
	file.close()
except Exception as reason:
	print('error!\n' + str(reason))
```

```python
try:	
	sum = 1 + '1'
	file = open('new.txt','r')
	print(file.read())
	file.close()
except OSError as reason:
	print('error1!\n' + str(reason))
except TypeError as reason:
	print('error2!\n' + str(reason))
```

```python
#with关键字，处理异常并自动关闭文件
try:
	with open('new.txt','w') as f:
		for each_line in f:
			print(each_line)
except OSError as reason:
	print("error!" + str(reason))
```

### 18. 类和对象

1. self关键字区别于是函数还是类的方法，类方法中不可省略

2. self可理解为某一实例，总是指向调用的类的实例

3. 同一个类中调用其他的方法时需要加self（self.test()）
4. self的属性，名称前加上**两个下划线**，就变成了一个**私有变量**（private），只有内部可以访问，外部不能访问


```python
class Turtle:
	#变量 = 属性
	color = 'green'
	weight = 10

	#函数 = 方法
	def climb(self):
		print("爬")

t = Turtle()
print(t.color)
t.climb()
```

```python
#魔术方法__init__
class Ball:
	def __init__(self,name):
		self.name = name
	def kick(self):
		print("我叫%s" % self.name)
b = Ball('篮球')
b.kick()
```

```python
#定义私有变量1
class Person:
	__name = "tattoo"
p = Person()
#p.__name访问变量将报错，因为私有变量只能内部访问
```

```python
#定义私有变量2
class Person:
	__name = "tattoo"
	def getName(self):
		return self.__name
p = Person()
print(p.getName())
#print(p._Person__name)也可访问私有变量
```

**继承**

```python
import random as r
class Fish:
	def __init__(self):
		self.x = r.randint(0,10)
		self.y = r.randint(0,10)
	def move(self):
		self.x -= 1
		print("我的位置是:(%s,%s)" % (self.x, self.y))

class Goldfish(Fish):
	pass

class Crap(Fish):
	pass

class Salmon(Fish):
	pass

class Shark(Fish):
	def __init__(self):
        #重写父类的方法，此处需先继承
		#1.调用super()方法，重写
		super().__init__()
		#2.或者调用未绑定的父类方法，重写
		#Fish.__init__(self)	#这里的self指的是Shark的实例
		self.hungry = True

	def eat(self):
		if self.hungry:
			print("天天都在吃")
			self.hungry = False
		else:
			print("太撑了，吃不下了")

fish = Fish()
fish.move()

goldfish = Goldfish()
goldfish.move()

shark = Shark()
shark.eat()
shark.move()
```

**多重继承**

```python
class Base1:
	def foo1(self):
		print("i am foo1...")
class Base2:
	def foo2(self):
		print("i am foo2...")
#多重继承
class C(Base1,Base2):
	pass

c = C()
c.foo1()
c.foo2()
```

**组合**

```python
class Turtle:
	def __init__(self,x):
		self.num = x
class Fish:
	def __init__(self,x):
		self.num = x
class Pool:
	def __init__(self,x,y):
		self.turtle = Turtle(x)
		self.fish = Fish(y)
	def print_num(self):
		print("水池共有%d只乌龟,%d条鱼!" % (self.turtle.num,self.fish.num))

pool = Pool(1,10)
pool.print_num()
```

**实例创建属性**

```python
class C:
	def x(self):
		print("x-man")
c= C()
c.x()
'''
为实例c创建属性x
但属性与方法名相同，方法名会被覆盖
'''
c.x = 1
#此时，调用x方法会报错
c.x()

```

**绑定**

类方法静态绑定在类中，实例对象不会拥有类方法

```python
class CC():
	def setXY(self,x,y):
		self.x = x
		self.y = y
	def printXY(self):
		print(self.x,self.y)

dd = CC()
#查看实例对象所拥有的的属性
print(dd.__dict__)

#查看类对象所拥有的的属性
print(CC.__dict__)

#绑定属性，仅属于dd
dd.setXY(4,5)	#相当于dd.setXY(self,x,y)
print(dd.__dict__)
print(CC.__dict__)
```

### 19. 魔法方法

构造方法\__init__

析构方法\__del__

```python
class Method:
	def __init__(self):
		print("调用init方法")
	def __del__(self):
		print("调用del方法")
m = Method()
```

**算数运算**

| 魔术方法                      | 作用         |
| ----------------------------- | ------------ |
| \__add__(self,other)          | 加法         |
| \__sub__(self,other)          | 减法         |
| \__mul__(self,other)          | 乘法         |
| \__truediv__(self,other)      | 真除法       |
| \__floordiv__(self,other)     | 整数除法     |
| \__mod__(self,other)          | 取模运算     |
| \__divmod__(self,other)       |              |
| \__pow__(self,other[,modulo]) | 次幂运算     |
| \__lshift__(self,other)       | 按位左移：<< |
| \__rshift__(self,other)       | 按位右移：>> |
| \__and__(self,other)          | 按位与：&    |
| \__xor__(self,other)          | 按位异或：^  |
| \__or__(self,other)           | 按位或：\|   |



### 20.正则表达式



