# PYTHON

> [TOC]

-------

## 一.string方法

| 方法                             | 作用于原字符串? | 返回值                                           | 作用 |
| -------------------------------- | --------------- | ------------------------------------------------ | ---- |
| capitalize()                     | 不作用          | 首字母大写                                       |      |
| casefold()                       | 不作用          | 将所有字符改为小写                               |      |
| center(width)                    | 不作用          | 将文本居中,左右填充空格至总长度width             |      |
| count(sub,[start],[end])         | 不              | 返回子串的数量(在start和end之间)                 |      |
| encode()                         |                 |                                                  |      |
| endswith(sub,[start],[end])      | 不              | 检查start到end是不是以sub结尾的                  |      |
| expandtabs([tablesize=8])        | 不              | 把\t转换为tablesize个空格                        |      |
| find(sub,[start],[end])          | 不              | 返回sub在start,end之间的第一个索引,无则-1        |      |
| index(sub,[start],[end])         | bu              | 返回sub在start,end之间的第一个索引,无则异常      |      |
| join(sub)                        | 不              | 以字符串str作为分隔符,将sub的每一个字符分隔      |      |
| ljust(width)                     | 不              | 左对齐,空格填充至width                           |      |
| lower()                          | 不              |                                                  |      |
| lstrip([char])/rstrip()/strip()  | 不              | 去掉左边/右边/两边的空白(或char)                 |      |
| partition(sub)                   | 不              | 用子串把字符串分成三份,返回一个元组              |      |
| replace(old,new,[count])         | 不              | 把old换成new,不超过count次                       |      |
| rfind(sub[start],[end])/rindex   | 不              | 从右边开始查找,其余与find同                      |      |
| split(sep=None,maxsplit=-1)      | 不              | 以第一个参数分割字符串返回列表                   |      |
| splitlines([keepends])           | 不              | 按\n分割,返回keepends个行或所有的列表            |      |
| startswith(prefix,[start],[end]) | 不              | 检查字符串(start,end之间)是否prefix开头          |      |
| swapcase()                       | 不              | 大小写互换                                       |      |
| title()                          | 不              | 大写开头,其他小写                                |      |
| translate(table)                 | 不              | 根据table(可由str.marktrans(a,b)生成))转换字符串 |      |
|                                  |                 |                                                  |      |
|                                  |                 |                                                  |      |

#### 其他方法

* isalpha(英文)

* isnum(有字符且都是英文数字)

* isdecimal(十进制)

* isdigit(都是数字)

* islower(只小写)

* isnumeric(只数字)

* isspace(只空格)

* istitle(标题化,首字母大写,其余小写)

#### format方法

`format`接受__位置参数__和__关键字参数__,替换字符串中的`replacement`字段

* 位置参数:根据{}出现的顺序传入参数

```python
>>> {0} love {1}.{2}.format('I','baidu','com')
O:I love baidu.com
    
```

```python
>>> {a} love {b}.{c}.format('I','baidu','com')
O:I love baidu.com
```

* 关键字参数:根据{}中的关键字传参

  ```python
  >>> {a} love {b}.{c}.format(a='I',b='baidu',c='com')
  O:I love baidu.com
  ```

注: 

1. _关键字参数和位置数不能混用_
2. 想要打印{,}只要连续打两个即可

##### %(百分号)格式化

1. 使用方法:在__字符串中__使用百分号格式化, 然后用%连接需要格式化的内容
```python
>>> '%c' % 97
'a'
```

2. 格式化表格(类似于printf)

   | 格式   | 对象                                               |
   | ------ | -------------------------------------------------- |
   | %c     | 将ascii转化成字符                                  |
   | %s     | 字符串                                             |
   | %d     | 格式化整数                                         |
   | %o     | 无符号八进制数                                     |
   | %x/X   | 无符号十六进制数(大小写)                           |
   | %f/e/E | 格式化定点数,f可指定`精度`;e是`科学计数法`(大小写) |
   | %g     | 根据值的大小选择%f或%E                             |
   |        |                                                    |
   |        |                                                    |

3. 格式化操作辅助命令(__在%和字母之间加的东西__)
| 辅助命令 |                                          |
| -------- | ---------------------------------------- |
| m.n      | m是宽度,n是精度                          |
| -        | 左对齐                                   |
| +        | 在正数前显示+                            |
| #        | 在八进制前显示`0`;<br />在16进制显示`0x` |
| 0        | 用0取代宽度空格(在前面填,后面不填)       |
|          |                                          |
|          |                                          |
|          |                                          |
|          |                                          |

