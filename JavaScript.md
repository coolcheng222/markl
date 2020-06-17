# JavaScript

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
* 基于原型的面向对象

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

