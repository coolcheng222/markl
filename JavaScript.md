# JavaScript

> [TOC]

## 一. 绪论

### 1. 概述

`JavaScript`是由netScape率先开发的前段验证语言,现在用在各种网页行为.

`ECMAScript`是JavaScript的标准,一般认为与JavaScript等价,但不一样

![image-20200616135411315](C:\Users\carrzhou\AppData\Roaming\Typora\typora-user-images\image-20200616135411315.png)

JavaScript=ES+DOM(文档对象模块)+BOM(浏览器对象模块)

![image-20200616135541946](C:\Users\carrzhou\AppData\Roaming\Typora\typora-user-images\image-20200616135541946.png)

### 2. 特点

* 解释型语言
* 类似于C和java的语法结构
* 动态语言
* 基于原型的**面向对象**

## 二. HelloWorld

### 1. 往哪写

html中的某个标签,需要编写到`<script>`标签中

写在<head>

```html
<script type="text/javascript">

</script>
```

### 2. helloworld

```html
    <script type="text/javascript">
        alert("这是我第一行JS代码");
        /*
        * 我是注释
        *控制浏览器弹出一个警告框
        * */

        /*
        让计算机在页面中输出一个内容
         */
        document.write("快来");

        /*
        向控制台输出一个内容
         */
        console.log("haha");
    </script>
```

### 3. 编写的位置

* 可以写在标签的`onclick`属性中,点击按钮时执行

```html
<button onclick="alert('haha')">点我一下</button>
```

* 可以写在超链接的href中

  ```html
  <a href="javascript:alert('hahaha')">点点</a>
  <a href="javascript:;">点点</a><!--点击没有任何功能-->
  ```

* 以上耦合,不推荐,往script标签写

* 写一个js文件,用script标签引入,推荐

  ```html
  <script type="text/javascript" src="script.js"></script>
  ```

  * 一旦用于引入,不能再标签内编写,无用

### 4. 输入

`prompt("str")`函数输入,会给一个提示框输入内容,作为字符串返回值返回

## 三. 字面量和变量

字面量就是常量嘛,就不可变,在JS里可以直接使用

但是我们不能总是用字面量,所以需要用变量来使用它

### 1. 声明变量

```js
var a;//声明变量
a = 123;

var b = 132;
```

### 2. 标识符

可以含有字母,数字,`_`,`$`

不以数字开头,不能是关键字或保留字

* 变量/函数命名规范:
  * 驼峰命名法,首字母小写,后面单词首字母大写(跟java一样)

### 3. 数据类型

js中有六种数据类型,可以使用typeof检查变量类型:(返回的都是小写)

> ```javascript
> console.log(typeof b);
> ```
>
> 
>
> `String`: 字符串
>
> `Number`: 数值
>
> `Boolean`: 布尔
>
> `Null`: 空
>
> `Undefined`: 未定义
>
> `Object`: 对象

前五个属于基本数据类型,Object是引用数据类型

#### 3.1 字符串String

JS中字符串用引号引起,__双引号或单引号都可以__

#### 3.2 数值型Number

Number可以存的最大值为`Number.MAX_VALUE`: 1.7976e308

Number可以存的0以上最小值为`Number.MIN_VALUE`: 5e-324

比这个数字大的会返回`Infinity`

* 特殊数值型:(不能加双引号)
  * `Infinity`: 表示无穷大
  * `NaN`: 非法运算时,返回不是一个数字

#### 3.3 布尔型Boolean

只有真假两值`true` `false`

#### 3.4 空NULL

Null就一个值,`null`,表示空对象

* 用typeof返回的是一个__"object"__

#### 3.5 未定义Undefined

只有一个值,`undefined`,

声明变量但不赋值时类型为Undefined,其值为undefined

### 4. 强制类型转换

#### 4.1 其他数据类型-->String

* **方式一**: 调用被转换类型的`toString方法`,返回结果为字符串

  * 对于null和undefined无效

* ```javascript
  var a = 123;
  
  a = a.toString(); // 不改变原值
  
  console.log(typeof a);
  ```

* __方式二__: 调用`String函数`,传入被转换值为参数
  * 对于numeber和Boolean跟toString一样
  * 对null和undefined会直接转换为"null","undefined"

```javascript
var b = 345;
String(b);//大写S,指代类名

console.log(typeof b);//number
b = String(b);
console.log(typeof b);//string
```

#### 4.2 字符串/Boolean/null/undefined->number

使用`Number函数`

```javascript
var a = "123"
a = Number(a)
console.log(typeof a)
```

String情况:

* 纯数字字符串直接转换
* 全是空白转换为0
* 如果有非数字内容为NaN

Boolean情况:

* true-1
* false-0

null,undefined----0

#### 4.3 String-->Number(特殊)

> 这两个是函数不是方法,把最左边的数字读出来,读到非数字结束(整数读小数点也结束)

`parseInt(str)`把一个字符串转换为整数,可以传入第二个参数指定进制

```javascript
parseInt("070",10);//传入数字10,8,16
```



`parseFloat(str)`把一个字符串转换成浮点数

* 可以传入非String值,会转换成String(用String函数)并操作

#### 4.4 转换为boolean

`Boolean`函数,除了 0/NaN 和 空字符串 和 null 和 undefined 都是false

### 5. 运算符

| 运算符 | 返回类型 | 值                                         | 目   |
| ------ | -------- | ------------------------------------------ | ---- |
| typeof | string   | 值的类型                                   | 单目 |
| +-*/%  | number   | 算术结果,会进行Number转换,和Nan运算都是nan | 双目 |
| +      | string   | 类似java的拼串,有字符串就拼                | 双目 |
| +/-    | number   | 转换成number,并正负运算                    | 单目 |
| ++ ,-- | number   | 自增自减,前后版本都有                      | 单目 |

#### 5.1 . 非布尔值的逻辑运算

与运算找的是`false`,如果都是true就返回顺序的最后一个(找不到),如果找得到就返回第一个False

或运算同理

#### 5.2 相等运算

不同类型之间相等/不相等运算会发生自动类型转换,一般两边都转成number

但是null和0不相等,null和undefined相等,NaN不和任何相等

可以使用`isNaN函数`检查变量是否为NaN

### 6. 全等运算符(建议使用)

`===`叫全等运算符,判断是否相等,不会做类型转换

`!==`不全等,同理



## 四. 流程控制

### 1. 代码块

JS中的代码块:

* 没有Java里代码块的用法
* <u>在代码块(for,if等)里声明var变量,在外面也看的到(比较独特)</u>