4. 转义字符:传统异能

#### format和%格式化结合

在format的大括号中,可以使用`{索引:格式化符}`格式将参数格式化.__(待补充)__

---------------

## 二.内嵌函数和闭包

- 内嵌函数:python中允许__函数定义__的嵌套,作用域在上一层函数之内

### 闭包(closure):

1. 定义:一种__编程范式__.如果内嵌函数对外部作用域(但不是全局作用域)的变量进行引用,这个内嵌函数称为`闭包`
2. `nonlocal`关键字:在内嵌函数中使用nonlocal声明一个变量,指定这个变量不会是这个作用域内的局部变量

## 三.集合(set)

用`{}`括起的__iterable__类型

1. 不支持索引

2. 值唯一,不会重复

3. set可以强制转换

4. 有iterable们的特性

### 1. 部分方法

add(values) 加入

remove(value) 删除

### 2. 不可变集合(frozenset)

不可以改变的set,即没有add和remove方法

## 四.文件

### 1.open参数

![image-20200205150257681](C:\Users\carrzhou\AppData\Roaming\Typora\typora-user-images\image-20200205150257681.png)

### 2. 文件对象方法

文件对象是iter大家族的一部分,每一行是一个元素,故用for和list可以打印出内容

| 方法                | 操作                                                         |
| ------------------- | ------------------------------------------------------------ |
| f.close()           | 关闭文件                                                     |
| f.read(size=-1)     | 读取size个字符,若size为负度去所有字符,会移动__光标__         |
| f.tell()            | 返回光标的位置                                               |
| f.seek(offset,from) | 移动光标,从`from`<br />(0表示__文件开始__,<br />1表示当前位置,<br />2表示__文件末尾__)向右偏移offset个字节 |
| f.readline()        | 读一行                                                       |
| list(f)             | 从光标读到末尾,返回列表                                      |
| f.write(str)        | 写入,返回写入字符数                                          |
| f.writeline()       | 写入字符串序列,可迭代,每个字符串占一行                       |
|                     |                                                              |
|                     |                                                              |

## 五.OS(操作系统)模块

| 方法                 | 作用                             |
| -------------------- | -------------------------------- |
| getcwd()             | 返回工作路径字符串               |
| chdir(path)          | 改变工作路径                     |
| listdir(path)        | 显示路径下的文件和文件夹         |
| mkdir(path+文件夹名) | 创建单层目录(文件夹)(存在则异常) |
| makedirs(res_path)   | 创建多层目录(存在则异常)         |
| remove(path)         | 删除文件                         |
| rmdir(path)          | 删除文件夹(目录)                 |
| removedirs(path)     | 删除多层目录                     |
| rename(old,new)      | 重命名文件                       |
| system(command)      | 运行系统shell命令                |

2. 属性

   | 属性    | 值                     |
   | ------- | ---------------------- |
   | curdir  | 当前目录('.')          |
   | pardir  | 上一层目录('..')       |
   | sep     | 路径分隔符             |
   | linesep | 当前平台行终止符       |
   | name    | 当前使用的操作系统平台 |
   |         |                        |

   ### 2. os.path模块

   是只一个对`路径字符串`操作的模块
   
   ![image-20200206204952410](C:\Users\carrzhou\AppData\Roaming\Typora\typora-user-images\image-20200206204952410.png)

## 六.pickle(泡菜)模块

__amazing__的模块,将任何类型转换成二进制模式文件,一般这样的文件用`pkl`后缀

方法和`json`差不多,但是二进制[^1]的

而json存的是字符串

| 方法              | 作用    |
| ----------------- | ------- |
| dump(pkl文件,str) | 存      |
| load(pkl文件)     | 取,返回 |

[^1]:打开时加b

## 七.异常处理(try)

### 1. except

```python
try:
	代码块
except 错误种类 as 异常变量:
    代码块
finally:
    必须执行的代码
```

* 异常变量存储错误的原因,类型是对应的`error类`
* 众所周知,每个error都是一个\_\_builtins\_\_的类

### 2. raise

