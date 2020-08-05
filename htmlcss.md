> [TOC]

# 前端

## 一. 绪论

### 1. 软件架构

* `C/S`架构: 客户端/服务端
  * 特点:
    *  使用前必须安装; 
    * 更新时客户端和服务端同时更新;
    *  不能跨平台使用
  * CS通信采用__自有协议__,比较安全
    * 协议相当于密码本
* `B/S`架构:  浏览器/服务器
  * 本质上也是C/S,只不过B/S使用浏览器作为客户端
  * 使用浏览器访问网页的方式,来使用软件
  * 特点:
    * 不需要安装,直接使用浏览器访问网址
    * 软件更新时客户端不需要更新
    * 跨平台使用,只需要浏览器
  * BS客户端服务器通信才用的通用`Http`协议,相对不安全,在http上加一层协议,`Https`相对安全

### 2. 开发流程

1. 网页设计师根据需求设计网页(用ps)
2. 前端工程师将设计做成仅该网页(html5)
3. 后台工程师将静态网页修改为动态网页(JSP)

### 3. 学啥

* __W3C标准__
  * ==结构== : HTML
  * ==表现== : CSS(页面元素的样式)
  * ==行为== : JavaScript(响应用户操作)



# HTML

## 一. 绪论

### 1. 是什么

`HTML`负责__结构__,全名叫`Hypertext Markup Language`(超文本标记语言)

使用__标签__的形式来标识网页的不同组成部分

文件以`html`后缀

* __基本架构/标准格式__:

  ```html
  <html>
      <head>
          <title>好网页</title> 
      </head>
     
      <body>
          <h1>这是我的第一个网页</h1> 
      </body>
  </html>
  
  <!--html称为根标签,一个页面有且只有一个-->
  <!--
  head 和 body是html的子标签
  两者是兄弟标签
  -->
  ```

  * head标签内容不会在网页中显示,是给浏览器看的
  * body是网页的主题

### 2. 注释

```html
<!--
	我是多行注释
	哈哈
-->
```

## 二. 标签(/元素)

### 1. 标签的属性

可以通过属性来设置标签如何处理标签中的内容

可以在开始标签里添加.

每一个标签都有`id`属性唯一的标识标签



```html
<html>
    <head>
        <title>
            好网页
        </title>
    </head>
    <body>
        <h1>第<font color="red">一个</font>网页</h1>
    </body>
</html>
```

这里font后面的color就是属性,再开始标签中以"KV对"方式赋值.

* 属性值必须加引号
* 查看有啥属性,去`W3School离线手册`
* 多个属性用空格隔开

### 2. doctype文档说明

写在html头部,注明html版本,现在市面上有`html4.01`,`xhtml`,`html5`

前两个的内容可以复制

h5就简单了

```html
<!doctype html>
<!--我是h5-->
```

如果不写doctype可能会导致一些浏览器进入怪异模式,导致页面无法正常显示.

### 3. meta告知编码集

浏览器在我们这基本都是gb2312的,用utf8会乱码.

写在<head>体内部

```html
<meta charset="utf-8" /> 
<!-- meta是自结束标签,一个标签,/表示结束-->
```

### 4. 常用标签及注意事项

html关心的是标签的语义,而不是表现

| 标签             | 作用                                |
| ---------------- | ----------------------------------- |
| <h1>,<h2>...<h6> | n级标题                             |
| <p>              | 段落标签,表示一个自然段(段间有距离) |
| <br /\>          | 自结束标签,表示换行                 |
| <hr /\>          | 自结束标签,水平分割线               |
| **图片标签**     |                                     |
| <img /\>         | 自结束,引入外部图片                 |
|                  |                                     |
|                  |                                     |
|                  |                                     |

* 对于搜索引擎来说,`h1`的重要性仅次于title,一个页面最好只有一个
* html中,换行和多个空格会当成一个空格解析



### 5. 实体(转义字符)

在html中 <,>,空格不太能正常使用(如果 < 中间 > 开头用数字,那不认为是标签)

需要转义,这个转义字符它是这样的:

```html
&实体名字;

<h1> &lt; </h1> #小于号
<h2> &gt; </h2> #大于号
<h3> &nbsp; </h3> #实体空格
<h4> &copy; </h4> #版权符号

<!--如你所想,le,ge之类的都能用 
	这东西必须写在一行列
具体可以去w3c手册看
-->
```

如果需要打unicode字符,应该这样

```html
&#十进制编码;
```



### 6. img图片标签详解

```html
<img src="路径" alt="描述"\>
<!-- alt在不能显示图片的时候会显示,主要用于搜索引擎,如果不写就不会被搜索到-->
<!-- 
其他属性:
	width="200px" 以px为单位,只设置一个则宽高比不变,一般划不来,找美工裁图好了
	hight="100px"
-->
```

* 补充:图片格式

  * `JPEG(JPG)`:
    * 支持颜色比较多,图片支持压缩
    * 不支持透明背景
    * 一般使用jpeg保存照片等颜色丰富图片
  * `GIF`:
    * 支持颜色少,支持简单(直线的)的透明,支持动态图
    * 颜色单一或者动态图使用GIF
  * `PNG`:
    * 支持颜色多,支持复杂的透明
    * 可以用来显示复杂的透明图片

  格式选择原则: 效果不一致,使用<u>效果好</u>的; 效果一致,使用<u>小</u>的

### 7. meta标签详解

meta标签可以设置字符集,还可以设置网页关键字,指定网页描述等

* 可以给name属性指明`"keywords"值`,表示content为关键词,关键词之间逗号隔开

* 可以给name属性指明`"description"值`,表示content为描述,是完整的一句话

```html
<head>
    <meta charset="utf-8" /> 
    <title>实体</title>

    <meta name="keywords" content="hemlsss,javam,aaa" />
    <meta name="description" content="zhehiwangye">
    <!--给搜索引擎看的,不会影响在搜索引擎的排名-->
    <meta http-equiv="refresh";content>
</head>
```
* 还可以用meta做请求的重定向

```html
<meta http-equiv="refresh" content="5;url=http://www.baidu.com" />
```

5秒后跳转百度,认识一下代码就好



### 8. 音视频元素

`audio`:audio的属性大多不需要赋值

```html
<audio src="./source/ww.mp3" controls autoplay loop></audio>
<!--
	controls: 由用户控制
	autoplay: 自动播放,不太支持
	loop: 单曲循环
	ie8不支持,可以在标签内用<source src="">指定,并且并列写者请换浏览器
-->
```

`vedio`用来视频

## 三. xHtml语法规范/内联框架

1. html不区分大小写,一般使用小写
2. 注释不能嵌套
3. 标签必须结构完整,要么成对要么自结束
4. 标签可以嵌套,但不能交叉嵌套
5. 标签中属性必须有值且值必须加引号

### 0. 内联框架

使用连框架引入外部页面,使用`iframe`框架,不推荐使用

内联框架内容不会被搜索引擎检索

```html
<iframe></iframe>
<!-- 
	属性
	src:指向外部页面的路径,可相对 
	width:宽
	hight:高
	name:制定一个name属性
-->
```

### 1. 超链接

链接到其他页面,标签为`<a>`

