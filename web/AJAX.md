# AJAX

## 一. 是啥

### 1. 是啥

`AJAX`(Asynchronous Javascript And XML)`异步JS和XML`,是指一种创建交互式网页应用的网页开发技术

ajax是一种浏览器通过js**异步**发起请求,局部更新页面的技术

### 2. 交互

传统得交互方式: 由浏览器发出请求,服务器返回整个界面

Ajax交互方式:由JS的**XMLHttpRequest对象**发送请求,不惊动浏览器,简称`XHR`

> 异步: 通信之间没有任何优先级关系
>
> 同步: 上一次通信完成,下一次才可以继续
>
> 在代码中,如果发的是异步请求,那么发送请求后面的代码不需要等待请求完成再执行
>
> 如果是同步,则必须等待请求完成才能执行后面的代码



## 二. 使用

### 1. 创建XMLHttpRequest对象()

```javascript
var xhr = new XMLHttpRequest();
```

### 2. 用这个对象向服务器发送请求

调用XHR的`open`和`send`方法

```javascript
//规定请求的类型、URL 以及是否异步处理请求。
xhr.open("GET","AJAXServlet",true);//
//参数
//method = 请求类型,GET,POST
//url = 请求地址,可以带上请求参数
//async = 是否为异步请求(true为异步,默认为true)
//username,password已经不用了
```

```javascript
//发送请求
xhr.send(null);
//参数: 请求体,仅用于post请求(在tomcat记了什么叫请求体)
xmlhttp.send("fname=Bill&lname=Gates");
```

### 5. 用监听事件绑定

```html
<button onclick="ajaxRequest();">发送ajax请求</button>
```

然后把上面的内容和下面接收响应的内容放在函数里

### 4. 接收响应数据

如果想要获得来自服务器的相应,请使用XMLHttpRequest的`responseText`或`responseXML`属性

<span style="color:red">必须在响应完成接收</span>

**responseText**: 获得字符串形式的相应数据

**responseXML**: 获得XML形式的响应数据

#### 4.1 监控响应状态

* 什么时候知道响应完成接收

几个概念: 

xhr的`status`属性: 200或者404,响应状态码

xhr的`onreadystatechange`事件: 在readyState变化时触发

XHR的`readyState属性`: 

> 0: 请求未初始化
>
> 1: 服务器连接已经建立
>
> 2: 请求已经被接收
>
> 3: 请求处理中
>
> 4: 请求已完成

#### 4.2 接收响应

```javascript
xhr.onreadystatechange = function(){
			if(xhr.readyState == 4 && xhr.status == 200){
				alert(xhr.responseText);
			}
		}
```

### 5. 原生js用AJAX的问题

1. 兼容性差,创建XHR的方法多种多样,特别是IE5,6
2. 如果把async改成true,它监控不到onreadystatechange事件

## 三. jQuery使用

js发送ajax麻烦还有问题,不如用无敌的`jQurey`

### 1. get请求

```
返回值:XMLHttpRequest
```

```javascript
$.get(url,[data],[callback],[type])
//url 可以带参数,也可以用data带参数
//data: 可选的,参数
//callback: 回调函数,可以传参数,实参为返回的响应
//type: 响应数据是什么类型,默认为_default
	//值: xml(不用了),html,script,json,text,_default
	//xml和html: 接收对象为dom对象
	//json: 接收对象为js对象(json转变而成)
	//text: 文本
```

实例:

```javascript
//这data就是响应的内容,对应上面的xhr.responseText
$.get("AJAXServlet",function(data){
	alert(data);
});

$.get("test.php", { name: "John", time: "2pm" } );
//无论怎么传参,最终的参数都加在url上.因为是get请求
```

* __响应json字符串时__:

  * 将type设置为`"json"`可以直接接收**转换完的对象**

    ```javascript
    $.get("jqueryServlet?method=getUrl&t=" + Math.random(),function(data){
    	alert(data); //[object Object]
    	console.log(data);
    },"json");
    ```

  * 或者干脆用`$.getJSON`,就是不用写"json",其他都一样

### 2. post请求

```java
返回值:XMLHttpRequest
jQuery.post(url, [data], [callback], [type])
//这就是上面的用法
    //url上带着的数据照样传
    //data里的数据放在请求体
```

### 3. ajax请求

`$.ajax`是jquery中最底层的ajax请求方法,上面的\$.get和\$.post都只是对这个函数的包装

```javascript
$.ajax(url,[settings])
//用settings进行详细设置(url都可以写在配置项里,并省略)
```

* 使用的意义

  * get和post请求出现404之类的错误,不会有任何反应

* __settings__: 所有的选项都是可选的

  * 有用的属性: 

  | **属性**                                           | **作用**                                                     | **类型**                    | **默认值** |
  | -------------------------------------------------- | ------------------------------------------------------------ | --------------------------- | ---------- |
  | **type**                                           | 请求方式,                                                    | String(GET,POST,PUT,DE:ETE) | GET        |
  | **url**                                            | 请求地址                                                     | String                      | 当前页     |
  | __data__                                           | 发送到服务器的数据,将自动转换为请求字符串给事<br>GET加在URL后<br>如果为数组,jq自动帮你分成好几个 | Object,String               | 无         |
  | __async__                                          | 是否异步                                                     | Boolean                     | true(异步) |
  | __success(data, textStatus, jqXHR)__               | 请求成功后的回调函数,可以只用data接收                        | function/array              |            |
  | __error(XMLHttpRequest, textStatus, errorThrown)__ | 失败的回调函数,后面参数是错误信息和捕获的错误对象            | function                    |            |
  |                                                    |                                                              |                             |            |
  |                                                    |                                                              |                             |            |
  |                                                    |                                                              |                             |            |

  

* 实例:

  ```javascript
  $.ajax({
  	url:"jqueryServlet2?method=getUrlData&t=" + Math.random(),
  	type:"POST",
  	data:{"name":"高三","age":18},
      //data:"name=zhangsan&age=18,"
  	success:function(data){
  		alert(data);
  	},
  	error:function(){
  		alert("wrong");
  	}
  })
  ```

  

### 4. ajax请求提交表单(表单序列化)

返回值:String

`serialize()`
序列表表格内容为字符串。

```javascript
var forms = $("#forms").serialize();
alert(forms);
```

```javacript
data: $("#forms").serialize(),
```