```python
#利用raise引发异常
>>> raise ZeroDivisionError('love')
Traceback (most recent call last):
  File "<pyshell#19>", line 1, in <module>
    raise ZeroDivisionError('love')
ZeroDivisionError: love
```

### 3. 异常处理和循环的else 

1. 循环后的else:

* 当且仅当在break之后,循环后的else代码块才不执行

2. 异常处理的else:
   * 在except后,若except未执行,则执行else

## 八.GUI(用户图形界面)编程

本节用到了`easygui`模块

不建议在shell上运行

~~请自己学~~

|                |                 |
| -------------- | --------------- |
| msgbox(str)    | 显示有str的窗口 |
| choicebox(str) |                 |
|                |                 |
|                |                 |
|                |                 |
|                |                 |
|                |                 |
|                |                 |
|                |                 |

## 九. 类

### 1. name mangling(名字重造技术)

* 在python类中定义__私有变量/方法__的方式是在变量名或方法名前加上___两个下划线___
  * 其本质是把属性改名成了`_类名__变量名`,其实在外面可以根据这个名字访问到(伪私有)

### 2. 继承

```python
class 子类(基类):
    pass
```

子类可以继承父类的所有属性和方法.因私有是伪私有,故也可以继承.

* 但子类重写父类方法会覆盖父类方法
* 父类可以作为子类的\_\_init\_\_参数
* 可以继承多个父类(多重继承)
* 所有类都继承于`object`基类

#### 覆盖

> 解决1:可以在子类新方法中调用父类的旧方法
>
> 解决2:super().父类方法
> #super方法自动寻找基类

### 3.类对象

__类对象__是代表类的一个python对象,类必须有实例对象才能调用方法,但类对象不能调用方法.在一定要调用的是

```sql
class CC:
	...
#CC是一个类对象,可以用del去除,删除后(实例对象)内存中的方法和属性不会失效(静态)
CC.__dict__ #类对象的__dict__属性返回一个属性字典
#但实例对象不会这样
```

### 4.类的BIF(内置函数)

| 函数                           | 返回                                       |
| ------------------------------ | ------------------------------------------ |
| issubclass(类对象A,类对象B)    | 若A是B的子类返回True                       |
| isinstance(实例对象,类对象)    | 若实例对象是类对象的实例返回True           |
| hasattr(实例对象,属性名)       | 对象有指定属性(字符串)则返回True           |
| getattr(object,name[,defalut]) | (name为字符串)返回属性值,没有则返回default |
| setattr(object,name,value)     | name为str,设置属性值,没有就异常            |
| delattr(object,name)           | 去掉属性,没有就异常                        |
|                                |                                            |
|                                |                                            |
|                                |                                            |

#### property函数(*)

* 使用方法:

```python
#在类中使用
x=property(get,set,del)
```

`get`:获得某属性值得方法

`set`:改变某属性值的方法

`del`:删除属性的方法

* 作用:

  这样property的返回值就可以承担这三样工作

  ```python
  	#假设存在类的实例对象c
      c.x #调用get方法,返回属性值
      c.x=1 #调用set方法,改变属性值
      del c.x #调用del 删除属性
  ```

  

## 九点五. 类的魔法方法

### 1. 构造和析构

`__init__(self,[,...])`

`__new__(cls,[,...])`两个都是构造函数

`__del__(self)`析构函数,所有引用都被del时才会调用

### 2. 工厂函数 和对象的运算

* 所谓的`工厂函数`即int,float 这种可以强制转换的类对象

  所以方法中加类似`__str__`这种可以完成强制转换,print时str隐式调用repr

  `__repr__`(self):对象打印时返回的字符串

* 运算符运算表

  | 魔法方法                   | 含义                  |
  | -------------------------- | --------------------- |
  | `__add__`(self,other)      | 加法+                 |
  | `__sub__`(self,other)      | 减法-                 |
  | `__mul__`(self,other)      | 乘法*                 |
  | `__truediv__`(self,other)  | 除法/                 |
  | `__floordiv__`(self,other) | 整除//                |
  | `__mod__`(self,other)      | 取模%                 |
  | `__divmod__`(self,other)   | 被divmode()调用的行为 |
  | `__pow__`(self,other)      | pow()调用或**运算     |
  | `__lshift__`(self,other)   | 定义左移<<            |
  | `__rshift__`(self,other)   | 定义右移>>            |
  | `__and__`(self,other)      | 按位与&               |
  | `__or__`(self,other)       | 按位或\|              |
  | `__xor__`(self,other)      | 按位异或^             |

  * divmod(a,b),返回商和余数