```html
<a href="http://www.baidu.com">我是超链接</a><br/>
        <a href="http://www.baiduaaaaaaaaaaaaaaaaaaaa.com">我是超链接</a><br/>
        <a href="sixth.html">我也是超链接</a>
        <a href="fifth.html" target="_self">haha</a>
        <a href="fifth.html" target="_blank">haha</a>
        <iframe src="fifth.html" name="tom"></iframe>
        <a href="http://www.baidu.com" target="tom">haha</a>
        <!--
            href指向连接跳转的地址,可相对路径,如果不确定可以用#占位(#有回到顶部的功能)
            target设置新页面的位置
                _self当前窗口,默认值
                _blank新标签
                "内联框架名" 在内联框架打开
        -->
```
```html
如果想要跳转到页面的某处,可以利用标签的id
<!--id在一个页面中不能重复,不能数字开头,每个标签都有id-->
<a href="#bottom">区底部</a>

<a id="bottom" href="#">回到顶部</a>
```

```html
自动打开邮件客户端:href="mailto:目标邮箱"
```

### 3. 元素关系

嵌套的标签称为父子关系

* 父元素: __直接__包含子元素的元素
* 子元素: 直接被父元素包含的元素
* 祖先/后代元素: 直接或间接包含(被包含)元素
* 兄弟元素: 拥有同一个父元素叫兄弟元素

## 四. 块元素,内联元素

~~h5没有这个概念了其实.~~

### 1. 块元素

**块元素**即会独占一行的元素,无论内容有多少,包括p,div,h1...都是块元素.

<div style="background-color:red">我是一个div</div><div style="background-color:yellow">我是一个div</div>

`div`是一个__块元素__,没有任何语义,就是单纯一个块元素,没有任何默认样式.

div的作用是对页面布局(页面分成一块一块就是div的功劳)

### 2. 内联元素

__内联元素(行内元素)__,只占自身大小的元素,不会占用一行,包括a,img,iframe等都是

`span`标签是一个内联元素,没有任何语义.可以把没有归属的文字放进span,进行管理/设置样式

内联元素主要用来选中文本设置样式

### 3. 联系

一般只会用块元素包含内联元素,而不会使用内联元素包含块元素

<u>a超链接可以包含任意元素,不能包含a</u>

<u>p段落不能包含其他块元素</u>

## 五. 文本标签

### 1. 常用文本标签

|              |                                                     |
| ------------ | --------------------------------------------------- |
| `strong`     | : __粗体__,表示"内容上的抢到"(内容的重要性)         |
| `em`         | : _斜体_,表示"语气上的强调"(内容中的着重点).        |
| <i>          | <u>斜体,</u>没有语义                                |
| <b>          | <u>加粗,</u>没有语义                                |
| <small>      | 文字比父元素文字小(可以越来越小),表示细则一类的内容 |
| <big>        | 文字比较大,没有语义,少用                            |
| <cite>       | <u>斜体</u>,表示参考的内容,比如书名,歌名,话剧名     |
| <q>          | 短引用(行内),会默认加上引号                         |
| <blockquote> | 长引用(块元素),缩进独占一行,相当于md的>             |
| <sup>        | 上标^哈哈^                                          |
| <sub>        | 下标<sub>e</sub>                                    |
| <ins>        | 表示插入的内容<ins>e</ins>,下划线                   |
| <del>        | 表示删除的内容~~h~~,删除线                          |
| <pre>        | 预格式标签,把代码原格式保存                         |
| <code>       | 专门表示代码的,不会保留格式,结合pre使用             |
|              |                                                     |
|              |                                                     |
|              |                                                     |
|              |                                                     |

>  <em>我是em</em>
>
> <strong>woshi</strong>
>
> <blockquote>blockquote
> </blockquote>
>
> <q>q</q>
>
> <p>赵薇<sup><a href="#">[1]<a></sup></p>

### 2. 列表标签

列表相当于购物清单,列表之间可以互相嵌套

#### 2.1 无序列表

`ul`标签创建无序列表,`li`创建列表项

都是__块元素__

* 这是示例

  -------

   <ul style="background-color:red">
        <li>aaa</li>
        <li>bbb</li>
        <li>
            ccc
        </li>
    </ul>

------------------------

* 通过type可以改变引用符号: disc/square/circle,__不推荐使用__

* 使用css去掉列表符号:

* ```css
  ul{
      list-style:none;
  }
  ```

#### 2.2 有序列表

用`ol`代替`ul`,里面还是`li`



> 设置type改变排序标志
>
> 用阿拉伯数字排序
>
> 1. 
>
> type="a/A" 采用小写或大写字母作为序号
>
> type="i/I"采用小写或大写罗马数字

```
<ol>
    <li>结构</li>
    <li>表现</li>
    <li>行为</li>
</ol>
```

#### 2.3 定义列表

用来对一些词汇和内容进行定义

使用`dl`创建,配合`dt`,`dd`

> dl : 定义列表
>
> dt: 被定义的内容,一个dl可以多个
>
> dd: 对定义内容描述,可以多个

* 示例:

  <dl style="background-color:red">
      <dt>萝卜</dt>
      <dd>兔子最爱</dd>
      <dt>兔子</dt>
     <dd>最爱萝卜</dd>
  </dl>

  ```html
  <dl style="background-color:red">
      <dt>萝卜</dt>
      <dd>兔子最爱</dd>
      <dt>兔子</dt>
     <dd>最爱萝卜</dd>
  </dl>
  ```

  \


## 六. 补充

### 1. 布局标签

在body里标志页面布局,可用可不用,新增的东西不一定兼容

只有语义的作用

```html
<header>头部</header>
<main>主题</main>
<footer>尾部</footer>

<nav>导航栏</nav> 
<aside>和主体相关的其他内容</aside>
<article>文章</article>
```

## 七. 表格

表格用于表示一些格式化的数据

### 1. table标签+tr+td

* table : 表格
* tr: 一行
* td: 一行中的一列
  * 叠起来写就是多行多列
  * 设置border="1"显示边框,但这本应是css做的

```html
<table border="1">
        <tr>
            <td>oo</td><td>oo</td><td>oo</td>
        </tr>
        <tr>
            <td>oo</td><td>oo</td><td>oo</td>
        </tr>
        <tr>
            <td>oo</td><td>oo</td><td>oo</td>
        </tr>
    </table>
```

<table border="1">
        <tr>
            <td>oo</td><td>oo</td><td>oo</td>
        </tr>
        <tr>
            <td>oo</td><td>oo</td><td>oo</td>
        </tr>
        <tr>
            <td>oo</td><td colspan="2">oo</td><td>oo</td>
        </tr>
    </table>

### 2. 合并单元格

在`td`中设置`colspan`属性表示横向合并几个格子

在`td`中设置`rowspan`属性表示纵向合并几个格子

### 3. 表头th

使用th标签标示表头的内容,用法和td一样,会有默认效果

### 4. 长表格

元组很多的表格叫长表格,可分成三部分看: 表头,表格主题,底部(比如合计)

html提供了三个标签对应这三个部分`thead`,`tbody`,`tfoot`来区分表格不同部分,应该成为table的子标签

tr写在这些标签当中.

> 注意: tbody是必然存在的,没写tbody默认会把所有不在头尾的tr放进tbody

* 好处:
  * 可以单独设置样式
  * 可以每次打印都打出头和尾
  * 对三者的顺序不要求,比如甚至可以把foot写到第一个,不影响他显示在表格尾

### 5. 古代用法: 布局

现在用div+css布局了

