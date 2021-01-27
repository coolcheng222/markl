# TypeScript

## 一. 介绍



### TypeScript 的介绍

### 1. 介绍

> TS是JS的超集

TypeScript是一种由微软开发的开源、跨平台的编程语言。它是JavaScript的超集，最终会被编译为JavaScript代码。

2012年10月，微软发布了首个公开版本的TypeScript，2013年6月19日，在经历了一个预览版之后微软正式发布了正式版TypeScript

TypeScript的作者是安德斯·海尔斯伯格，C#的首席架构师。它是开源和跨平台的编程语言。

TypeScript扩展了JavaScript的语法，所以任何现有的JavaScript程序可以运行在TypeScript环境中。

TypeScript是为大型应用的开发而设计，并且可以编译为JavaScript。

TypeScript 是 JavaScript 的一个超集，主要提供了类型系统和对 ES6+ 的支持**，它由 Microsoft 开发，代码开源于 GitHub 上

* TS一般需要编译形成JS才能在浏览器使用,可以编译出纯净简介的js代码,包括Node.js
* TS建立在ES6之上
* TS包含了类型系统等先进特性

### 2. 安装

```java
npm install -g typescript
```

检查

```java
tsc -v
```

### 3. 编写

文件名后缀`.ts`

编译: `tsc 文件名`

如果ts文件中直接书写js语法,则html可以直接引入

如果ts文件有了ts代码则需要先编译成js再引入

### 4. 自动编译

1. 生成tsconfig.json,在文件夹的目录下使用`tsc -init`
2. 修改json中的outDir输出目录,不用严格模式(看喜好)

## 二. 简单语法demo

### 1.类型注解

轻量级的为函数或者变量添加的约束

```javascript
function showMsg(str:string){
   return "aaa" + str;
}
let msg = [10,20,30];
showMsg(msg);//报错,主要是提示信息,但是不影响它编译
```

> Error:(6, 13) TS2345: Argument of type 'number[]' is not assignable to parameter of type 'string'.

### 2. 接口

主要是一种约束(规范),跟类型注解搭配使用

若使用的变量没有满足约束就会编译报错

```typescript
interface IPerson{
    firstName:string
    lastName:string
}
function showFullName(person:IPerson){
    return person.firstName + "_" + person.lastName;//确保这里不会出错
}
```

### 3. 类

```javascript

class Person{
        firstName:string
        lastName:string
        fullName:string

        constructor(firstName:string,lastName:string) {
            this.firstName = firstName;
            this.lastName = lastName;
            this.fullName = firstName + "_" + this.lastName;
        }
    }
```

## 三. 类型

### 0. 字面量作为类型

```javascript
let a : 10; //a就是10
let b : "a" | "b"; //b从两个值里选
```



### 1. js自带的基础类型

```javascript
let a:boolean = true;//布尔
let n:number = 0x10;//0b111,0o11117,12345
let name:string = "aaa";
let name2 : string | boolean; // 联合类型
```

期待它隐式转换,就报错;比如赋值之类的

* null类型和undefined类型

  * 可以作为所有类型的子类型(可以给别的对象赋值,非严格模式下)

  * 值固定

    ```javascript
    let nu:null = null;
    ```

    

### 2. 数组和元组

* 声明数组(固定类型)

  ```javascript
  //方式1: let 变量: 数据类型[] = [...];
  let arr1:number[] = [10,20,30];
  //方式2: 泛型写法
  let arr2: Array<number> = [1,2,3];
  ```

* 元组: 让"数组"中有不同的类型且固定长度

  ```javascript
  let arr3:[string,number,boolean] = ['',1,true];//严格对应
  ```

### 3. 枚举

```javascript
enum Color{
    red,
    green,
    blue = 10,
}
let color:Color = Color.red;//实际是number 0,后面一个一个递增
Color(10);//返回"blue";
```

### 4. any/unknown和void和never

any类型: 可以存任何类型,可作为数组的元素

void: 空,可以接受null,undefined

never: 不可以返回任何值,连空都不可以,可以在函数里单纯抛异常啥的

unknown: 能接受任何类型,但是比any更严格,不能给已知类型的变量赋值;在类型检查typeof后才可以赋值

### 5. object类型

对象

```javascript
let a:object;//当然,这样没什么意义
let b:{name:string};//钦定有一个name属性,且只能有name属性
let c:{name:string,age?:number} // 钦定有name属性.age可以没有
```

* 部分要求

  ```javascript
  let d:{name:string,[propName:string]:any};//propName:string表示属性名应该是string,其他不管
  //即只要有name,其他属性不管
  ```

* 限制函数结构

  ```javascript
  ;let e1:Function;
  let e:(a:number,b:number)=>number; //限定函数对象参数和返回值的类型和数量
  ```

  

### 6. 类型断言/泛型

把unknown断言为指定类型

```javascript
s = e as string;
```

泛型

```javascript
s = <string>e;	
```

### 7. 类型或/类型与

```typescript
let e:string|number;//string或number
let j:{name:string}&{age:int} //两者都要满足
```

### 8. 类型别名

```typescript
type myType = 1|2|3|4;//后面用myType就行
```