* __反运算__

  在魔法方法的字母前加个`r`,能实现别的类主动的反运算

* __增量赋值运算__(例如+=)

  在魔法方法的字母前加个`i`,能实现增量赋值

* __一元操作__

| 方法               | 代表       |
| ------------------ | ---------- |
| `__neg__`(self)    | +x         |
| `__pos__`(self)    | -x         |
| `__abs__`(self)    | abs()      |
| `__invert__`(self) | 按位求反~x |



### 3. 属性访问魔法方法

name是固定关键词

| 方法                           | 作用                                |
| ------------------------------ | ----------------------------------- |
| `__getattr__(self,name)`       | 定义getattr失败时的行为(属性不存在) |
| `__getattribute__`(self,name)  | 定义类属性被访问时的行为            |
| `__setattr__`(self,name,value) | 定义当一个属性被设置的时候的行为    |
| `__delattr__`(self,name)       | 定义删除时的行为                    |

* 不能再setattr中使用赋值,否则会无限递归,可以使用`super().__setattr__(name,value)`赋值,也可以使用

  `self.__dict__[name]=value`赋值

### 4. 描述符

__描述符__是将某种==特殊类型==,能将类的实例指派给另一个类的属性.

* 描述符的__本质__即特殊类型:

  至少实现以下三个方法之即特殊类型一

  	1. `__get__(self,instance,owner)`访问属性,返回其值
   	2. `__set__(self,instance,value)`属性分配操作中调用,不返回任何内容
   	3. `__delete__(self,instance)`控制删除操作,不返回任何内容

* 举例:

  ```python
  #描述符类
  class MyDecriptor:
      def __get__(self,instance,owner):
          print('get')
      def __set__(self,instance,value):
          print('set')
      def __delete(self,instance):
          print('del')
  #指派给另一个类的属性  
  class Test:
      x=MyDecriptor()
      
  >>> test.x
  get
  >>> test.x=1
  set
  ```

* `property`:

  是一个__描述符类__,传入get,set,del在对应的方法内调用.然后在别的类中,其实例便成了一个属性

### 5. 容器类型

* __协议__:
  1. 希望定制的容器__不可变__,只需要定义`__len__`和`__getitem__`方法
  2. 希望可变则_还需要_`__setitem__`和`__delitem__`

* getitem(self,item)自动对应下标,赋值对应set

* __迭代器__:

   迭代器的BIF:

  1. `iter()`,将变量传入返回迭代器类型的变量
  2. `next()`,取得下一个元素

  魔法方法:`__iter__`,`__next__`在迭代器类中    实现以指定对应行为

## 十.生成器

### 1.yield生成器

在函数里出现`yield`时该函数称为一个特殊的__迭代器__:生成器.

为协同程序提供了可能

* 举例:

  ```sql
  def libs():
  	yield 1
      yield 2
      
  for each in libs():
  	print(each)
  >>> 1
  >>> 2
  ```

### 2. for生成器

[i*2 for  i in ...]

## 十一.模块包的创立

1. 创建文件夹

2. 在文件夹中创建`__init__.py`模块文件

3. 将模块放进文件夹中

   导入:import 包.模块

## 十二 爬虫

### 1.访问互联网

`urllib`包,有`request`,`error`,`parse`,`robotparser`组成

* url由三部分组成:
  * 协议:如http
  * 存放资源的服务器的域名或ip地址,有时需要端口号
  * 资源的具体地址,如目录或文件名

* 打开网页:

  response = urllib.request.urlopen(url)

  |      |      |
  | ---- | ---- |
  |      |      |
  |      |      |
  |      |      |
  |      |      |
  |      |      |
  |      |      |
  |      |      |
  |      |      |
  |      |      |

  ## 十二.正则表达式
  
  `re`模块,通配符
  
  |             |                          |
  | ----------- | ------------------------ |
  | search(a,b) | 返回a在b第一次出现的时候 |
  | .           | 任意字符                 |
  | \d          | 任意数字                 |
  | [xyz]       | 任意择一匹配             |
  | x{3,2}      | 某字符重复次数           |
  | [1-20]      | 匹配1到20的数            |
  |             |                          |
  |             |                          |
  |             |                          |
  
  