## 八. 表单

表单是用来__提交信息给服务器__的

### 1. 创建表单

* 写__form标签__并指定`action`属性,提交表单时会提交到action对应地址(跳转到)

```html
<form action="target.html">

</form>
```

* 使用表单项__input自结束标签__:

  * 如果希望表单项数据提交到服务器,必须给input指定name属性
  * 给submit按钮设置value属性,指定上面的字
  * 给input指定value属性,指定默认值

  ```html
  <form action="target.html">
          用户名: <input type="text" name="user" />
      	密码: <input type="password" name="password"/>
          <input type="submit" value="你好"/>
      </form>
  ```

  <form action="target.html" style="border:yellow 1px solid">
          用户名: <input type="text" /><br/>
      密码: <input type="password" name="password"/>
          <input type="submit" value="你好"/>
      </form>

* 点击提交,所输入信息会出现在url的**查询字符串**中,<small>target.html?user=ohhh</small>

### 2. 表单项

表单项类型用`type属性`区分

#### 2.1 表单项表格

记得把表单项写在form里

| 属性值                                     | 含义                    |
| ------------------------------------------ | ----------------------- |
| radio                                      | 单选框                  |
| checkbox                                   | 多选框                  |
| 下拉表: 非input系列; 多行文本框<textarea\> |                         |
| reset                                      | 重置,把form里的全都掀了 |
| button                                     | 按钮,跟js一起用         |
| <button></button>标签                      | 按钮                    |
|                                            |                         |
|                                            |                         |
|                                            |                         |

#### 2.2 单选/多选详解

<u>不需要填写的表单项值默认为on,需要用value影响</u>

radio为单选,需要相同的name属性才能指定为多项单选,值为value属性值

```html
性别:<input type="radio" name="gender" value="M"/>男
        <input type="radio" name="gender" value="F"/>女
```

<form action="target.html" style="border: red 1px solid">
        性别:<input type="radio" name="gender" value="M"/>男
        <input type="radio" name="gender" value="F"/>女
        <input type="submit" value="aaa"/>
    </form>

checkbox为多选,跟单选一样,最后值以多个键值对体现在url中

#### 2.3 下拉表

使用`<select>`创建下拉表,用`<option>`指定选项

```html
<select name="star" id="">
            <option value="bing">fanbingbing</option>
            <option value="ben">ben</option>
            <option value="ru">ru</option>
 </select>
```

<select name="star" id="">
            <option value="bing">fanbingbing</option>
            <option value="ben">ben</option>
            <option value="ru">ru</option>
 </select>

* 可以用optgroup对option们分组,并制定label标签指定组名

  ```html
  <select name="star" id="">
              <optgroup label="F">
                  <option value="bing">fanbingbing</option>
              </optgroup>
              <optgroup label="M">
                  <option value="ben">ben</option>
                  <option value="ru">ru</option>
              </optgroup>
  
          </select>
  ```

  

#### 2.4 默认选中选项

单选/多选中 可以在希望选中的选项中添加属性`checked="checked"`表示默认选择

下拉框中 可以用`selected="selected"`在option中,或者`multiple="multiple"`在select中,表示多选下拉列表

##### 2.5 多行文本域

```html
<textarea name = "aaaa"></textarea>
```

<textarea></textarea>

#### 2.6 按钮button标签

比input的系列更灵活,不是自结束

```html
        <button type="submit">熬</button> <!--提交-->
        <button type="reset">锕</button>  <!--重置-->
        <button type="button">阿</button> <!--无用-->
```

#### 2.7 label标签

可以把表单标签在label里,专门css,也可以在里面加for属性值为表单项id,把两者关联

```html
<label for="um">用户名</label>
<input id="um" type="text" name="username" />
```

#### 2.8 fieldset

在表单里用fieldset来为表单分组,

```html
<fieldset>
    <legend>我是组名</legend>
    <input type="text" value="a"/>
</fieldset>
```



## 九. 框架集

也是用来引入其他外部页面的,和内联框架一样

frameset不能和body放在一个页面中.

```html
<frameset rows(cols)="50%,50%"><!--页面一行(列)一行(列)的出现,占50%-->
	<frame src="01.html" />
    <frame src="02.html" />
</frameset>
```



# CSS(上)

## 一. 绪论

### 1. 简介

`CSS`名为__层叠样式表(Cascading Style Sheets)__,用来为网页创建样式表,通过样式表对网页进行修饰

所谓层叠,可以将网页想象成一层一层的结构,高层次覆盖低层次.Css可以对各个层次设计样式.

### 2. 写在哪:

#### 2. 内部样式表

* 可以把CSS样式表编写到__元素的style属性__当中
  * 称作__内联样式__,只对当前元素有效,不适合复用
  * 高耦合,不推荐使用

```html
<body>
        <p style="color:red;font-size:20px">锄禾日当午,汗滴禾下土</p>
        
</body>
```


* 把CSS样式写到`head`的`style标签`里
  * \<style type="text/css">(头标签固定格式)
  * 在标签内容里需要使用CSS选择器


```html
    <meta charset="utf-8" /> 
    <title>CSS</title>
    <style type="text/css">
        p{color:red;}
    </style><!--p{}为指定<p>标签的选择器-->

</head>
```
#### 2.2 外部样式表

可以用在不同的页面的样式表.编写到外部的`.css文件`,使结构表现分离

可以利用缓存加快用户访问速度

```css
p{
    color:red;
    font-size:20px;
}
文件内容和style标签内容一样
```

应用到html页面,通过`<link>`自结束标签,在head中

```html
<head>

    <meta charset="utf-8" /> 
    <title>CSS</title>
    <link rel='stylesheet' type='text/css' href="style.css"/>
	<!--前面的标签固定,最后一个写css文件路径-->
</head>
```

### 3. 语法

style标签或者css文件中遵循css语法.

#### 3.1 注释

同C语言的多行注释

```html

<style type="text/css">
            /*haha*/
            p{
                color:"red";
            }
    </style>
```

### 3.2 语法

`选择器 声明块`

* 选择器: 通过选择器选中页面中指定元素,把声明块应用到选择器应用元素
* 声明块: 紧挨在选择器后面,使用一对{}括起,内容是一组一组名值对(称为__声明__),分号隔开声明
* 声明: `属性:值`,值不加引号



## 二. 选择器

通过选择器选择页面中所有指定元素

### 1. 语法

语法: 标签名{}

#### 1.1 元素选择器/类选择器/id选择器

```css
p{} /*通过标签名*/
#tom{} /*#+id属性值 用id选择指定标签*/

<p id="tom"></p>
```

```css
如果需要多个标签共用一个,可以指定标签的class属性为同一个,称为一组元素
<p class="p2">锄禾日当午</p>

.p2{
            color:blue;
}/*语法为 .class{}*/
```

* 注意: 一个标签的class属性可以设置多个,Css只要认中其中一个就起作用

  多个class属性用空格隔开,可以使多个样式表同时作用这个标签(冲突则覆盖)

  ```html
  <p class="p2 hello">锄禾日当午</p>
  ```

#### 1.2 选择器分组

为id为p1的元素,class为p2的元素,还有h1同时设置背景元素为黄色

使用**选择器分组(并集选择器)**同时选中多个选择器对应元素

```css
选择器1,选择器2,...{}

#p1,.p2,h1{
            color:yellow;
        }
```

