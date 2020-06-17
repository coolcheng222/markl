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

a超链接可以包含任意元素,不能包含a

p段落不能包含其他块元素



# CSS

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



## 二. 常用选择器

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

