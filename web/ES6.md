# ECMAScript6

## 一. 介绍

ECMA是欧洲计算机制造商协会,ECMAScript是一个脚本程序设计语言,根据ECMA-262标准

ES6是变动最大的版本

## 二. let和const关键字

### 1. let

声明就和var差不多

```javascript
let a;
let b,c,d;
let e = 100;
let f = 123, g = 234
```

* 特性:

  * 同名变量重复声明会报错(var声明相同变量不会)

  * 块作用域: 只在代码块中有效

    ```javascript
    {
        let a = 1;
    }
    console.log(a); // 报错
    ```

    ```javascript
    //对比
    console.log(a);//undefined,变量提升
    {
        var a = 1;
    }
    console.log(a);//1
    ```

  * 不存在变量提升(在声明之前使用,var变量会返回undefined,称为变量提升)

  * 不影响作用域链的效果(块里的块也能用它)

* 小示例:

  ```javascript
  for(var i = 0;i < items.length ;i++){
           items[i].onclick = function(){
           //修改背景颜色
           this.style.background = 'pink';
           //在这里是不能用i的,因为全局变量i在onclick执行的时候就已经是最大值了
      }
  }
  ```

  ```javascript
  for(let i = 0;i < items.length ;i++){
             items[i].onclick = function(){
             //修改背景颜色
             items[i].style.background = 'pink'; // 块作用域,值不被全局控制
        }
  }
  ```

  

### 2. const

声明语法和let,var一样

* 语法特点:
  * 必须声明时初始化(不然报错)
  * 一般常量名使用大写
  * 常量能修改
  * 块作用域
  * 可以对常量中的元素或者属性修改

## 三. 变量解构赋值

### 1. 语法

> 声明时不限定var,let或者const

* 数组解构

```javascript
const F4 = ['x','y','z','s'];
let [a,b,c,d] = F4;
console.log(a);//x
console.log(b);//y
console.log(c);//z
console.log(d);//s
```

* 对象解构

```javascript
const zhao = {
    name:"zhao",
    age: 100,
    gender:'M',
    xiaopin:function(){
        console.log("aaaa");
    }
}
let {name,age,xiaopin} = zhao;
//变量名和属性要对应上
```

## 四. 模板字符串

使用**反引号**括起的新字符串声明方式

```javascript
let str = `aaa`;
console.log(str);
```

* 特性:

  * 内容中可以直接出现换行符(编辑时可以直接换行)

    ```java
    let str = `
    <ul>
         <li>玛丽</li>
         <li>玛丽</li>
    </ul>`;
    ```

  * 可以进行变量拼接(使用`${}`嵌入)

    ```javascript
    
    let lov = 'aaa';
    let out = `${lov}tql`;
    console.log(out);
    ```

    

## 五. 简化对象

```javascript

let name = 's';
let change = function(){
      console.log('haha');
}
const school = {
    name,//name:name
    change,//change:change
    
    //方法简化:
    improve: function(){
    	console.log('...');
    },
    //简化后:
    improve(){
        console.log('...');
    }
}
```

## 六. 箭头函数

### 1. 简单声明和调用

```javascript
//声明
let fn = (a,b) =>{
   return a + b;
}
//调用
let result = fn(1,2);
```

### 2. 特性

1. __this是静态的__; 函数始终指向声明时所在作用域下的this的值(无论谁调用都不会变)

   ```javascript
    function getName(){
        console.log(this.name);//this会改变
    }
    let getName2 = () =>{
        console.log(this.name);//此this永远是window
    }
    window.name = "aaa";
    const school = {
        name:"bbbb"
    }
   
    getName.call(school);//bbb
    getName2.call(school);//aaa
   ```
   
2. 不能作为构造函数

3. 没有argments变量

### 3. 拓展简写

1. 当形参有且只有一个,可以省略小括号

   ```javascript
   let add = n =>{
       console.log(n)
   }
   ```

2. 只有一条语句的时候,省略大括号,并且语句的结果作为返回值

   ```javascript
   let add = n => console.log(n)
   ```

### 4. 应用案例

1.

```javascript
//需求1 点击div 2s后颜色变pink
 let ad = document.getElementById("ad");//获取div
 ad.addEventListener("click",function(){//点击事件
     setTimeout(() => {
         //注意,这里如果是function()则this指向window,因为调用环境是window
         //然而声明时这个this指向外层的this指向ad,所以用()=>可以解决this的归属问题
         this.style.background = "pink";
     },2000);
 })
```

### 5. 应用总结

1. 适合与this无关的回调,比如定时器,数组
2. 不适合与this的回调设置

## 七. 函数参数

### 1. 参数默认值

```javascript
function add(a,b,c=10){
    
}
```

有默认值的函数参数一般放在最后(没有严格要求)

可以和解构赋值结合,解构中的参数也可以赋值

```javascript
let a = {
    host:"aaa",
    name:"bbb"
}
function connect({host="127.0.0.1",name}){//对象解构传参
    console.log(name);
    console.log(host);
}
connect(a);
function connect2({host,name} = a){//对象解构默认值
    
}
```

### 2. rest参数

rest旨在取代arguments

* rest参数的获取: `...参数名`,放在**参数列表最后**

  ```javascript
  function connect({host,name} = a,...rest){
       console.log(rest);//是个数组,可以用数组的方法
  }
  ```

  


## 八. 拓展运算符

拓展运算符`...`可以将数组拆解成逗号分隔的形式

```javascript

const aaa = [1,2,3];
chunwan(...aaa);//三个参数

function chunwan(){
    console.log(arguments);
}
```

> 区别于rest参数,这个是用在数组前面的运算符

### 2. 应用

* 数组拼接

  ```javascript
  var a = [1,2,3];
  var b = [4,5,6];
  var c = [...a,...b];//[1,2,3,4,5,6,];
  ```

* 数组深拷贝

  ```javascript
  const d = [...a];
  ```

* 伪数组转换为真数组

  ```javascript
  var divs = document.getElementsByTagName("div");
  var divArr = [...divs];
  ```

  