#### 1.3 通配选择器

选择页面中所有元素

```css
*{}
```

#### 1.4 复合选择器(交集选择器)

让拥有 `class p3`的`span`元素设置style

```css
语法: 选择器1选择器2选择器...{}/*紧挨着*/

```

不建议对id选择器使用

#### 1.5 后代元素选择器

```css
祖先元素/#id/.class 后代元素/#id/.class{
    
}

       div span{
            color:red;
        
}
/*可以一直空格下去直到最后一个后代*/
```

#### 1.6 子元素选择器

```css
/*只找子元素*/
div > span{

}
/*可以和空格,>连着使用*/
```



#### 1.7 伪类选择器

![image-20200615114648124](C:\Users\carrzhou\AppData\Roaming\Typora\typora-user-images\image-20200615114648124.png)

访问过的链接和没访问过的链接,我想设置不同的颜色

使用__伪类__,表示元素一种特殊状态.比如: 访问过的超链接,普通的超链接,获取焦点的文本框

* 四个伪类选择器表示链接状态:
  * `a:link`: 正常链接
  * `a:visited`:访问过的链接(由浏览器历史记录判断)
    * 由于隐私问题,visited伪类只能设置color
  * `a:hover`:鼠标划过的链接
  * `a:active`:正在点击的链接
    * hover和active也可以是别的元素的伪类

```css
a:link{
            color:red;
}
a:visited{
            color:yellow;
}
```

* 其他伪类:

  * `:focus`: 获取焦点状态(比如获取文本框光标)

    ```css
    input:focus{
        background-color: red;
    }
    ```

  * `::selection`:选中的元素

    ```css
    p::selection{
        background-color:orange;
    }
    
    p::-moz-selection{/*火狐专用*/
    }
    ```

#### 1.8 伪元素

表示元素中特殊的位置

`:first-letter`: 表示元素中第一个字母

```css
p:first-letter{
}
```

`:first-line`:元素中第一行

`:before`,`:after`表示标签最前面的部分(默认什么也没有)需要结合content样式一起使用.

```css
p:before{
    content:"haha";
}
```

#### 1.9 属性选择器

对很多属性可用,以title属性为例

`title属性`: title属性可给任何标签指定.当鼠标移动到元素上时,title的值作为提示文字显示.

* 为所有有title属性的元素设置一个背景颜色

```html

   <style type="text/css">
        p[title]{
            background-color: red;
        }/*选取含有指定属性的元素*/
    </style>
```

* 为所有title属性为"Hello"设置颜色

```css
        p[title="Hello"]{
            background-color: yellow;
        }
```

* 为title属性值为"ab"开头的元素设置背景颜色(^=)

```css
p[title^="ab"]{
            background-color: wheat;
}
```

* 为title属性值以ab结尾($=)

  ```css
  p[title$="ab"]{
              background-color: wheat;
  }
  ```

* title属性值含有ab(*=)

  ```css
  p[title*="ab"]{
              background-color: wheat;
  }
  ```

  

#### 1.10 子元素伪类选择器

| 选择器         |                                   |
| -------------- | --------------------------------- |
| :first-child   | 第一个子标签                      |
| :last-child    | 最后一个子标签                    |
| :nth-child     | 指定第几个子标签                  |
| :first-of-type | 当前子元素中,是这个种类的第一个   |
| :last-of-type  | 当前子元素中,是这个种类的最后一个 |
| :nth-of-type   | 指定                              |

```css
p:first-child{/*含义:既得是p元素,也是父元素的第一个子元素
    last child同理*/
}
:first-child{/*跟写 *:fc 一样,不指定必须是什么元素*/
    
}

```

* **nth-child:**	

  ```css
  /*
  在选择器后面加()指定选中第几个子元素
  */
  p:nth-child(1){
      /*第一个元素,且为p标签*/
  }
  /*
  可以传进特殊值,如even(偶数),odd(奇数)
  */
  p:nth-child(even){}
      
  ```

  

#### 1.11 兄弟元素选择器

`+`号选择一个元素紧挨着的元素

```css
span+p{
    /*
   	选择span后紧挨着的元素p (兄弟)
    */
}

```

`~`号选中后边所有指定元素

```css
span~p{
    /*
    选择span后面所有p元素(兄弟元素)
    * /
}
    
```

#### 1.12 否定伪类

`:not(内容)`

```css
        p:not(.hello){
            background: #235d89;
        }
```

### 2. 样式的继承

像儿子可以继承父亲遗产一样,CSS中,祖先元素的样式也__会__被后代元素继承

利用继承可以将一些基本样式继承给后代元素

```CSS
   <p style="font-size:20px">
/*内外文字都变成20px*/
        我是p标签文字
        <span>
            我是span中文字
        </span>
    </p>
```

* 并不是所有样式都会被继承:
  * 比如背景颜色(背景相关的样式+边框相关+定位相关)

### 3. 选择器优先级

当使用不同选择器选中同一个元素时,并且设置相同的样式.样式之间产生冲突.由__选择器优先级(权重)__决定

* __规则__:

  > **内联样式**: 优先级 1000
  >
  > * ```html
  >   <p style="...">
  >       <!--这个叫内联样式-->
  >   ```
  >
  > **id选择器**: 优先级100
  >
  > **类和伪类**: 优先级10
  >
  > **元素选择器**: 优先级1
  >
  > **通配***: 优先级0
  >
  > **继承样式:** 没有优先级
  * 如果有多重选择器,将选择器优先级相加并比较
    * 但是选择器优先级计算不会超过他的数量级(10个10最大99)
  * 优先级相同,使用靠后的样式
  * 并集选择器优先级各选择器单独计算
  * 可以在样式后面加`!important`把优先级拔到最高,尽量避免使用

### 4. 伪类的顺序

涉及到a的伪类有四个: link,visited,hover,active

* active和hover会同时出现,伪类优先级相同,用靠后的 
* 如果把link和visited放在下面,则一定不会出现active和hover

所以顺序最好是:

> link visited hover active

## 三. 文本格式化

### 1. 长度单位

* `px`像素:
  * 像素是在网页中使用最多的单位,一个像素相当于屏幕的一个小点
  * 不同显示器一个像素大小也不同,越小越清晰
* `%`百分比 : 
  * 可以将单位设置成百分比形式,浏览器将根据其__父元素__的样式__百分比__计算该值
  * 适用于自适应界面
  

<center><div class="box" style="width:100px;background-color:red">
    a
    <div class="box1" style="width:50%;background-color:yellow">
        b
    </div>
    </div></center>

```html
<div class="box" style="width:100px;background-color:red">
    a
    <div class="box1" style="width:50%;background-color:yellow">
        b
    </div>
</div>
```

* `em`
  * 和百分比类似,相对当前元素**字体大小**计算
  * ==1em \=\= 1font-size==
  * 设置字体相关样式会使用
* `rem`
  * 相对于root(html)字体大小

### 2. 颜色单位

在CSS中可以直接用颜色单词表示不同颜色,比如red,blue,green,可以在手册里找"颜色名"

也可以通过`RGB`值表示不同颜色,通过red,green,blue光学三原色,用三种颜色的浓度表示不同颜色

> __注意__: 光学三原色最大是白色,没有是黑色

* 部分**语法**:

  ```css
  rgb(红色浓度,绿色浓度,蓝色浓度);
  rgb(255,0,0);
  /*颜色浓度用0-255表示,0表示没有,255表示最大*/
  
  rgb(100%,0%,0%);
  /*也可以用百分数表示 0%-100%*/
  ```

* 十六进制RGB

  * 使用三组两位的16进制数字,两位就表示0-255
  * ==#红色绿色蓝色==
  * 如果像`#ff0000`这种两位两位重复的可以简写`#f00`

### 3. 字体系列

#### 3.1. 字体样式1

* 表格版

| 样式            | 含义                                                         |
| --------------- | ------------------------------------------------------------ |
| __color__       | 字体颜色,前景色                                              |
| **font-size**   | 默认16px,设置字符所在格的高度,一般字符比格小(字体相关)       |
| __font-family__ | 指定字体,值可以是宋体之类的,如果不支持字符就按照默认<br/>使用计算机中字体,有就用,没有就不用,尽量不用奇怪字体<br/>字体都保存在C://windows//fonts里 |
|                 | 可以用多个字体,用逗号分开,浏览器优先用前面的,没有就用后面的  |

```css
.p1{
	color:red;/*前景色*/

	font-size: 20px;/*默认16px
每个文字都是处在框中,设置的是框的高度
            */
	
    font-family:华文彩云,微软雅黑,arial;
    /*指定文字字体,如果不适用字符用默认值
     可以同时指定多个字体,优先用第一个,没有用后面的
     字体都保存在C://windows//fonts里
     */
}
```

#### 3.2. 字体分类

> 大类:可以将字体(font-family)设置成大类
>
> * **serif**衬线字体
> * **sans-serif**非衬线字体
> * **monospace**等宽字体: 编程爱用
> * **cursive**草书字体
> * **fantasy**虚幻字体

当字体设置为大类以后,**浏览器会自动选择**指定字体并应用样式

一般把大类作为最后一个字体,保底用.

#### 3.3 字体样式2

|                  |                                                              |
| ---------------- | ------------------------------------------------------------ |
| __font-style__   | 字体风格,默认为normal,有继承性<br>normal: 标准样式<br>italic:斜体<br>oblique: 倾斜,一般和倾斜效果一样<br>inherit:从父元素继承字体样式 |
| __font-weight__  | 字体粗细,默认为normal,值400<br>bold:粗体,值700 <br>bolder: 更粗(没用)<br>lighter:更细(没用)<br>整百值(没有这么多级别字体,用不上) |
| __font-variant__ | 规定是否以小型大写字母显示,默认值normal<br>small-caps: 小型大写字母显示,把小写变小型大写 |
|                  |                                                              |

#### 3.4 字体样式3 font

在CSS中还有样式`font`,使用该样式可以设置和字体(font)相关的所有样式.

* 举例

* ```css
  .p3{
      font:italic small-caps bold 50px "华文彩云" ;
  }
  ```

* 注意:
  * 设置字体样式时<u>,斜体,加粗,小型大写</u>没有顺序,可不写(用默认值)
  * 要求__文字大小和字体__必须写,而且__字体必须最后一个,大小倒数第二__

### 4. 行间距

CSS中不能直接设置行间距,可以通过设置`行高line-height`来间接设置行间距

```css

.p1{
            line-height:40px;
        }
```

行高类似于单线本两个线之间的距离.

* 行高如果用`%`为单位,那就**相较于字体大小**的百分数
* 也可以用**正整数**,是字体大小相应**倍数**

<u>行间距=行高 - 字体大小</u>,可以为负(重叠)

> 网页中的文字实际上是在两个看不见的线中,文本在两线之间垂直居中显示

* 小应用:

  * 在单行文字中,设置行高与父元素一致可以达到垂直居中显示的效果

  <div id="p2" style="width:200px;
              height:200px;
              background-color: #bfa;
              line-height:200px">
          <a href = "#">
              我是超链接
          </a>
      </div>

  * `font`样式也可以指定行高,和字体放一起,如果不写会指定**默认值**并**覆盖已经定义的行高**

    ```css
    .p1{
        font: 30px/30px "微软雅黑"
    }
    ```

    

### 5. 文本样式

#### 5.1 text-transform

`text-transform`这个样式将元素中的字母全都变成大小写?

| 值         | 表现                          |
| ---------- | ----------------------------- |
| none       | 默认,标准文本                 |
| capitalize | 单词大写开头,通过边界识别单词 |
| uppercase  | 全都大写                      |
| lowercase  | 全都小写                      |

<p style="text-transform:capitalize;background-color:red">
        aaa AAA abC Def
    </p>

#### 5.2 text-decoration

`text-decoration`样式给文本添加各种修饰.其实就是加线

| 值           | 表现                             |
| ------------ | -------------------------------- |
| underline    | 下划线,超链接默认有,可以设置none |
| overline     | 上方线                           |
| line-through | 删除线                           |
| none         | 没有                             |

<p style="text-decoration:overline underline line-through">
        aaa AAA abC Def
    </p>

#### 5.3 spacing系列

* `letter-spacing`
* `word-spacing`: 单纯设置空格大小

值:

* px为单位的,百分数为单位的,正数(增加距离)负数(减少)

#### 5.4 text-align

`text-align`文本对齐方式

| 值      | 样式                      |
| ------- | ------------------------- |
| left    | 默认值,左对齐             |
| right   | 右对齐                    |
| center  | 居中                      |
| justify | 两端对齐,通过调整空格大小 |

#### 5.5 text-ident

`text-ident`设置首行缩进,一般用`2em`,用负值可以隐藏文字

## 四. 盒子(框)模型

`Box Model`.

**盒子:** CSS处理网页时认为**每个元素**都放在一个矩形的盒子里

对网页的布局相当于摆放盒子,摆放即可完成网页布局

### 1. 盒子的分划

一个盒子分为几个部分:

> 1. 内容区(content): **width和height就指定的是内容区大小**
> 2. 内边距(padding): 内容区和边框的距离
> 3. 边框(border):
> 4. 外边距(margin):盒子之间的举例

![image-20200624134055266](C:\Users\carrzhou\AppData\Roaming\Typora\typora-user-images\image-20200624134055266.png)

> ==注意==: 盒子的大小由内容区,边框,内边距共同决定

### 2. 边框border

* 如何设置边框?

  * 要为元素设置边框必须指定三个样式

    * `border-width` 边框厚度
    * `border-color` 边框颜色
    * `border-style` 边框样式

  * 示例:
  
  * ```
    .box1{
        /*
        使用width设置内容区宽度
        使用height设置内容区高度
        */
        width:100px;
        height:100px;
    
        background-color: #bfa;
    
        border-width: 5px;
        border-color: red;
        border-style: solid;
    }
    ```
<div style="width:100px;height:100px;background-color: #bfa;border-width: 5px;border-color: red;border-style: solid;"></div>

* 微操:分别指定四个边框的宽度/颜色/样式

  * ```css
    border-width: 10px 20px 30px 40px;
    /*
    如果borderwidth设置了4个值,则按 上右下左 顺时针赋值
    */
    border-width: 10px 20px 30px;
    /*
    如果3个值,上 左右 下 赋值
    */
    border-width: 10px 20px;
    /*
    如果2个值.上下 左右 赋值
    */
    ```

  * 这个规则对于颜色和样式也适用

* border样式大家庭

  * border-left-width,border-right-width,border-bottom-width,border-top-width
  * 分别设置四周的width
  * <u>对于color和style也有这个</u>

* `border-style`:

  | 值                   |          |
  | -------------------- | -------- |
  | solid                | 实线     |
  | none/hidden          | 没有边框 |
  | dashed               | 虚线     |
  | dotted               | 点状     |
  | double               | 双线     |
  | 还有3d样式的去手册找 |          |

* 简写border
  * 大部分浏览器边框的宽度和颜色都是有默认值,但style没有
    * 所以只指定样式可以出现边框

#### 2.1 border样式

* **border**

可以同时设置四个边框的样式,宽度,颜色

对于三者的顺序没有要求

但只能同时制定四个边框,不能微操

* **border-top,right,bottom,left**

  指定一个边的三个样式,没有顺序要求,是微操

* 如果不要一条边,可以将此边样式值设为none

  <div style="border:10px red solid;width:100px;height:100px;border-right:none"></div>

  

### 3. 内边距padding

内边距是盒子内容区和边框间的**距离**(长度单位),有四个方向有内边距对应四个样式

* **padding-top**
* **padding-bottom**
* **padding-left**
* **padding-right**

> 内边距会影响盒子可见框大小
>
> 背景颜色会影响到内边距和内容区

* padding设置上右下左,和border-width规则一样
  * padding: 10px 20px 30px 40px

### 4. 外边距margin

指的是盒子与其他盒子(包括父元素盒子)之间的距离__最小值__,在边框以外,会__影响盒子位置__

同样__有四个方向__

* **margin-top** : 上边框和其他盒子的举例
* **margin-bottom** :同理
* **margin-left**
* **margin-right**

> 由于们元素都是默认左上摆放,所以设置left或者top会**改变盒子自身位置**
>
> 而设置right和bottom会**改变其他盒子的位置**

* 外边距可以指定为__负值__,向反方向移动,会发生元素重叠
* 可以设置__auto__,一般只给水平方向设置,如果只设置left和right,会使值达到最大;同时设置,可以在父元素中居中

* 用margin可以同时设置四个方向,和上面border-width和padding一样

### 5. 垂直外边距重叠

在网页中**垂直方向**__相邻(很严格的元素相邻)__外边距会发生重叠:

​	兄弟元素之间的相邻**外边距会取最大值**而不是和

​	父子元素垂直外边距相邻,子元素外边距会对父元素外边距产生影响

​		加边框或内边距可以解决这个问题

### 6. 去掉浏览器默认样式

```css
*{
    margin: 0;
    padding: 0;
}
```

### 7. 内联元素的盒子模型

* 不能设置内容区宽高
* 可以设置水平内边距
* 可以设置垂直内边距,不会影响布局
* 可以设置边框,垂直方向不影响布局
* 水平外边距有效,但是没有相邻重叠问题,求和
* 不支持垂直外边距

## 五. display和visibility和overflow

### 1.display 

通过display样式可以指定元素为块元素或内联元素

| 值           |                                        |
| ------------ | -------------------------------------- |
| block        | 为块元素                               |
| inline       | 为内联元素                             |
| inline-block | 行内块元素,可以用块元素宽高,不独占一行 |
| none         | 此元素不会被显示                       |
| table        | 类似<table>效果                        |

```css
a{
 background-color: #bfa;
 width:500px;
 height:500px;
 display:block;
}
```

### 2. visibility

设置元素是否可见的样式,跟display none不同的地方在于,**它还占位置**

| 值      |        |
| ------- | ------ |
| visible | 可见   |
| hidden  | 不可见 |
|         |        |

### 3. overflow

子元素默认存在于父元素内容区中,但是它太大了的话会溢出父元素内容区.

如何设置滚动条或者溢出抛弃?用`overflow样式`设置溢出时行为

> overflow应在父元素样式中设置

| 值      |                                            |
| ------- | ------------------------------------------ |
| visible | 默认值,显示                                |
| hidden  | 隐藏溢出内容                               |
| scroll  | 会修剪,用滚动条查看,水平和垂直一定加滚动条 |
| auto    | 根据需求给水平或垂直加滚动条               |
|         |                                            |
|         |                                            |

## 六. 文档流

文档指的就是html文档,网页的页面

__文档流__就是网页的最底层,表示的是页面得位置,我们创建的元素都**默认**在文档流中

### 1. 元素在文档流中的特点

* __块元素__
  * 独占一行,自上向下排列
  * 默认宽度为父元素的100%,指定内边距不会影响可见框大小
  * 默认高度被子元素撑开
* 内联元素
  * 只占自身大小.自左向右排列
  * 如果不能容纳就换行继续自左向右
  * 宽度和高度都被内容撑开

### 2. 浮动float

`float`是一个定位__样式__,让元素从文档流里飘出来

* 如果想使用块元素在页面中水平排列,可以使块元素脱离文档流
* 脱离文档流后,相当于它从文档流里不见了,**其他元素会顶上来**
* 如果上一个元素是没浮起来的块元素,那浮动元素不会进入上一行(块元素那一行)
* 浮动的元素不会超过他的上边的兄弟元素,最多一边齐
* 浮动的元素不会盖住文字,文字会环绕在周围,可以做文字环绕效果
* 浮动以后内联和块差不多
  * 块元素浮动时宽高都被内容撑开
  * 内联元素浮动时可以指定宽高而不是固定内容撑开

* **值:** 
  * `none`: 默认,不脱离文档流
  * `left`: 脱离文档流向左上侧浮动,直到遇到父元素或者其他浮动元素边框
  * `right`: 脱离文档流向右上侧浮动,直到遇到父元素或者其他浮动元素边框

<div class="box1" style="width: 200px;
            height :200px;
            background-color: red;
            float: right;"></div>
<div class="box2" style="width: 200px;
            height :200px;
            background-color: yellow;"></div>

```css
.box1{
    /*display:inline-block;*/
    width: 200px;
    height :200px;
    background-color: red;
    float: right;
}
.box2{
    /*display:inline-block;*/
    width: 200px;
    height :200px;
    background-color: yellow;
}
```

### 3. 试着固定布局

```html

<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <style>
        *{
            padding: 0;
            margin: 0;
        }
        .header{
            width: 1000px;
            height: 140px;

            background-color: red;

            margin :0 auto;
        }
        .content{
            width: 1000px;
            height: 240px;

            background-color: orange;

            margin :10px auto;
        }
        .left{
            width: 200px;
            height: 100%;
            background-color: blue;
            float:left;
        }
        .middle{
            width: 580px;
            height: 100%;
            background-color: aquamarine;
            float:left;

            margin:0 10px;
        }
        .right{
            width: 200px;
            height: 100%;
            background-color: darkturquoise;
            float:right;
        }
        .footer{
            width: 1000px;
            height: 140px;

            background-color: mediumspringgreen;

            margin :0 auto;
        }
    </style>
</head>
<body>
    <div class="header"></div>
    <div class="content">
        <div class="left"></div>
        <div class="right"></div>
        <div class="middle"></div>
    </div>
    <div class="footer"></div>
</body>
</html>
```

### 4. 高度塌陷

在文档流中,父元素高度默认被子元素撑开,

但是子元素浮动以后,子元素脱离文档流,子元素无法撑起父元素,导致__高度塌陷__

父元素的兄弟元素会向前,造成布局混乱

* 解决方案零
  * 可以写好父元素高度,但不能自动适应子元素高度
* 解决方案一
  * 根据w3c标准,页面元素都有一个隐含的属性叫`Block Formatting Context(BFC块级格式化环境)`
  * 当开启BFC后,会有如下特性
    * 父元素垂直外边距不会和子元素重叠
    * 不会被浮动元素覆盖
    * 可以包含浮动的子元素
  * 如何开启BFC(n选一)
    * 设置元素浮动(不好)
    * 设置元素绝对定位(不好)
    * 设置元素为inline-block(不太好)
    * 将overflow设置非visible值(推荐)

![image-20200701133331788](E:\markd\pics\image-20200701133331788.png)

### 5. 让元素自身不受其他浮动影响

`clear`可以清除其他元素浮动对当前元素的影响

* 值:
  *  none 不清楚
  * left: 清除左侧浮动(float:left)元素对我元素的影响
  * right:右侧
  * both:不是都清除,是清除影响最大的浮动的影响

### 6. 用clear清除高度塌陷

直接在高度塌陷的父元素最后加一个div,并清除其附近浮动影响

虽然可以解决问题,但是会添加多余结构



通过:after伪类可以加一个东西代替多余div

```css
.box1:after{
   display:block;
    content:"";
    claer:both;
}
```



## 附: 做个导航栏

   <style>
        *{
            margin: 0;
            padding: 0;
        }
        li a:hover{
            background-color: red;
        }
    </style>
<div class="box1">
        <ul class="nav" id="nav" style=" list-style: none;
            background-color: blue;
            width: 100%;
            margin : 50px auto;
            overflow: auto;">
            <li class="ll" id="lili" style="float: left;
            width:25%;"><a href="#" style=" color: white;
            text-decoration-line:none ;
            display:block;
            width :100%;
            padding: 5px 0;
            font-weight:bold;
            text-align:center;">首页</a></li>
            <li class="ll" style="float: left;
            width:25%;"><a href="#"style=" color: white;
            text-decoration-line:none ;
            display:block;
            width :100%;
            padding: 5px 0;
            font-weight:bold;
            text-align:center;">新闻</a></li>
            <li class="ll" style="float: left;
            width:25%;"><a href="#"style=" color: white;
            text-decoration-line:none ;
            display:block;
            width :100%;
            padding: 5px 0;
            font-weight:bold;
            text-align:center;">联系</a></li>
            <li class="ll" style="float: left;
            width:25%;"><a href="#"style=" color: white;
            text-decoration-line:none ;
            display:block;
            width :100%;
            padding: 5px 0;
            font-weight:bold;
            text-align:center;">关于</a></li>
        </ul>
    </div>
```html
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>

    <style>
        *{
            margin: 0;
            padding: 0;
        }
        .nav{
            list-style: none;

            background-color: blue;
            width: 1000px;
            margin : 50px auto;
            overflow: auto;
        }
        .ll a{
            color: white;
            text-decoration-line:none ;
            display:block;
            width :100%;
            padding: 5px 0;
            font-weight:bold;

            text-align:center;
        }
        .ll a:hover{
            background-color: red;
        }
        .nav .ll{
            float: left;
            width:25%;

        }

    </style>

</head>
<body>
    <div class="box1">
        <ul class="nav">
            <li class="ll"><a href="#">首页</a></li>
            <li class="ll"><a href="#">新闻</a></li>
            <li class="ll"><a href="#">联系</a></li>
            <li class="ll"><a href="#">关于</a></li>

        </ul>
    </div>
</body>
</html>
```



## 附: 开班信息练习

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <style>
        * {
            margin: 0;
            padding: 0;

        }

        .box1{
            width: 100px;
            height: 100px;
            background-color: red;
        }
        body{
            font: 12px/1 宋体;
        }
        .outer{
            width:300px;
            /*height: 473px;*/
            margin: 20px auto;
        }
        .header{
            /*width: 300px;*/
            /*如果设置宽度,内边距会往外边挤,不指定会往内部挤*/
            height: 38px;

            /*margin: 50px auto 0;*/
            background-color: rgb(241, 241, 241);

            border-top: 2px rgb(5, 157, 131) solid;
            line-height: 38px;

            padding: 0 22px 0 16px;
        }
        ul{
            list-style:none;
            border-bottom: 1px #E4E3E0 dashed;
        }
        li{
            margin-bottom: 14px;
        }
        .content{
        /*width: 298px;*/

            /*height: 400px;*/
        /*margin:0 auto;*/

            border: 1px rgb(228, 228, 224) solid;
            padding:0 28px 0 20px;

        }
        .content h3{
            margin-top:14px;
            margin-bottom: 16px;
        }
        .content a{
            text-decoration:none;
            color: black;
            font-size:12;
        }
        .content .red{
            color:red;
            font-weight: bold;
        }
        .content .right{
            float:right;
        }
        .content .noborder{
            border: none;
        }
        .header h3{
            font-family: 微软雅黑,serif;
            font-weight:bold;
        }
        .header a{
            float:right;
            color:red;

            font-weight:bold;
        }
        .content a:hover{
            text-decoration: underline;
            color:blue;
        }

    </style>
</head>
<body>
    <div class="outer">
    <div class="header">
        <a href="#">16年面授开班计划</a>
        <h3>近期开班</h3>
    </div>
    <div class="content">
        <h3><a href="#">javaee+云计算-全程就业班</a></h3>
        <ul>
            <li>
                <a href="#" class="right"><span class="red">预约报名</span></a>
                <a href="#">开班时间: <span class="red">2016-04-27<span></a>
            </li>
            <li>
                <a href="#" class="right"><span class="red">预约报名</span></a>
                <a href="#">开班时间: <span class="red">2016-04-27<span></a>
            </li>
            <li>
                <a href="#" class="right"><span>开班盛况</span></a>
                <a href="#">开班时间: <span>2016-04-27</span></a>
            </li>
        </ul>
        <h3><a href="#">h5全称就业班</a></h3>
        <ul>
            <li>
                <a href="#" class="right"><span class="red">预约报名</span></a>
                <a href="#">开班时间: <span class="red">2016-04-27<span></a>
            </li>
            <li>
                <a href="#" class="right"><span class="red">预约报名</span></a>
                <a href="#">开班时间: <span class="red">2016-04-27<span></a>
            </li>
            <li>
                <a href="#" class="right"><span>开班盛况</span></a>
                <a href="#">开班时间: <span>2016-04-27</span></a>
            </li>
        </ul>
        <h3><a href="#">Android+人工智能全称就和版</a></h3>
        <ul class="noborder">
            <li>
                <a href="#" class="right"><span class="red">预约报名</span></a>
                <a href="#">开班时间: <span class="red">2016-04-27<span></a>
            </li>
            <li>
                <a href="#" class="right"><span class="red">预约报名</span></a>
                <a href="#">开班时间: <span class="red">2016-04-27<span></a>
            </li>
            <li>
                <a href="#" class="right"><span>开班盛况</span></a>
                <a href="#">开班时间: <span>2016-04-27</span></a>
            </li>
        </ul>

    </div>
    </div>
</body>
</html>
```



## 七. 定位

定位指的就是把指定元素摆放到任意位置

通过`position`样式来改变定位类型

__相对定位不会脱离文档流,绝对脱离会__

* 特性:

  * 不继承

* 值:

  * `static`: 默认值,不开启定位
  * `relative`: 开启相对定位
  * `absolute`: 开始绝对定位
  * `fixed`: 开启固定定位(绝对定位)

  只要position不是static,就可以通过`left(左侧偏移量)`,`right`,`top`,`bottom`设置元素的相对/绝对偏移量

### 1. 相对定位

* 使用: 指定定位类型,并设置偏移量

  * 

    ```css
    position: relative;
    left:100px;
    bottom : 100px;
    ```

  * 相对: relative相对原来位置的定位

  * 通常只需要定位两个(水平垂直)就能完成定位了

* 相对定位的特点:

  * 不脱离文档流
  * 会使元素提升一个层级

### 2. 绝对定位

* 特性:
  * 开启绝对定位会脱离文档流
  * 如果不设置偏移量,位置不会改变
  * 相对于__离它最近的开启了定位的祖先元素定位__
  * 一般都会给父元素设置相对定位
  * 定位使元素提升层级

### 3. 固定定位fixed

* 特点:
  * 脱离文档流
  * 相对于__窗口__的绝对定位
  * 会固定在浏览器位置,不会随着滚动条滚动

### 4. 元素层级

如果定位元素层级一样,结构上在下的盖住结构上在上的.

但是可以用`z-index`样式设置层级

* 值: 正整数,越大越优先显示,默认0
* __只能对开启定位的元素使用__
* 父元素不可能盖住子元素

### 5. opacity样式

通过`opacity`样式设置透明度,值为0-1之间的值,0最透明

对IE8以下没用

# CSS(下)

## 一. 背景

### 1. 相关样式

> background-image
>
> background-color
>
> background-repeat
>
> background-position
>
> background-attachment
>
> background

* 设置背景图像:

  * 使用`background-image`样式,值为url

    ```css
    background-image: url(img/aaa.jpg);
    ```

    

  * 如果图片大于元素,默认从左上角开始显示

  * 如果图片小于元素,图片会循环显示充满元素

    * 使用`background-repeat`设置是否重复显示
      * `repeat(默认)`,`no-repeat(不重复)`
      * `repeat-x`沿横向重复
      * `repeat-y`沿纵向重复

  * 背景颜色一定在图片后面,可以作为png背景的底色

### 2. 想给图像定位

`background-position`:给图像定位

* 值:

  * 默认值: `0% 0%`

  * 可选值1: top right left bottom center选两组合

    ```css
    background-position:top left;
    
    background-position:bottom right;
    ```

  * 可选值2: 百分数

    * 根据父元素的百分比指定两个值,第一个水平偏移量,第二个竖直偏移量

  * 可选值3: 像素

    * 根据父元素相对左上角,第一个水平,第二个竖直

`background-attachment`:设置背景图片是否和页面一起滚动

* 值:
  * scorll: 默认,跟着滚动
  * fixed: 不随着滚动,会使定位相对于浏览器窗口

### 3. 简写背景样式

显然我们五个样式太多了,我们用`background`

```css
/*background-color: #bfa;*/
/*background-image: url("img/active.png");*/
/*background-position: center center;*/
/*background-repeat: no-repeat;*/
/*background-attachment: fixed;*/
  background: #bfa url("img/active.png") center center no-repeat fixed;
```

* 没有顺序要求,样式可以不全写(用默认值)

## 二. 用超链接冒充按钮(超链接图片)

### 1. 初登场

平时用普通图,hover时换一张背景,点击时换一张背景

但是图片会加载,会造成不好的用户体验

```html
 #btn{
            width:93px;
            height: 29px;
            display:block;
            background-repeat: no-repeat;
            background-image:url("img/link.png");
        }
        #btn:hover{
            width:93px;
            height: 29px;
            display:block;
            background-repeat: no-repeat;
            background-image:url("img/hover.png");
        }
        #btn:active{
            width:93px;
            height: 29px;
            display:block;
            background-repeat: no-repeat;
            background-image:url("img/active.png");
        }
```

### 2. 改进(图片整合/CSS-sprite)

修改图片,把三张图放进同一张图片,并在不同行为时移动背景图片

```css
#btn{
            width:93px;
            height: 29px;
            display:block;
            background-repeat: no-repeat;
            background-image:url("img/btn.png");
        }
        #btn:hover{
            width:93px;
            height: 29px;
            display:block;
            background-repeat: no-repeat;
            background-image:url("img/btn.png");

            background-position: center;
        }
        #btn:active{
            width:93px;
            height: 29px;
            display:block;
            background-repeat: no-repeat;
            background-image:url("img/btn.png");
            background-position: right;
        }
```



### 附. CSS-sprite

<img src="pics/htmlcss/amazon-sprite_.png" alt="amazon-sprite_" style="zoom:33%;" />

这是一张亚马逊的雪碧图,如何用之?

用ps量,然后靠background-position偏移.



## 三. 表格的样式

[联动html表格元素](#七. 表格)

### 1. 基本样式/边框

table属于__块元素__,可以设置内容区的width和height,可以设置margin,border,padding

td也可以设置边框,就像这样![image-20200709133016009](pics/htmlcss/image-20200709133016009.png)

* table和td边框有个默认距离,在样式中叫`border-spacing`,可以在table的样式中设置为0
  * 对表格的所有边框有效
* 然后设置table样式`border-collapse`值为`collapse`
  * 好像只有border-spacing为0才有效
  * 对整个表格的边框都会合并

### 2. 花式表格背景

```css
tr:nth-child(odd){
    background-color: red;
}
tr:hover{
    background-color: #E4E3E0;
}
```

### 3. 用table影响相邻重叠外边距

使用table可以取消父子元素的相邻,使外边距不重叠(div做不到)

```css
div class="box1">
        <table></table>
        <div class="box2"></div>
    </div>
```

但是实际用法:使用before+display:table

```css
.box2:before{
	content:"";
    display:table;/*类似<table>的效果*/
}
```

## 附. clearfix(BFC)最终写法

综合上述说法,我们可以把高度塌陷问题和外边距重叠问题综合解决

```css
.clearfix:after,.clearfix:before{
    content: "";
    display: table;
    clear: both;
}
```

## 四. hack

某些代码只需要在某些特殊的浏览器中执行,可以用`CSS Hack`执行

指的是一段特殊的代码只能在某些浏览器上识别

### 1. 条件hack

```html
<!--[IF IE 6]>
<p>原理ie</p>
<![endif]-->

<!--[IF gt/lt/lte/gte IE 6]>
<p>原理ie</p>
<![endif]-->
```

### 2. 属性hack

css样式指定浏览器

```css
//样式前加符号避免
_: ie6及以下
*: ie7及以下
//样式后加符号
\9: ie6以上
\0: ie8及以上
```

# H5/CSS3补充

## 一. 图标字体

`iconfont`

在网页中会使用一些小型的图标,可以通过图片来引入,但是图片太大,不灵活

### 1. 前置知识

CSS中使用`@font-face`引入字体文件并给字体命名,就可以在网页中用了

```css
@font-face{
	font-family: "名字";
    src: 字体路径;
}
```

### 2. 使用下载的图标字体

先把css和webfonts黏贴到目录下,必须在同一目录下

将all.css引入网页

* 使用:
  * 用类名使用图标字体(类名固定,文档去查)

```html
<span class="fas fa-bell"></span>
```

![image-20200730140217103](pics/htmlcss/image-20200730140217103.png)